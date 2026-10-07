# closedloop-priorselect — When and Why LLM Causal Priors Help

arXiv [2609.06941](https://arxiv.org/abs/2609.06941) · prior *selection* for a PFN
Source: `papers/closedloop-priorselect/text.txt`

## Why this one matters

**It independently corroborates this project's central negative result from a different
literature, with nine controlled experiments and three theorems.** It is the strongest external
support the closed prior line has, and it was found by accident — a sweep for "two-part /
hurdle" surfaced it via "two-stage", not via any prior-related query.

It is also the closest published precedent to the question the roadmap asked: *is retraining the
prior worth it?* It answers that question generally, for a different PFN family, and gets the
same answer this project reached empirically.

## The result

Nine controlled experiments on a 7.34M-parameter Do-PFN yield a **three-condition empirical
regularity**: prior injection yields significant gains **only when all three hold**:

1. **the base is underfit on the task domain**
2. **the prior domain matches the task domain**
3. **the task lies within the support of the base's training prior**

And three theorems give it conceptual grounding:

- **ensemble dilution** — combining prior sources cannot improve, and empirically *degrades*
  (three-source ensembling: −0.111, cross-seed sd up ~4.7×)
- **probe–transfer ranking reversal** — "synthetic probe metrics systematically diverge from
  real-domain transfer"
- **support boundary on repair** — "risk outside the support cannot be bounded by the training
  objective"

## How this maps onto this project

| closedloop condition | this project's evidence | verdict |
|---|---|---|
| **1. base must be underfit** | F32: the stock checkpoint already exploits feature-linked zero-inflation at **98.7% of the calibrated Bayes ceiling**, beating the GBDT, with no training | **FAILS — base is at the ceiling, so not underfit** |
| **2. prior domain must match** | The prior is binary / ≤5 features / 150 rows; insurance is 19–130 features, 89% zeros, continuous | **FAILS** |
| **3. task within prior support** | Task dimensionality far exceeds the prior's support (19–130 vs ≤5) | **FAILS** |

All three conditions fail, so the regularity predicts **no gain from prior injection** — which is
exactly what F32 concluded from measurement. Two independent routes, same answer.

And the paper states the failure mode in the same terms this project found it:

> injecting the same recipe into an **already-strong base degrades it 4–9× with a complete
> ranking reversal**

That is the formal analogue of F32's finding. The project closed the prior line because the
checkpoint was already at the bound; this paper says an already-strong base is precisely the
case where injection *hurts*.

## The one place it pushes back — and the distinction that matters

The paper's support-boundary theorem says the only way to repair an out-of-support task is to
**expand the support itself** — retrain on a higher-dimensional prior — "not to pick a better
prior within the existing support."

Read carelessly, that argues *for* the ~24.5 H100-day full pretraining this project declined.
It does not, and the reason is worth recording precisely, because the two "walls" are different
objects:

| | closedloop's wall | this project's ceiling (F30) |
|---|---|---|
| what it is | the **model/prior** cannot represent the task | the **data** does not contain the signal |
| measured as | risk unbounded outside the training objective | conditional severity R² ≈ **0.047** — ~5% explainable |
| fixable by retraining? | **yes** — expand the support | **no** — no model recovers magnitude the features do not carry |

F30 is explicit that the severity ceiling is "a property of the **feature set**, not the model
class" — and it was verified robust across Ridge, HistGB and ExtraTrees, tuning, and both
shuffled and unshuffled folds. A wider prior does not add information the covariates do not
contain. So support expansion would address a different wall than the one that actually binds
here.

**This is the sharper version of the project's own conclusion:** the prior line is closed not
merely because the checkpoint is at the ceiling, but because the residual axis is an
information limit rather than a representation limit. closedloop's theorem would reopen the
question only if the ceiling were a representation limit — and F30 says it is not.

## Two smaller transfers

- **"Synthetic probe metrics systematically diverge from real-domain transfer."** A caution for
  any future use of synthetic priors as a proxy: probe performance is not evidence of transfer.
  Aligns with F20's finding that a toy at perfect separability gave +0.505 against +0.202 at
  real difficulty.
- **Selection beats ensembling; averaging dilutes.** Relevant to the two-step TabICL (F31) —
  the gain comes from putting each model where the signal is, not from blending models.

## Caveats

- **Different task entirely**: causal effect estimation (law_race / IHDP / Lalonde), NMSE on
  interventional effects. Not tabular regression, not insurance, not classification.
- Different PFN family (Do-PFN, 7.34M params), not TabPFN or TabICL.
- The three-condition regularity is an **empirical induction, not a theorem** — the paper says
  so explicitly, notes each condition rests on a single instance and that conditions two and
  three are not separable, and claims no quantitative prediction.
- LLM-distilled causal *graphs* are the injected prior material — a different kind of "prior"
  than a synthetic tabular generator.
- The headline 2.75× gain is a **descriptive cross-lineage comparison**, by the paper's own
  admission: the official base is not part of the paired test.