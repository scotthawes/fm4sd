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

## Q3. Has anyone studied whether speedrun/pretraining gains survive *stacking*?

```bash
python3 scripts/fm4sd_search.py grep "catastrophic forgetting" --min-hits 3
```

**Result: 2 papers, neither tabular.**

- `continual-learning-compose` (2609.06986) — 6 hits
- `nora` (2608.31036) — 4 hits

Both are general continual-learning work. **Nothing in this index addresses whether the
speedrun ledger's gains are composable.** Each record was validated against the baseline target
independently; the paper does not test the *combination* of records 2–9 together.

This is a real gap in the protocol: the ledger is presented as a sequence where improvements
"stack", and the leaderboard rules require each record to beat the *prior record*, but the
paper does not report a joint run of all techniques. **Worth verifying empirically** before
treating 81× as achieved.

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