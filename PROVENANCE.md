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
| Index parsed | **`README.md` in this fork** since 2026-10-08 (was upstream `main/README.md` at `62382d2`) |
| Entries parsed | 303 (was 275 before the actuarial category) |
| With an arXiv id | 253 (83%) |
| Linking released code | 117 |
| Fetched locally | 253 — 247 LaTeX source bundles, 6 PDF fallback |
| Extracted text | 33.3 MB |
| Source preference | `arxiv.org/src/<id>`, falling back to `arxiv.org/pdf/<id>` |
| PDF extractor | PyMuPDF 1.28.2 |
| Rate limit | 3 req/s, 5 worker threads |
| Not fetchable | 50 entries — `pmlr`, `doi`, `springer`, `blog`, `lgresearch`, or PDF-only links |

`references/manifest.json` is the machine-readable record: id, title, category, section,
links, sha256, source type, byte counts, fetch status. Its header records which index source was
used.

### Index source — changed 2026-10-08

`--index` used to read `INDEX_URL`, the **upstream** `raw.githubusercontent.com/.../README.md`.
This is a fork whose README we took over, so local edits were being silently ignored: 28 papers
were added, `--index` reported 275 again, and only calling `parse_index()` directly (303) exposed
the mismatch. It now defaults to the **local** `README.md`; `--remote` reads upstream for
comparison.

### Actuarial category — added 2026-10-08

28 papers under `actuarial / loss modelling`, because the original index is ML-methods only: a
sweep across 225 papers for `Tweedie|compound Poisson|loss ratio|actuarial|claim frequency`
returned **two incidental hits**.

IDs came from arXiv API **search results, never constructed**, and authors/years were taken from
the API response rather than written from memory. Digest: `references/summaries/actuarial.md`.

Scope limit: **arXiv-only.** Paywalled actuarial venues (IME, ASTIN) are unreachable, and
`fm4sd_fetch.py` skips any entry without an arXiv ID — the same reason 50 pre-existing entries
were never fetched.

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