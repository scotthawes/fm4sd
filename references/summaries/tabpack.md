# tabpack — efficient hyperparameter ensembles

arXiv [2607.05380](https://arxiv.org/abs/2607.05380) · [code](https://github.com/yandex-research/tabpack) · ICML 2026
Gorishniy, Kotelnikov, Rubachev, Babenko (Yandex — same group as finetuningtfm).
Source: `papers/tabpack/text.txt`

## What it does

An MLP ensemble that avoids hyperparameter tuning by **sampling** rather than searching. In a
single run it samples many MLPs across hyperparameter ranges, trains them in parallel, and
**selects ensemble members on the fly during training**.

The key property: you specify **ranges**, not exact values. Precision-demanding tuning is
replaced by range selection, which is a much easier problem to get roughly right.

Reported: on par with extensively tuned prior methods at default settings. The headline
efficiency claim is that the default configuration on a **modern MacBook took less time than
tuning some baselines on an industry-grade GPU**.

## Why it belongs in this corpus

It is **not** a foundation model — no ICL, no synthetic prior. Included deliberately because it
attacks a different bottleneck: the compute spent on per-dataset hyperparameter search, which is
the dominant cost in most tabular pipelines and which the speedrun protocol sidesteps entirely
by fixing a target.

The mechanism generalises. If range-sampling-and-selecting works for MLP ensembles, the obvious
question is whether it composes with a TFM: sample nanoTabPFN configs across a range, train in
parallel, select on the fly. That would attack pretraining cost from the same angle as the
speedrun ledger but along a dimension the ledger has not touched — the ledger varies
optimizer/precision/architecture, it never varies the *search* itself.

Same author group as [finetuningtfm](finetuningtfm.md), which found full finetuning best for
TabPFN v2. These two read as a coherent programme on tuning cost.

## Caveats

- Classification/regression on standard benchmarks — no actuarial or zero-heavy evaluation.
- MLP ensembles, not ICL models. Performance does not transfer to the TabPFN family.
- ICML 2026; very recent, no independent replication found in this corpus.