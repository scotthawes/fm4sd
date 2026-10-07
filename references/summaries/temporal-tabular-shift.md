# temporal-tabular-shift — The Anatomy and Boundary of Adaptation

arXiv [2609.12136](https://arxiv.org/abs/2609.12136) · Wang et al., KTH. Sept 2026
Source: `papers/temporal-tabular-shift/text.txt`

## Why it is here

It is the one paper in the corpus with a **formal, sample-size-invariant lower bound** on what a
frozen tabular foundation model cannot recover — a proof-shaped version of what this project
established by measurement. Worth recording as the theoretical vocabulary for the p99 ceiling,
with a clear statement of where the analogy breaks.

## The result

Prequential adaptation of **frozen** TFMs under temporal drift: "helps some deployments and
harms others, yet current practice does not predict which."

Two distinct walls:

1. **The identifiability wall.** Within an agnostic total-variation drift class, "the target
   conditional is only partially identified: its identified-set diameter, the **wall**, is
   irreducible from unlabeled data uniformly in sample size." Formally a Le Cam two-point lower
   bound: the wall height is "a functional of the prior alone" and **invariant to n**.
2. **The $L^2$ projection wall** — quantifies "what the frozen representation cannot express."

Two canonical mechanism priors collapse the first wall. Under stated nuisance-rate conditions
the wall can be estimated from labeled historical windows at a √N rate above a margin threshold
$\gamma^\star = d_0/(2\alpha_s)$. At γ=0 the conditional lower-bound program depends on an open
affinity estimate; the positive-margin lower branch also remains open.

Note the paper is careful about its own status — results are tagged `[proven]`, `[partial]`,
`[conjecture]`, and one remark explicitly says an "unrestricted maximal wall" reading "does not
hold: heuristic only."

## Relevance to this project

The useful transfer is the **shape of the argument**, not the setting:

| | temporal-tabular-shift | this project (F30) |
|---|---|---|
| object | identifiability from *unlabeled* data under drift | explainable variance from a *fixed feature set* |
| claim | wall is irreducible **uniformly in sample size** | conditional severity R² ≈ 0.047 — **independent of model class** |
| method | Le Cam two-point lower bound | measured across Ridge / HistGB / ExtraTrees, tuning, shuffled and unshuffled folds |
| consequence | more data does not help | more data or a better model does not help |

Both say "the limit is not a sample-size problem." That is the same conclusion this project
reached for the p99 tail (F30, and F16's finding that the TabICL tail deficit is invariant to row
count, 0.294 → 0.260 over 8× rows). Having a published lower bound of the same form is useful
support when the ceiling claim is challenged.

The paper also supplies vocabulary worth borrowing: the distinction between a **wall** the model
cannot cross and a **projection** limit of a frozen representation. This project's F30 ceiling is
closer to the second.

## Where the analogy breaks — do not overclaim

- **Different wall.** temporal-tabular-shift's wall is about *identifiability from unlabeled
  data* — information that exists but cannot be pinned down without the labels you do not have.
  F30's ceiling is about *information the covariates do not carry at all*. These are different
  obstructions; a lower bound for one is not a lower bound for the other.
- **Different regime.** Streaming/prequential with temporal drift, single-pass, labels revealed
  after prediction. This project does folds of i.i.d. (or shuffled) splits on a static table.
- **Different model setting.** Frozen TFM adaptation under drift; not prior design, not
  two-part modelling, not insurance.
- **The interesting cases are open in the source paper** — γ=0 and the positive-margin lower
  branch are explicitly unresolved, and one supporting bound is `[conjecture]` with a stated
  failure mode (boundary π_W, barely-overlapping class-conditional supports).

**Do not cite this as proof that F30's ceiling is fundamental.** Cite it, if at all, as an
independent instance of the same *kind* of claim, and be explicit that the objects differ.

## Caveats

- Very theoretical; the empirical content is semi-synthetic plus "eight industrial streams"
  falling "on the difficult side under a stated roughness bound."
- The equality case γ = γ⋆ "remains unresolved."
- Not insurance, not severity, not a tail metric.