# Digests

Close readings of the papers that matter most, plus the output of targeted corpus sweeps.
Each digest cites the extracted text it was written from, so claims are traceable to a line
range in `papers/<slug>/text.txt`.

## Digests

| Paper | Why it is here |
|---|---|
| [modded-nanotabpfn](modded-nanotabpfn.md) | The speedrun protocol and the full technique ledger — 74.32 → 0.92 min, with per-record attribution |
| [tabdpt](tabdpt.md) | Real data + SSL beats synthetic pretraining at every epoch. Strongest published argument against the synthetic-only recipe |
| [tabicl](tabicl.md) | Column-then-row factorization as the structural answer to quadratic cost; prior-sampling detail as an untried lever |
| [nanotabpfn](nanotabpfn.md) | The architecture being optimised, and the hard cap (binary, ≤5 features, 150 rows) inherited from the prior dump |
| [tabpfnv2closerlook](tabpfnv2closerlook.md) | Documents the high-dim/many-category/large-scale failure modes; training-free scaling path |
| [finetuningtfm](finetuningtfm.md) | Full finetuning best, via improved query–key retrieval — but unstable exactly on temporal-shift + wide tables |
| [tabpack](tabpack.md) | Attacks tuning cost by sampling ranges rather than searching. Not a TFM, but composable with one |

## Sweeps

[SWEEPS.md](SWEEPS.md) — targeted `grep` sweeps, and one **negative** result that held up:

- **Nothing** in this index treats feature-linked zeros / zero-inflated targets as an ICL
  decomposition problem. `foundcause` mentions zero-inflation only as a synthetic-data artefact.

One sweep was **wrong and has been corrected in place**. I searched paper text for evidence that
the speedrun gains stack, found nothing, and reported that as a gap. The records *do* stack —
they are explicitly cumulative (`pr19` vs `pr19+pr20`, 31 runs each) — and the paper is also two
records out of date. **A leaderboard's live state lives in its repo, not its paper.** The
correction, with the current ledger at **0.76 min / ~98×**, is in
[SWEEPS.md Q3](SWEEPS.md#q3-correction-read-the-repo-not-just-the-paper).

## Coverage

7 of 225 fetched papers digested. The rest are corpus for sweeping — see
`../manifest.json` for what is available and `../README.md` for how to search it.

Not yet digested but high-value on the current question set: `gotabpfn` (high-dim tokenization),
`tabpfn-3` (may close the high-dim limitation), `tacticl` (compression), `tabiclv2`
(next generation), `tabfm`, `tabh2o`.