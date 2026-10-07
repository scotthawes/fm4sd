# Provenance

Every claim about the corpus in this repo resolves to something here.

## What this repo is

A fork of [borawhocodess/fm4sd](https://github.com/borawhocodess/fm4sd), which curates a
reading index for foundation models on structured data. Upstream's index is preserved verbatim
below our banner in `README.md`.

Upstream ships **no paper content** — `papers/` is gitignored there, and the repo is otherwise
one markdown file. So there was nothing to vendor; the corpus is rebuilt by resolving the
index's links against arXiv.

| Field | Value |
|---|---|
| Upstream | https://github.com/borawhocodess/fm4sd |
| Upstream commit at fork time | `62382d23` "unnest tabpfn versions" |
| Fork created | 2026-10-05 |
| Research layer added | 2026-10-07 |
| Origin of the seminar | [fm4sd seminar, Uni Freiburg, SS 2026](https://ml.informatik.uni-freiburg.de/teaching/summer-semester-2026/seminar-seminar-on-foundation-models-for-structured-data/) |

## Corpus

| Field | Value |
|---|---|
| Index parsed | `main/README.md` at `62382d2` |
| Entries parsed | 275 |
| With an arXiv id | 225 (82%) |
| Linking released code | 117 |
| Fetched locally | 225 — 221 LaTeX source bundles, 4 PDF fallback |
| Extracted text | 30.5 MB |
| Source preference | `arxiv.org/src/<id>`, falling back to `arxiv.org/pdf/<id>` |
| PDF extractor | PyMuPDF 1.28.2 |
| Rate limit | 3 req/s, 5 worker threads |
| Not fetchable | 50 entries — `pmlr`, `doi`, `springer`, `blog`, `lgresearch`, or PDF-only links |

`references/manifest.json` is the machine-readable record: id, title, category, section,
links, sha256, source type, byte counts, fetch status.

## Licence — recorded as UNKNOWN, deliberately

`scripts/fm4sd_licences.py` queries the arXiv API for a licence per paper. It returns
**`api-omitted` for all 225**.

Verified directly rather than assumed: the API omits the `<arxiv:license>` element entirely.
2511.03634 is CC-BY-4.0 on its `arxiv.org/abs` page, yet carries no licence element in the API
response. The manifest therefore records the licence as **unreported**, not as absent.

`redistributable` is `false` everywhere — an unreported licence is an unknown one, and unknown
is not permission. Hence:

```
papers/                     # gitignored — licence unresolved, and 78 MB
references/manifest.json    # tracked — public metadata only
```

Individually observed while reading (a sample, **not** a census):

| Paper | Licence |
|---|---|
| `nanotabpfn`, `modded-nanotabpfn`, `tabpfnv2closerlook` | CC-BY-4.0 |
| `finetuningtfm` | CC-BY-NC-SA 4.0 — non-commercial, no derivatives |
| `tabicl`, `tabdpt`, `tabpack` | arXiv non-exclusive distribution |

To clear any paper for redistribution, read the licence line on its abs page and add a row here.

## Reproducing the corpus

```bash
python3 scripts/fm4sd_fetch.py --index      # README -> references/manifest.json
python3 scripts/fm4sd_fetch.py --fetch      # fetch + extract, resumable
python3 scripts/fm4sd_licences.py           # licence fields from the arXiv API
python3 scripts/fm4sd_search.py stats
```

`--index` is safe to re-run: it reconciles against payload already on disk and preserves
fetch metadata rather than forcing a refetch. `--force` refetches deliberately.

## Digests

`references/summaries/*.md` are written by an LLM reading `papers/<slug>/text.txt`, and each
cites the extracted text it came from. They are **synthesis, not primary sources** — verify
any load-bearing claim against the paper before relying on it. `SWEEPS.md` records the
`grep` queries used, so a negative result is reproducible rather than anecdotal.

## Corrections log

| Date | Correction |
|---|---|
| 2026-10-07 | `iced` was mislabelled as a model-compression paper in the private repo's notes. It is *In-Context Density Estimation for Tabular Data*. The compression paper is `tacticl` (*Task-Aware Compression of Tabular ICL Models*). |