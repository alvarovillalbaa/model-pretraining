# Pretraining backlog

Last updated: 2026-11-10

All engine work remains planned. The
[original mixed backlog](../sources/pre-expansion/backlog.md) is historical.

- [ ] Select one tiny architecture implementation from model-architectures or an external library, local runtime, tokenizer input and portable export format.
- [ ] Implement declared train/validation data loading without implicit corpus downloads.
- [ ] Implement causal model training, optimizer/schedule and local validation loss.
- [ ] Save complete training state and reject incompatible resume requests.
- [ ] Prove checkpoint resume and generation on a bounded local fixture.
- [ ] Export weights/config/tokenizer references loadable by an independent consumer.
- [ ] Add one selected scaled-training adapter after the local path and budget gates pass.
- [ ] Compare controlled data/model variants with recorded code, inputs, resources and failures.

SFT, preference losses, reward loops, and managed post-training adapters are
maintained by
[model-posttraining](https://github.com/alvarovillalbaa/model-posttraining).
