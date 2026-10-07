# finetuningtfm — On Finetuning Tabular Foundation Models

arXiv [2506.08982](https://arxiv.org/abs/2506.08982) · [code](https://github.com/yandex-research/tabpfn-finetuning) · CC BY-NC-SA 4.0
Rubachev, Kotelnikov, Kartashev, Babenko (Yandex). Source: `papers/finetuningtfm/text.txt`

## Findings

**Full finetuning wins** — the paper establishes it as the most practical option for TabPFN v2
on both time-efficiency and effectiveness, after systematically evaluating alternatives
(LoRA-style and partial variants included).

**The mechanism is retrieval, and this is the valuable part.** Drawing an analogy to
retrieval-augmented models: after gradient-based adaptation, the **dot products of
query-representations of test objects with key-representations of in-context training objects
better reflect their target similarity.** ICL is a soft retrieval step, and finetuning sharpens
it — which lets the model weight in-context samples more appropriately and so approximate the
target dependency better.

That is a mechanistic explanation, not just a leaderboard delta, and it is the kind of claim
that suggests *interventions*: if you want better predictions and cannot afford full
finetuning, you want something that improves query–key alignment specifically.

## Scale and stability

- Finetuned successfully on datasets up to **50K objects**, with improvements on almost all tasks.
- On academic datasets with **IID splits**: achieves **state-of-the-art** results.
- On datasets with **gradual temporal shifts and rich feature sets**: **less stable**, prior
  methods remain better.

## Why it matters here

1. **The retrieval framing connects ICL to representation quality**, and gives a concrete lever:
   the speedrun optimises wallclock to a fixed AUC target. Full finetuning says there is a
   *different* route to a better target — one that adapts the model to the specific dataset
   instead of pretraining harder on a synthetic prior.
2. **The stability caveat is the important half.** The failure mode is specifically *gradual
   temporal shift + rich feature sets* — long time horizons with many features, which is the
   common case in applied tabular work. So the "finetuning gives SOTA" headline should **not**
   be assumed to transfer, and the honest read is that the IID win is the inapplicable part.
3. It also implies TabPFN v2's pretrained representation is *not* already optimal for
   shifted, wide tables — consistent with the closer-look paper's limitation list.

## Caveats

- **CC BY-NC-SA** — non-commercial, no derivatives. Not redistributable; `papers/` stays local.
- Evaluated on TabPFN **v2** specifically; the architecture has changed in later versions.
- 50K-object ceiling means this says nothing about whether finetuning scales to the dataset sizes
  where a TFM would actually be deployed.