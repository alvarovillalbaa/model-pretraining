# Inherited backlog

Last updated: 2026-11-10

Original status from [Lab](https://github.com/alvarovillalbaa/lab/blob/ba67e510bab2703b1042993744ce099490221c1f/docs/IMPLEMENTATION_BACKLOG.md), retained as planning provenance. Current implemented scope is in this repository's README.

## Tiny From-Scratch Trainer

Status: planned.

- [ ] Tokenizer loading.
- [ ] Packed dataset loader.
- [ ] Transformer block.
- [ ] RoPE.
- [ ] RMSNorm.
- [ ] SwiGLU MLP.
- [ ] Causal attention.
- [ ] AdamW.
- [ ] LR schedule.
- [ ] Checkpoint save/load.
- [ ] Eval loss.
- [ ] Generation.
- [ ] Chat wrapper.
- [ ] Export path.
- [ ] Blog/report draft.


## Pretraining

Status: planned.

- [ ] OLMo-core or TorchTitan runner.
- [ ] `lab-124m` smoke config.
- [ ] Checkpoint resume test.
- [ ] Generation smoke test.
- [ ] Eval wrapper.
- [ ] 124M data ablations.
- [ ] 350M data ablations.
- [ ] 1.3B run plan.
- [ ] 3B run plan.


## Post-Training

Status: planned.

- [ ] TRL local SFT runner.
- [ ] Fixed chat template.
- [ ] Loss masking.
- [ ] DPO runner.
- [ ] GRPO/verifiable reward runner.
- [ ] Tinker adapter.
- [ ] Safety dataset hooks.
- [ ] Post-training eval gates.
