# The Corpus

### A genealogy of the datasets that trained the frontier models

---

## How to read this

The layers are organised as a genealogy rather than a catalogue, because the datasets are descended from each other. C4 begat RefinedWeb begat FineWeb begat FineWeb-Edu, each one a filtering opinion applied to the same underlying Common Crawl. Knowing the lineage tells you what each one threw away.

Three things worth holding onto:

- **Almost everything catalogued here is public.** The frontier labs train on approximately this, plus licensed books and their own post-training data. If general intelligence were a corpus problem it would be solved, because the corpus is sitting there and it is mostly free.
- **Public is not the same as reachable.** About a third of this isn't on HuggingFace at all, one major entry is on HuggingFace but contains no data, and a few cost money. The **Access** column says which. See *Getting it* for the mechanics.
- **Sizes are in tokens or bytes, not both, because nobody agrees.** Where a figure is a derivation rather than a published number, it's marked ≈.

**Access codes:** `HF` streamable now · `HF-gated` click-through, then normal · `HF-ptr` pointers only, content elsewhere · `S3` AWS, free · `S3-$` requester-pays, you pay egress · `site` project website · `licence` signed agreement or money · `gone` no longer distributed

---

## Layer 0 — Hand-built (1961–2011)

The era when a corpus was something people typed. Every resource here was assembled by linguists, by hand, at a cost per token roughly a billion times higher than anything that followed. None of them are used for pretraining today. All of them shaped how the field thinks about meaning.

| Resource | Scale | Access | Note |
|---|---|---|---|
| **Brown Corpus** (1961) | 1M words | `site` | The actual zero. Balanced American English, hand-tagged. Ships in NLTK. |
| **Penn Treebank** (1992) | ~4.5M words | `licence` | LDC. Costs money. WSJ text with syntactic parse trees; trained a generation of parsers. |
| **British National Corpus** (1994) | 100M words | `licence` | Oxford. The first corpus large enough to argue about. |
| **Princeton WordNet** | 117,659 synsets (3.0) | `site` | wordnet.princeton.edu, or `nltk.download('wordnet')`. Frozen — 3.0 is what NLTK ships, 3.1 is online-only. |
| **FrameNet / VerbNet / PropBank** | thousands of frames | `licence` | FrameNet needs a signed agreement; VerbNet is freer. Semantic roles, by hand. |
| **EuroWordNet** (1996–99) | 8 languages | `licence` | Introduced the Inter-Lingual-Index. Not free. |
| **ConceptNet** | ~34M edges (5.7) | `site` | conceptnet.io S3 dumps. HF mirrors exist, of varying fidelity. |

### The wordnets, specifically


| Resource | Coverage | Access | Status |
|---|---|---|---|
| **Open English WordNet** | 152K words / 121K synsets (2024) | `site` | GitHub (`globalwordnet/english-wordnet`), WN-LMF XML. The living fork of Princeton, annual releases. |
| **Open Multilingual Wordnet** | 40+ languages, curated | `site` | omwn.org. Linked via CILI. Ships with NLTK; use the `wn` package instead. |
| **CILI** | interlingual concept IDs | `site` | GitHub (`globalwordnet/cili`), Turtle RDF. The Collaborative Interlingual Index (Bond, Vossen, McCrae, Fellbaum 2016). Exists to break OMW 1.0's Princeton-as-pivot dependency, which lost every concept English lacked. |
| **BabelNet 5.3** | 600 languages, ~23M synsets, ~1.7B senses | `licence` | Registration + licence agreement for the dump. API caps at 1,000 BabelCoins/day, which rules out bulk. Auto-merged from WordNet + OEWN + Wikipedia + Wiktionary + Wikidata + OmegaWiki + wordnets in 33 languages. |
| **National wordnets** | plWordNet, GermaNet, IndoWordNet, MCR, FinnWordNet… | mixed | Quality high, licences a minefield. GermaNet isn't free. OMW licensing varies *per language* — check before you bundle. |
| **Wikidata** | ~100M items, 300+ languages | `site` | dumps.wikimedia.org, ~130 GB bz2. Arguably the most successful interlingua ever shipped. For named entities it beats every wordnet. |

**Retrieval note.** The `wn` package handles all of these incrementally and per-project — `wn.download('oewn:2024')`. All of them together are ~500 MB.

**The structural caveat.** Most non-English wordnets were built by the *expand* method: take Princeton's synsets, translate the lemmas. The survey literature is blunt about it — most have one-to-one links to English synsets. They are English's conceptual carving wearing other languages' vocabulary. The exceptions (plWordNet, GermaNet) were built *merge*-style from native lexicography and are correspondingly harder to align.

---

## Layer 1 — The raw material

| Resource | Scale | Access | Note |
|---|---|---|---|
| **Common Crawl** (2008–) | 300B+ page captures cumulative | `S3` | **Not on HF.** `s3://commoncrawl/` or data.commoncrawl.org, free, no account. Deliberate: CC avoids storage costs by staying on AWS Open Data and dodges egress fees by encouraging in-place analysis. ~2.3B pages / ~398 TiB uncompressed per snapshot. Everything downstream is a filtering opinion applied to this. |
| **CC web graph** | 247.3M hosts / 6.3B edges | `S3` | Same bucket, `projects/hyperlinkgraph/`. PageRank and harmonic centrality precomputed. WebGraph-compressed it fits in RAM. |
| **Software Heritage** | 3.28B files / 104.2M repos | `licence` | Own S3. Bulk access requires an agreement with SWH and INRIA. The archive The Stack is built from. |
| **Wikipedia dumps** | ~100 GB all languages | `site` / `HF` | dumps.wikimedia.org is canonical; `wikimedia/wikipedia` on HF is a good preprocessed parquet cut and the easier path. |

**Retrieval note.** Don't download crawls. Each CC snapshot ships a columnar URL index (~200 GB) that lets you locate specific pages by URL, domain, or timestamp and then range-request only the relevant WARC offsets. The `cdx` API handles single lookups. Downloading a 398 TiB snapshot to find 10,000 pages is the most common and most expensive mistake here.

A datum worth internalising: of 2.3 billion pages in the January 2026 crawl, only 616 million were URLs never seen in any prior crawl. The web is mostly a copy of itself.

---

## Layer 2 — First-generation web corpora (2019–2023)

| Dataset | Scale | Access | Lineage |
|---|---|---|---|
| **BookCorpus** | 7,000 books / 985M words | `HF` | Legally murky; the original is gone, HF has reconstructions. Scraped from Smashwords. Trained the original GPT and BERT. The whole edifice started on self-published indie ebooks. |
| **WebText / OpenWebText** | ~40 GB | `HF` | `Skylion007/openwebtext`. Outbound Reddit links with ≥3 karma. GPT-2. Karma as a quality classifier. |
| **C4** (2020) | 305 GB | `HF` | `allenai/c4`. T5. Filtered Common Crawl, naive by later standards. |
| **The Pile** (2020) | 825 GB, 22 subsets | `gone` / partial | Original taken down post-litigation. `monology/pile-uncopyrighted` is the surviving cut. The first serious attempt at a *mixture* — arXiv, PubMed, GitHub, Books3, StackExchange. |
| **Books3** | ~197K books | `gone` | Removed after litigation. Its absence is the largest hole in the open stack. |
| **OSCAR / CCNet** | multilingual | `HF` | The multilingual CC pipelines that came before FineWeb-2. |
| **MassiveText** | 2.35B docs | `gone` | Gopher / Chinchilla. DeepMind, never released. |
| **RefinedWeb** (2023) | 5T tokens / 1.68 TB | `HF` **partial** |  `tiiuae/falcon-refinedweb` is a **~600B-token public extract, not the full 5T**. The headline number and the downloadable number are different. Proved that aggressively-filtered web *alone* beats curated mixtures — the turning point. |

---

## Layer 3 — The modern web stack (2024–)

This layer is the good news: it is almost entirely HF-native, ungated, and streamable. If you only take one thing operationally, take the Layer 3 table.

| Dataset | Scale | Access | Note |
|---|---|---|---|
| **FineWeb** | 15T tokens / 44 TB | `HF` | 96 CC snapshots, MinHash dedup at 0.85 Jaccard, PII redaction. ~5–10% survival from raw. The reference corpus. |
| **FineWeb-Edu** | 1.3T tokens (≈4 TB) | `HF` | See *FineWeb-Edu in detail*. Configs: `sample-10BT` (28.5 GB), `-100BT`, `-350BT`, plus one per crawl. |
| **FineWeb-Edu-Score-2** | 5.4T tokens | `HF` | The looser threshold (≥2) variant. |
| **FineWeb-2** | 1000+ languages | `HF` | The multilingual successor. |
| **DCLM-baseline** | 3.8T tokens | `HF` | DataComp-LM. Competing filtering opinion. |
| **Dolma** | 3T tokens | `HF-gated` | AI2, fully open pipeline. OLMo's corpus. Click-through, then normal. |
| **RedPajama-v2** | 30T tokens | `HF` | Raw + quality signals, 5 languages. Least filtered, most flexible. |
| **Nemotron-CC** | ~6.3T tokens | `HF` | NVIDIA. Synthetic rephrasing of CC to raise quality. |
| **GneissWeb** | >10T tokens | `HF` | IBM. Beats FineWeb v1.1 by ~2.7pp across 11 benchmarks. |

### FineWeb-Edu in detail

- **Method:** Llama-3-70B-Instruct scored ~450–500K FineWeb samples for educational value 0–5 on an additive scale. A Snowflake-arctic-embed-m encoder with a linear regression head was trained on those annotations, then applied to all 15T tokens — 6,000 H100 hours.
- **Threshold:** ≥3. This removed **92%** of FineWeb, leaving 1.3T tokens.
- **Deliberate bias:** the annotation was anchored on *grade-school and middle-school knowledge*, explicitly to stop the model favouring highly technical pages like arXiv abstracts. The classifier card confirms it: weak on higher education and specialised domains, prone to overfitting on academic-*looking* content.
- **The tradeoff:** raising the threshold above 3 improved knowledge and reasoning benchmarks but *significantly degraded* HellaSwag and PIQA. Educational value was bought by selling commonsense and physical reasoning. Someone had to just pick a number.
- **What it is not:** it contains no instruction pairs, no prompt/response format, no chat. It is English web prose, tuned away from the technical. It will not teach a model to architect an application.
- **Derivatives:** the recipe was ported to code (**Stack-Edu**) and to Chinese (**FineWeb-Edu-Chinese**).

---

## Layer 4 — Code

| Dataset | Scale | Access | Note |
|---|---|---|---|
| **The Stack v1** | 6.4 TB full / 2.9 TB dedup / ~200B tokens train | `HF-gated` | BigCode, first pass. Contains actual file contents — unlike its successor. |
| **The Stack v2** | 3.28B files, 600+ languages, ~900B tokens | `HF-ptr` |  **See *The Stack v2 is not a dataset*.** 67.5 TB raw / 32.1 TB dedup / ~3 TB train. From Software Heritage (SWH graph 2023-09-06) + GHArchive metadata. Trained StarCoder2. |
| **StarCoder2Data** | ~4T tokens (trained on) | `HF-ptr` | The curated training mix. Same pointer structure. |
| **Stack-Edu** | ~125–160B tokens ≈ | `HF` | Edu-classifier applied to StarCoder2Data. Python: 50.6B → 21.8B. Has content. |
| **SwallowCode** | 16.1B tokens | `HF` | LLM-rewritten code — style-guided and self-containment-optimised. Rewriting, not just filtering. |
| **CodeParrot-Clean** | 12.8B tokens | `HF` | Early, small, still cited as a baseline. |

###  The Stack v2 is not a dataset. It's an index.

The single biggest trap among everything catalogued here, so it gets its own heading.

The four HF repos (`the-stack-v2`, `-dedup`, `-train-full-ids`, `-train-smol-ids`) **contain only SWHIDs, not the content of the files**. `train-smol-ids` is **59.3 GB total** — that's the whole ~900B-token corpus, because it's pure pointers plus metadata. The actual bytes live in `s3://softwareheritage/content/{blob_id}`, gzipped.

To get code out of it you need, in order:

1. Accept the terms on the HF page (shares your email with the maintainers).
2. **A separate bulk-access agreement with Software Heritage and INRIA** — datasets@softwareheritage.org. This is a correspondence, not a click.
3. Your own AWS credentials, and you pay the egress.
4. A standing commitment to re-sync: the dataset is regularly updated to enact validated removal requests, and accepting the terms means agreeing to update to the most recent usable version. v2.1.0 already dropped repos that opted out before 2024-04-09.

```python
from datasets import load_dataset
from smart_open import open   # pip install smart_open[s3]

ds = load_dataset("bigcode/the-stack-v2-train-smol-ids",
                  split="train", streaming=True)   # metadata streams fine

# content is a second, separate fetch per file:
#   s3://softwareheritage/content/{file['blob_id']}   (gzip, decode via src_encoding)
```

Useful metadata you get for free in the pointer layer, without ever fetching a byte of code: `detected_licenses`, `license_type`, `star_events_count`, `fork_events_count`, `revision_date`, `is_vendor`, `is_generated`, `language`, `length_bytes`. You can do a great deal of corpus design against 59 GB of metadata before committing to 3 TB of downloads. Do that first.

**The most valuable thing in this layer isn't the code.** It's the PR and issue history — diffs. A diff is a record of a *fix*: broken state, intervention, resolved state. It's the closest thing in the entire public corpus to a trajectory rather than an artifact. See *What is not on this list*.

---

## Layer 5 — Math and formal

| Dataset | Scale | Access | Note |
|---|---|---|---|
| **OpenWebMath** | 14.7B tokens | `HF` | Mathematical web pages, LaTeX preserved. |
| **Proof-Pile-2** | ~55B tokens | `HF` | AlgebraicStack + OpenWebMath + arXiv. Llemma's corpus. |
| **FineMath** | ~50B tokens | `HF` | HuggingFace's math filtering pass. |
| **AutoMathText** | ~200GB | `HF` | LM-autolabelled math text. |
| **Lean mathlib** | ~1.5M lines | `site` | GitHub. Not on HF. Formal proofs — **the typechecker is the point.** |

This layer is small — tens of billions of tokens against fifteen trillion — and punches far above its weight, for a reason given under *What is not on this list*.

---

## Layer 6 — Science and reference

| Dataset | Scale | Access | Note |
|---|---|---|---|
| **arXiv** | ~2.5M papers | `S3-$` |  **The only entry that costs real money.** `s3://arxiv/` is **requester-pays** — ~1.1 TB of PDFs at ~$0.09/GB ≈ $100 in egress. LaTeX source is available, which matters: it's structure, not just text. The Kaggle metadata dump is free if you only need titles/abstracts. |
| **PubMed Central OA** | ~5M articles | `S3` / `site` | NCBI FTP or AWS Open Data. Free. The open subset only. |
| **S2ORC / peS2o** | 136M papers | `HF` / `site` | `allenai/peS2o` is the cleaned pretraining-ready cut, on HF. Raw S2ORC needs a Semantic Scholar API key. |
| **Stack Exchange** | ~50M posts | `site` | archive.org dumps; SE moved distribution and the policy has shifted. Question, answers, votes — a human-graded correctness signal, free. |
| **Project Gutenberg** | ~70K books | `site` / `HF` | gutenberg.org canonical, HF mirrors exist. Everything out of copyright — which is to say, everything before roughly 1929. |
| **Wikidata** | ~100M items | `site` | dumps.wikimedia.org, ~130 GB bz2. **No incremental option** — full dump or the SPARQL endpoint for targeted queries. Structured, language-neutral, underused. |

---

## Layer 7 — Multilingual

| Dataset | Scale | Access |
|---|---|---|
| **FineWeb-2** | 1000+ languages | `HF` |
| **CulturaX** | 6.3T tokens, 167 languages | `HF-gated` |
| **MADLAD-400** | 419 languages | `HF` |
| **mC4** | 101 languages | `HF` |
| **CC-100** | 100 languages | `HF` |
| **NLLB / Flores-200** | 200 languages, parallel | `HF` |

Adjacent, and more useful for a retrieval system than any of the corpora in Layer 7: **BGE-M3**, **multilingual-e5**, **LaBSE** — ~100 languages in one shared vector space, no translation step required.

---

## Layer 8 — Post-training

Where the models actually become useful. Note the scale collapse: this entire layer is a few gigabytes.

| Dataset | Type | Access |
|---|---|---|
| **FLAN / Super-NaturalInstructions** | Instruction, academic tasks reformatted | `HF` |
| **Self-Instruct / Alpaca** | Instruction, model-generated | `HF` |
| **OpenAssistant** | Instruction, human-written, multilingual | `HF` |
| **OpenHermes** | Instruction, aggregated | `HF` |
| **Tulu 3** | AI2's full open post-training pipeline — the best public reference | `HF` |
| **Anthropic HH-RLHF** | Preference, helpfulness/harmlessness | `HF` |
| **UltraFeedback / HelpSteer / Nectar** | Preference | `HF` |
| **OpenThoughts / OpenR1** | Reasoning traces | `HF` |

Every row is ungated and streamable. The most consequential layer in the stack is also the easiest to obtain.


---

## Getting it

Layers 0–8 are organised by *what the data is*. *Getting it* cuts the same material by *whether you can have it*, which turns out to be the more actionable axis.

### The five tiers

| Tier | What | Who's in it |
|---|---|---|
| **1. Stream it now** | HF-native, ungated | The entire modern web stack (FineWeb family, DCLM, RedPajama-v2, Nemotron-CC, GneissWeb), all math, all post-training, MADLAD, mC4, CC-100, NLLB, Stack-Edu, SwallowCode, peS2o, C4, OpenWebText |
| **2. Click, then stream** | HF, gated | Dolma, CulturaX, The Stack v1 |
| **3. Pointers only** | HF, no content | The Stack v2 and StarCoder2Data. Metadata streams; code requires an SWH/INRIA agreement |
| **4. Elsewhere** | S3 or project site | Common Crawl, CC webgraph, Wikidata, Wikipedia, Stack Exchange, Lean mathlib, all wordnets, PubMed Central, Gutenberg |
| **5. Costs or gone** | — | arXiv (requester-pays, ~$100). BabelNet, Penn Treebank, FrameNet, BNC (licence/money). Books3, the full Pile, MassiveText (gone) |

Tier 1 is roughly two-thirds of the useful mass. Tiers 3–5 are where the schedule slips.

### Incremental retrieval, by tier

**Tiers 1–2 (HF).** Three mechanisms, cheapest first:

1. **Take a subset config.** Most large datasets ship pre-made samples that nest — FineWeb-Edu's `sample-10BT` ⊂ `sample-100BT` ⊂ `sample-350BT`, plus one config per CC crawl. Prototype small, scale up without rewriting your sampling.
2. **Stream.** `load_dataset(..., streaming=True)` returns an IterableDataset that pulls parquet row groups over HTTP as you iterate. Nothing touches disk.
3. **Range-request with pushdown.** Parquet footers carry row-group statistics, so DuckDB fetches only the groups that can satisfy your predicate:
   ```sql
   SELECT url, text FROM 'hf://datasets/HuggingFaceFW/fineweb-edu/sample/10BT/*.parquet'
   WHERE score > 4.5;
   ```
   Or `snapshot_download(..., allow_patterns="data/CC-MAIN-2024-10/*")` for whole files.

Install `hf_xet` first — these repos are on the Xet backend, which does content-defined chunking and dedups rather than refetching whole files.

**The streaming caveat:** don't stream a full default config end-to-end. That's TB over HTTP with no resume, and a crash at 80% means starting over. Streaming is for exploration and one-pass filter-and-write. Anything you'll read twice, land on local disk. HF's own `datatrove` does task-level checkpointing for exactly this reason.

**Tier 3 (Stack v2).** Metadata streams like any HF dataset. Content is a per-file S3 fetch against `s3://softwareheritage/content/{blob_id}` and needs the agreement. Do your filtering against the 59 GB of metadata first; only then fetch.

**Tier 4 (elsewhere).** Each has its own mechanism, and they're all better than "download everything":

- **Common Crawl** — the columnar URL index (~200 GB/crawl) resolves URL/domain/timestamp to WARC offsets; range-request just those. `cdx` API for one-off lookups.
- **Wikidata** — no incremental. Full dump, or SPARQL for targeted queries.
- **Wordnets** — `wn.download('oewn:2024')`, per project.
- **PubMed Central / Gutenberg** — per-article and per-book fetches are native.

**Tier 5 (arXiv).** `--request-payer requester` on `s3://arxiv/`. Budget it. If you only need metadata, the Kaggle dump is free.

### The pattern

Notice what's consistent across every tier: **the metadata is always cheap and always separable from the content.** CC has its URL index. The Stack v2 is *nothing but* an index. Parquet has footers. FineWeb-Edu carries a `score` column.

Which means the correct order of operations is always the same — pull the index, decide against the index, fetch only what survived. The people who blow their budget here are the ones who fetch first and filter second.

---

## The whole thing, sized

| Layer | Size |
|---|---|
| FineWeb (all 15T tokens) | 44 TB |
| The Stack v2 (train cut) | ~3 TB |
| Multilingual (CulturaX, MADLAD) | ~5 TB ≈ |
| Science (arXiv, PMC, peS2o) | ~1 TB ≈ |
| Wikipedia + Wikidata | ~0.3 TB |
| Math + formal | ~0.2 TB ≈ |
| All wordnets, all languages | ~0.5 GB |
| All post-training data ever released | ~0.05 TB |
| **Total** | **≈ 55 TB** |

Everything that has ever trained a frontier model — the entire public inheritance, from the Brown Corpus to Tulu 3 — fits on four hard drives with room left over.

Though "fits" and "obtainable this afternoon" are different claims. Of that 55 TB: roughly 50 TB is Tier 1 and you could start pulling it in the next ten minutes; ~3 TB is The Stack v2, which is a correspondence with INRIA before it's a download; ~1 TB is a $100 arXiv bill; and the most historically important item on the list, Books3, is simply gone.

That is the real finding, and it survives the caveats. The corpus was never the hard part.

---

## What is not on this list

Every dataset in Layers 0–8 is a record of **outputs, not processes**.

A textbook is the polished residue of someone's thinking; the thinking is gone. GitHub has the commit that worked; the four hours of confusion that produced it were never written down. Papers report the experiment that succeeded. The web is an archive of conclusions. The corpus that would matter most — trying, failing, noticing, correcting — does not exist, because nobody records that.

And what's scarce was never tokens. It's **verification**. Look at which layers punch above their weight: Lean (typechecks), code (compiles, tests pass), math (has an answer), Stack Exchange (has votes). Every one has a ground-truth checker, which is what lets you *generate* data instead of collecting it. Prose has no checker — which is exactly why the FineWeb-Edu threshold decision came down to a human picking the number 3 and accepting worse HellaSwag in exchange.

So: the finite set is assembled. It's public. It's 55 terabytes. And it wasn't enough.

You asked for finite. But generality is precisely the capacity to handle what the corpus didn't contain. A fixed corpus is a snapshot of what was already known and already written down. You can't read your way to the frontier, because the frontier is defined as the place where the reading runs out.

---

*Figures marked ≈ are derivations rather than published numbers. Everything else is sourced from dataset cards, papers, or the maintaining organisation.*

*Access status is the most volatile column in these tables — it changes by litigation, not by release cycle. The Pile, Books3, and the Stack v1→v2 shift from content to pointers all happened for legal reasons, not technical ones, and there is no reason to think that's finished. Verify against the card before you plan around any row here.*
