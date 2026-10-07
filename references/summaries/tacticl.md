# tacticl — Task-Aware Compression of Tabular ICL Models

arXiv [2608.10837](https://arxiv.org/abs/2608.10837) · [code](https://github.com/Hebog/tfm_compression)
Source: `papers/tacticl/text.txt`

## What it does

Prunes transformer layers from a tabular ICL model and **replaces them with lightweight
adapters trained on the downstream task** — blending in-context with in-weight learning rather
than converting wholesale to one or the other.

Headline: **up to 85% of layers can be substituted without substantial performance drop** on a
given downstream task, with compute cost falling almost linearly in the number of dropped
layers. Evaluated on TabArena datasets; retains ICL capability and "maintains robustness to data
shifts."

## Why the framing matters more than the number

The paper draws a sharp and useful distinction that this project's corpus mostly blurs:

- **Distillation** (what TabPFN v2/v2.5/v3 ship) produces a compact MLP per dataset with
  orders-of-magnitude lower latency — but it "explicitly converts the model to the
  in-weight-learning (IWL) regime", so "the model must be **re-distilled from scratch whenever
  the context changes**", forfeiting ICL's ability to adapt at inference time.
- **Pruning + adapters** keeps ICL alive.

It also cites the sharp hazard: "the transition from in-context to in-weights learning is sharp
and often irreversible", and fine-tuning on a fixed dataset "can actively suppress its ICL
ability due to low data diversity."

**That is a concrete caution for this project's closed prior line.** Any future GPU work that
fine-tunes the released checkpoint on one insurance portfolio risks exactly this — suppressing
the general ICL ability that makes TabICL win on lapse, in exchange for a task-specific gain.
The paper says the transition is sharp and often irreversible, which is a stronger statement
than "watch out for overfitting."

## Relevance

Second-order for the hardware blocker — it reduces compute, so it could help reach higher row
counts, but it targets model depth rather than context construction, and it requires a
per-task adaptation stage. BAPS ([baps.md](baps.md)) is the more direct route to the same goal
because it is training-free.

Worth keeping for the **ICT→IWL** distinction, which is the clearest statement in the corpus of
why distillation is not a free win for a project that depends on ICL.

## Caveats

- Task-specific: the compression configuration is optimised per downstream task, so it is not a
  general speedup.
- The 85% figure is "without substantial performance drop" on a *given* task, with the drop
  definition left to the paper's own threshold.
- TabPFN v2.5-centric; no TabICL, no regression-tail, no insurance evaluation.
- No released weights; code link present in the index entry.