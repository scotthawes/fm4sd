# tabdpt — Scaling Tabular Foundation Models on Real Data

arXiv [2410.18164](https://arxiv.org/abs/2410.18164) · [inference](https://github.com/layer6ai-labs/TabDPT-inference) · [training](https://github.com/layer6ai-labs/TabDPT-training)
Ma, Thomas, Hosseinzadeh, et al. (Layer 6 AI). NeurIPS 2025. Source: `papers/tabdpt/text.txt`

## The finding that matters most

**Real data + self-supervised learning beats synthetic pretraining, consistently, at every
epoch** — lower test loss under the same compute budget. The paper's own words: "real data can
contain useful signal not easily captured in synthetic training."

Concretely:
- **Without SSL**, training on real data **overfits after ~50 epochs**.
- **With SSL**, the model keeps improving past **500 epochs**.
- The synthetic-data curve and the real+SSL curve both saturate around 500 (verified out to 1024).

This is the strongest published argument against the synthetic-only pretraining recipe that
nanoTabPFN and the speedrun both use.

## Scaling laws

First scaling-law analysis for TFMs not restricted to a domain. Joint power-law fit following
Hoffmann et al. Model size 33K–78M params, data 52M cells (104K rows) → 2B cells (32M rows).
Both model and data scale give consistent improvements.

## Architecture findings — the "Bitter Lessons" section

This is the most transferable part, because it says *which pretraining-data-dependent choices
transfer and which do not*:

- **Class embeddings / proto-networks hurt**, especially on real data. (Contrast TabPFN-style
  class-token schemes.)
- **Cell-as-token architectures** — tensors of `(B, N, f, d)` with vertical+horizontal
  attention à la video spatiotemporal attention — are **more memory intensive**. The simpler
  `(B, N, d)` form permits a higher embedding dim `d`. The paper suggests synthetic-vs-real
  data differences are large enough to flip which architecture wins.
- Explicitly framed as bitter-lesson caveats, with "your mileage may vary."

## Evaluation

CC18 (72 classification) and CTR23 (35 regression), 107 datasets total, 500–100K instances,
<5K features. Headline config: **2,048 context size, 8 ensemble members**, with per-member
randomness from permuting features and classes. Beats TabPFN v2 on CC18 accuracy; competitive
elsewhere; beats per-dataset DL and tree baselines.

## Why this matters here

Two direct implications for synthetic-pretraining speedrun work:

1. **There is headroom in the data, not just the optimizer.** If real data is genuinely
   under-exploited, a pretraining run that mixes in real data could reach the same AUC target
   with *less* wallclock — which is exactly the speedrun objective. Nobody in the ledger seems
   to have tried this.
2. **The architecture guidance may not survive contact with real data.** The speedrun's record
   3 (SDPA/LR/width) and record 6 (RMSNorm/thinking rows) were all tuned on synthetic data
   against a classification target. Record 6's thinking rows came from TabPFN 2.5. If TabDPT is
   right that synthetic and real data favour different architectures, some of these wins may not
   transfer to regression targets.

## Caveats

- Released as inference + training repos; not a drop-in speedrun target — architecture differs
  from nanoTabPFN's bi-attention design, so the speedrun ledger's techniques do not all apply.
- No actuarial or zero-heavy evaluation. CTR23 is regression, but ordinary continuous targets.
- Scaling-law fits are on their own architecture; the exponents are not architecture-independent.