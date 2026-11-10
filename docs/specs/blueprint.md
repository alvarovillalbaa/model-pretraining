# Pretraining implementation blueprint

Last updated: 2026-11-10

Status: planned. This projection narrows the original Lab design to the base
training product. The exact mixed source remains in
[the pre-expansion snapshot](../sources/pre-expansion/blueprint.md).

## First local vertical slice

Build one tiny decoder-only training path with tokenizer loading, a packed
dataset reader, causal attention, the chosen normalization/position/MLP
components, optimizer/schedule, checkpoint save/load, local validation loss,
generation, and export. Architecture implementation remains with this first
trainer; this split does not invent a separate model library.

A chat UI and public endpoint are serving consumers of the export. They are not
required to use or verify this trainer.

## Scaling and continued pretraining

After the tiny path works, add a single selected production-training adapter.
Use frozen training/validation inputs for small model/data-mix comparisons,
then choose any larger run only after observed stability, checkpoint resume,
and budget decisions. Continued pretraining, cooldown and context extension
remain explicit stages with a frozen parent checkpoint.

A base model must be evaluated and frozen before an independently configured
post-training consumer starts adaptation.

## Configuration and failure evidence

Record optimizer/schedule, precision, sequence length, batch size, clipping,
checkpoint cadence and held-out inputs. Stop or fail visibly on non-finite
training state, failed checkpoint resume, data contamination, or invalid input
identities. Preserve failure metrics and partial artifacts.

The former generic training blueprint also contained SFT/DPO/RL requirements.
Their current owner is
[model-posttraining](https://github.com/alvarovillalbaa/model-posttraining).
The [relocation map](../sources/repository-expansion-map.json) records original
Lab sections 5, 9, 10 and 11 as pretraining inputs and section 12 as post-training
input. It does not assert that either engine is implemented.
