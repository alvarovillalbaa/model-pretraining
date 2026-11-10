# model-training specification

Last updated: 2026-11-10

Status: planned. All runtime capabilities below remain planned.

## Responsibility

Model implementations, pretraining, SFT/DPO/RL post-training, checkpoint resume, generation smoke checks, and export.

## First next work

- [ ] Build the smallest local trainer with checkpoint/resume and deterministic generation.
- [ ] Keep data and execution backends explicit optional inputs.
- [ ] Use nanochat and autoresearch as separately pinned implementations/references; preserve upstream identity.

## Interfaces and composition

Specify the smallest public API against the first real consumer before implementing it. Use explicit inputs, artifacts, versions, and dependency declarations. A planned interface is not an implementation.

[Inherited blueprint](blueprint.md) preserves the relevant original design sections. Provider claims, dates, estimates, and unimplemented features there are historical requirements, not new measurements.

[Inherited backlog](backlog.md) records the original planning status; use the implementation status above for the current boundary.

## Acceptance

The first implementation must install/run independently, declare dependencies, include a useful example and focused behavior checks, and record remaining limitations. Do not mark this project implemented solely because the documentation exists.
