# Sweep findings

Targeted `fm4sd_search.py grep` sweeps run 2026-10-07 over the 225-paper local corpus, aimed
at questions the digests raised. Recorded because a negative sweep result is evidence too:
it says the literature does *not* address something, which is a reason to believe a finding is
novel rather than a reason to have not looked.

Reproduce any of these with the command shown.

---

## Q1. Has anyone published on TFMs exploiting *feature-linked zeros* / zero-inflated targets?

```bash
python3 scripts/fm4sd_search.py grep "zero-inflation|hurdle model" --min-hits 3
```

**Result: 1 paper, 2068 hits total — and it is off-target.**

`foundcause` (2606.17516) mentions zero-inflation only as one item in a list of *post-processing
artefacts injected into synthetic data*:

> post-processing pipeline injects real-world artefacts: MCAR / MNAR / MAR missingness (15% of
> tasks), measurement noise, **zero-inflation**, discretization, rounding, heteroscedasticity,
> and log-uniform scale

That is a data-generator detail, not a treatment of zero-inflated *targets* as an ICL problem.

**Conclusion.** Nothing in this index addresses a TFM using an indicator of feature-linked
zeros as part of the prediction. The zero-exploitation result appears to be genuinely
unclaimed. Related but distinct: `iced` (In-Context Density Estimation for Tabular Data) targets
the zero problem as *density estimation*, not as a two-part predictive decomposition.

---

## Q2. What is published on TabPFN/TFM failure at high dimension and many categories?

```bash
python3 scripts/fm4sd_search.py grep "high-dimensional" --category tabular --min-hits 4
```

Five papers, in rough order of relevance:

| Paper | Hits | Angle |
|---|---|---|
| `gotabpfn` (2606.05441) | 33 | From feature ordering to **compact tokenization** for high-dimensional tabular FM. Most directly on point. |
| `tabpfnv2closerlook` (2502.17361) | 17 | Documents the limitation; test-time divide-and-conquer. [Digest](tabpfnv2closerlook.md) |
| `contextadaptiveinference` (2607.23304) | 16 | Adaptive inference under distribution shift. |
| `tabflex` (2506.05584) | 13 | In-context learning, Microsoft TICLE line. |
| `tabpfn-3` (2605.13986) | 10 | TabPFN v3; may have addressed the limitation directly. |

**Next step:** read `gotabpfn` and `tabpfn-3` before assuming the limitation is still open —
both postdate the closer-look analysis.

---

## Q3. Do the speedrun gains actually *stack*?

⚠️ **CORRECTION — an earlier version of this file said "no evidence". That was wrong.** The
sweep searched paper text and found nothing, which I over-read as a gap in the literature. The
definite answer is in the **speedrun repo, not the paper** — and the paper is also out of date.
See [the correction below](#q3-correction-read-the-repo-not-just-the-paper).

### Q3 correction: read the repo, not just the paper

The records are **explicitly cumulative**, and each one is verified against the previous with
many repeated runs.

Evidence from `records/20260826widthsort/README.md`:

> Comparison — 31 runs each of the previous record (**pr19**) and this one (**pr19+pr20**)

The `pr19+pr20` notation is the point: a new record is *previous + one change*, not a parallel
alternative. And the record itself states the change is narrow — "the model and optimizer are
unchanged, only the order of the datasets within an epoch." So record 11 is record 10 plus a
dataloader sort, measured over 31 runs each with bootstrap 95% CIs.

Verified in the live `train_nano.py` (898 lines, single script): markers for Muon (14 hits),
zeropower/Newton–Schulz (6), SDPA (2), `compile` (3), bf16 (5), `float32_matmul_precision` (1),
residual decay `0.95` (2), RMSNorm (4), thinking rows (11), feature grouping (10) — all present
in one file. The gains do compose.

### The paper's numbers are stale

The paper reports record 9 at **0.92 min**. The live repo has two records since:

| # | Record | Date | Change | Contributor |
|---|---|---|---|---|
| 10 | **0.79 min** | 2026-08-15 | Shape-grouped Newton–Schulz, producer-thread dataloader, single datapoint SDPA | @tjeong117 |
| 11 | **0.76 min** | 2026-08-26 | Feature-width sorted batching | @shounakb1 |

Current: **0.76 min vs the 74.32 min baseline ≈ 98×**, on 4,096 synthetic datasets vs 80,576.

**Quote 0.76 min / ~98×, not 0.92 min / 81×**, unless specifically discussing the paper.

### Two caveats on record 11 worth knowing

1. **Its margin is thin.** Record 10 median is 0.79 min, record 11 median 0.76 min, and record
   11's own std is **0.06 min**. The improvement is roughly half a standard deviation — it may
   not be a real separation. Record 11's own numbers also show the trade-off honestly: epochs got
   *faster* per epoch (0.84s → 0.72s) but it needs *more* of them (57 → 64).
2. **Sub-minute timings assume a warm `torch.compile` cache** — the record's README says so
   explicitly. First-run wallclock will not match.

### The exact target and eval, for reproduction

Target: **≤ 0.8068462330697953** validation average ROC AUC — chosen to match Random Forest on the
same subsampled TabArena evaluation. Evaluation: 38 TabArena **classification** tasks;
subsample to 100 features and 1,000 rows (stratified by class); 5-fold shuffled
`StratifiedKFold`; constant columns dropped, numeric mean-imputed, categorical ordinal-encoded;
binary or one-vs-rest ROC AUC averaged over tasks.

Note the paper itself is inconsistent on thinking-row count — the record-6 text says 16, the
architecture caption says 24, and the live repo README lists **24**.

For a clean starting point the repo recommends commit **`b0f29b7`**; `train_nano.py` on `main`
has "gotten a bit crowded".

---

## Original sweep (superseded by the correction above)

```bash
python3 scripts/fm4sd_search.py grep "catastrophic forgetting" --min-hits 3
```

Returned 2 papers, neither tabular: `continual-learning-compose` (6 hits), `nora` (4 hits).

The corpus sweep was simply the wrong instrument here — a speedrun leaderboard's live state
lives in its repo, and a 2026-06 paper cannot describe records set in 2026-08.

---

## Q4. Which papers define the TabArena evaluation protocol?

```bash
python3 scripts/fm4sd_search.py grep "TabArena" --min-hits 3
```

`tabarena` (199 hits) is canonical. `tabpfn-3` (89), `tabprep` (107), `onelayerenough` (123),
`beyondarena` (157), `agentic-search-spaces` (90) all build on it.

Relevant caveat: the speedrun evaluates on **subsampled** TabArena. `beyondarena`
(2606.30410) exists because TabArena itself was found to saturate — worth checking before
treating a TabArena AUC as a meaningful target.

---

## Q5. Compression / quantization of TFMs — for the inference-cost side

```bash
python3 scripts/fm4sd_search.py list --category tabular --has-code | grep -iE "quant|compress|distill"
```

| Paper | Title |
|---|---|
| `tacticl` (2608.10837) | **TACTICL: Task-Aware Compression of Tabular ICL Models** |
| `attention-quantization-tfm` (2609.13031) | Attention Quantization for Tabular Foundation Models |
| `local-distillation` (2608.23538) | Local distillation (repo: `erincr/local-distillation-benchmark`) |
| `memoryefficienttfms` (2607.27546) | Memory-efficient TFMs |
| `loopicl` (2609.36108) | Loop ICL |

⚠️ **Correction to an earlier note in the private repo's docs:** `iced` was listed there as a
"compression" paper. It is **not** — `iced` is *In-Context Density Estimation for Tabular Data*.
The compression paper is `tacticl`. Fixed in this fork's docs; the private repo's copy still
carries the wrong label.

Upstream's own `todos` file independently flags "quantization in the TFM world … applied to
TabPFN", so this axis is already on the maintainer's radar.