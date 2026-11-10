# model-pretraining

Last updated: 2026-11-10

Train base language models and continue pretraining from explicit architecture, data, and tokenizer inputs.

**Status: planned.** This repository contains the product boundary, target
interfaces, inherited requirements, and a scoped implementation backlog. It has
no executable engine or installable package.

## Independent consumer

A researcher building or continuing a base model can use this toolkit without installing a post-training, serving, or evaluation service. Its portable model export can be adapted by any compatible post-training tool.

## Start here

Read [the specification](docs/specs/README.md),
[implementation blueprint](docs/specs/blueprint.md), and
[scoped backlog](docs/specs/backlog.md). The specification is usable without a
runtime installation; implementation acceptance is still future work.

## Ownership

This component owns the first model implementation, from-scratch pretraining,
continued pretraining/cooldown, local training loss and generation checks,
resumable training state, and portable base-model export.

[model-posttraining](https://github.com/alvarovillalbaa/model-posttraining) owns
SFT, preference optimization, and reward-based adaptation of an existing model.
Serving lifecycle and workload benchmarking have separate owners. Model-data
and tokenizer tooling provide explicit input artifacts; this trainer does not
own corpus curation or tokenizer research.

The repository continues the history originally named model-training; its
canonical name is model-pretraining.

## Composition and terms

Lab may assemble pinned releases of this component; this component never
requires Lab or a sibling checkout. Exchanges use explicit versioned artifact
or endpoint references. Optional adapters must declare their own dependencies.

[Source history and relocation map](docs/sources/README.md) explains the split.
MIT covers authored code and documentation. Third-party models, data, tools,
and services retain their own terms. No source recovery, paid job, provider
acceptance, or scientific result is established by these specifications.
