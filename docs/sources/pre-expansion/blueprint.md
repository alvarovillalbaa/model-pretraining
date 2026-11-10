# Inherited model-training blueprint

Last updated: 2026-11-10

Historical design extracted from [Lab at ba67e51](https://github.com/alvarovillalbaa/lab/blob/ba67e510bab2703b1042993744ce099490221c1f/docs/specs/steps.md). The component specification owns current scope. This text does not prove implementation, current provider facts, or executed research.

# 5. Phase 1 — learn by building the smallest full system

Before scaling, build a toy trainer yourself.

## Goal

Train a **20M–60M decoder-only transformer** from scratch on a tiny corpus and serve it.

## Build manually

Implement:

1. tokenizer loading
2. packed dataset loader
3. transformer block
4. RoPE
5. RMSNorm
6. SwiGLU MLP
7. causal attention
8. AdamW
9. cosine or warmup-stable-decay schedule
10. checkpoint save/load
11. eval loss
12. text generation
13. simple chat wrapper
14. vLLM/Transformers export path if possible

This teaches you the mechanics. Then stop writing your own trainer. Move to OLMo-core/TorchTitan.

## Deliverable

A blog post: **“Training a tiny LLM from scratch: every tensor, every bug, every metric.”**

This is not for performance. It is for credibility and personal understanding.

---

# 9. Phase 5 — model architecture

Start with a dense decoder-only transformer. Do not start with MoE.

## Baseline architecture

Use a modern Llama-like architecture:

* decoder-only causal LM
* RMSNorm
* SwiGLU
* RoPE
* grouped-query attention for larger models
* no dropout or very low dropout
* tied or untied embeddings, but document the choice
* context length 2048 or 4096 for base pretraining
* long-context extension as a separate stage

## Suggested model ladder

| Name     | Params | Layers |    Hidden | Heads |   Seq len |
| -------- | -----: | -----: | --------: | ----: | --------: |
| lab-124m |  ~124M |     12 |       768 |    12 |      2048 |
| lab-350m |  ~350M |     24 |      1024 |    16 | 2048/4096 |
| lab-1.3b |  ~1.3B |     24 |      2048 | 16/32 |      4096 |
| lab-3b   |    ~3B |     32 | 2560/3072 |    32 |      4096 |
| lab-7b   |    ~7B |     32 |      4096 |    32 |      4096 |

Exact shapes should be chosen to fit your framework and GPU memory.

## Research architecture track

Only after the dense baseline works, test:

1. hybrid attention/RNN layers
2. sliding-window attention
3. sparse attention
4. MoE
5. modular expert post-training

Hybrid architectures are a serious research direction. Ai2’s Olmo Hybrid replaced many attention layers with Gated DeltaNet-style recurrent layers and reported large token-efficiency gains in controlled comparisons, so this is a credible place to explore improvements. ([allenai.org][13])

---


# 10. Phase 6 — pretraining ladder

## 10.1 Smoke run

Train 124M on 100M–500M tokens.

Success criteria:

* loss decreases smoothly
* no NaNs
* checkpoints resume
* eval job runs
* model can generate coherent-ish text
* vLLM/Transformers export path works

## 10.2 Mini scaling run

Train 124M and 350M models on multiple data mixes:

```text
124M x baseline_mix
124M x quality_mix
124M x reasoning_code_mix
350M x best_two_mixes
```

Track:

* validation loss by domain
* downstream evals
* cost
* tokens/sec
* instability events

Choose the final mix based on **eval-per-dollar**, not vibes.

## 10.3 1.3B run

Train ~1.3B on ~26B tokens.

Stages:

1. general pretraining
2. high-quality cooldown/midtraining
3. optional long-context extension
4. base eval
5. release base checkpoint

Do **not** post-train until the base model is evaluated and frozen.

## 10.4 3B run

Train only after the 1.3B pipeline is stable.

The 3B run is your first serious “I can train LLMs” proof. It forces you to solve throughput, memory, checkpointing, data quality, and evals without needing frontier money.

---


# 11. Phase 7 — pretraining configuration

Use this as your starting config, then tune from smaller runs.

| Hyperparameter     | Starting point                         |
| ------------------ | -------------------------------------- |
| optimizer          | AdamW                                  |
| betas              | 0.9, 0.95                              |
| weight decay       | 0.1                                    |
| grad clip          | 1.0                                    |
| precision          | bf16; FP8 only after baseline works    |
| LR schedule        | warmup + cosine or warmup-stable-decay |
| warmup             | 1–2% of total steps                    |
| sequence length    | 4096 for 1B+                           |
| global batch       | 0.5M–4M tokens                         |
| checkpoint         | every 500–2000 steps                   |
| eval               | every 250–1000 steps                   |
| heldout validation | fixed, versioned, never trained on     |

Stop the run immediately if:

* loss spikes and does not recover
* MFU is far below target
* dataloader stalls
* checkpoints cannot resume
* eval contamination is found
* validation loss diverges from train loss unusually early

---


# 12. Phase 8 — post-training

Post-training should be treated as multiple controlled stages, not one messy fine-tune.

## 12.1 SFT: supervised fine-tuning

Goal: make the base model instruction-following.

Data:

* permissively licensed instruction data
* your synthetic instruction data
* tool-use examples
* refusal/safety examples
* formatting examples

Rules:

* use a fixed chat template
* mask user/system tokens in loss
* keep validation split by source
* avoid training on benchmark answers
* evaluate every checkpoint

Deliverable:

```text
lab-1.3b-sft
lab-3b-sft
```

## 12.2 Preference optimization

Start with DPO before PPO-style RL. DPO is simpler, cheaper, and easier to debug.

Data:

* chosen/rejected pairs
* teacher-ranked outputs
* human-labeled pairs where possible
* self-generated pairs filtered by judges/verifiers

Algorithms:

* DPO
* IPO/KTO/ORPO as ablations
* GRPO for verifiable reasoning tasks

TRL has trainers for post-training and distillation, including DPO/GRPO-style workflows; its GRPO documentation describes the online loop of generating completions, computing rewards, estimating KL, and updating the model. ([Hugging Face][6])

## 12.3 RL with verifiable rewards

Use RL only where rewards are objective:

| Domain                | Reward                               |
| --------------------- | ------------------------------------ |
| math                  | exact answer / symbolic checker      |
| code                  | unit tests                           |
| tool use              | task success                         |
| JSON/function calling | schema validation                    |
| retrieval QA          | answer contains cited source span    |
| safety                | policy classifier + human spot-check |

For scaled RL, use verl or OpenRLHF. verl integrates with FSDP, Megatron-LM, vLLM, SGLang, and supports flexible device mapping; OpenRLHF uses a Ray + vLLM distributed architecture and advertises scaling to 70B+ models. ([GitHub][14])

## 12.4 Safety post-training

Minimum safety work:

1. refusal data
2. jailbreak evals
3. harmful request evals
4. PII leakage tests
5. hallucination/factuality tests
6. prompt-injection tests for tool use
7. deployment moderation layer

Ai2’s Safety Toolkit is a good open reference point; it includes WildGuard and safety-eval resources for harmfulness/refusal style work. ([allenai.org][15])

---
