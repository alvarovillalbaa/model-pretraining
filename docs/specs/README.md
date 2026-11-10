# Model pretraining specification

Last updated: 2026-11-10

Status: planned. The target capabilities below are requirements, not current
functions or a released wire schema.

## Purpose and consumer

Build a base language model from a declared architecture and frozen data, or
continue pretraining from an explicitly supplied compatible checkpoint. Neither
post-training nor serving is required to use the trainer.

## Target interface

- A pretraining request supplies the architecture/init policy; immutable train
  and held-out manifest references; tokenizer identity/hash; optimizer,
  schedule, seed, precision and resource configuration; checkpoint/output
  destinations; and the selected execution adapter.
- Architecture configuration/forward/cache and portable weight compatibility
  belong to model-architectures or an external selected library. The trainer
  owns optimization/data iteration and complete training-state resume semantics.
- A resume request adds an exact checkpoint reference and its training-state
  identity. It must reject incompatible architecture, tokenizer, data, or
  optimizer-state changes rather than silently start a different experiment.
- A run emits observed training metrics and status, resumable checkpoints, and
  a portable model export with weights/config/tokenizer references and hashes.
  The portable export does not require the optimizer state to be consumed.
- Evaluation is an optional external consumer of frozen model exports. A local
  loss/generation check does not become a capability or safety benchmark.

These are behavioral interfaces; no CLI, Python API, artifact schema, or
adapter is implemented yet.

## Prerequisites and scope

Use a tiny authorized local corpus and a declared tokenizer before scaling.
The first implementation must select one model/export format and one local
execution path, pin its environment, and record resource limits. A CPU toy
path is acceptable; remote GPU access and paid runs retain the owner's gates.

SFT, DPO/IPO/KTO/ORPO, reward-based post-training, deployment, and inference
benchmark methodology are outside this owner. Training data curation and
tokenizer training are upstream capabilities, not hidden side effects.

## Next work and acceptance

Follow [the scoped backlog](backlog.md). A first accepted implementation must
run with its own declared environment, produce a useful local result, resume a
checkpoint, and export a model that a separately installed consumer can load.
Record the data/tokenizer/code/environment identities, seeds, failures, and
observed time/cost coverage. Unknown cost remains unknown.

[Blueprint](blueprint.md) · [Original source and relocation](../sources/README.md)
