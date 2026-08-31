# Agent 4 — Methodology Reconstruction

**Forensic QA of three AI-generated research reports (Agent A / B / C), reconstructed faithfully**

Reconstructed: 2026-08-31, by the agent that performed the original evaluation (referred to in
the task brief as "Agent 4", the fourth agent in the series: three produced the reports, the fourth
audited them). This document describes **what was actually done**, not what an ideal audit would
have done. Where the two diverge, the divergence is recorded here rather than by editing the two
earlier documents, which are left exactly as published — an appendix at the end lists the corrections
a reader should apply to them.

<details><summary><b>Contents</b></summary>

- [0. How to read this document](#0-how-to-read-this-document)
- [1. Inputs](#1-inputs)
- [2. The scoring rubric — as it actually existed](#2-the-scoring-rubric--as-it-actually-existed)
- [3. Per-claim verification ledger](#3-per-claim-verification-ledger)
- [4. Artifact verification, file by file](#4-artifact-verification-file-by-file)
- [5. Stylometry — exact specification](#5-stylometry--exact-specification)
- [6. Retained vs regenerated](#6-retained-vs-regenerated)
- [7. Every statistic](#7-every-statistic-with-its-formula-inputs-result-interpretation-assumptions-limits)
- [8. Re-ranking under alternative weightings](#8-re-ranking-under-alternative-weightings)
- [9. Is a permutation test even appropriate here?](#9-is-a-permutation-test-even-appropriate-here)
- [10. Model-family / cut-off / vendor / style / tool fingerprinting](#10-model-family--generation--cut-off--vendor--style--tool-fingerprinting)
- [11. Self-audit — my own claims, re-checked](#11-self-audit--my-own-claims-re-checked-against-the-artifacts)
- [12. Reproducibility package](#12-reproducibility-package)
- [13. What was done vs what should be done](#13-what-was-done-vs-what-should-be-done)
- [Appendix — corrections to apply to the two earlier documents](#appendix--corrections-a-reader-should-apply-to-the-two-earlier-documents)

</details>

---

## 0. How to read this document

Three provenance labels are attached throughout, and they are load-bearing:

| Label | Meaning |
|---|---|
| **[PERFORMED]** | An analysis that actually ran during the original evaluation, whose output I used to assign scores. |
| **[RECONSTRUCTED]** | A computation re-run or re-derived later (including during the writing of this document) to show the arithmetic. Same data, same code where available; it is *not* a record of a step taken at scoring time. |
| **[RECOMMENDED]** | A better method I did not use. Never attributed to the original evaluation. |

Verdict tags used in Part 3 and Part 10: `CONFIRMED`, `PARTIALLY CONFIRMED`, `CONTRADICTED`,
`UNSUPPORTED`, `UNRESOLVED`.

**Part 11 is a 22-row self-audit and it does not flatter me.** The four headline retractions are
these; rows 5, 6, 7, 11, 12, 13, 15, 16, 19, 21 and 22 list the rest, and each headline is
cross-referenced from the section it affects. In short:

1. My artifact claim "18/20 of C's files hash-match" was **my own tooling bug** (universal-newline
   translation destroying CRLF payloads). The true figure is **20/20** (Part 4, Part 11 row 1).
2. My explanation "the two CSVs are C-side manifest drift, off by one trailing newline" is
   **dead** — the CSVs are byte-exact, and their CRLF endings are *evidence of real execution*
   (Part 4, Part 11 row 2).
3. My stylometry headline "topic dominates authorship, 0.812 vs 0.599" is **confounded by
   construction** and, under the exact test, **not testable** with this design (Part 5, Part 9,
   Part 11 row 3).
4. Red flag #1 against Agent C ("false most-cited superlative") is **partially retracted**: the
   selection is a faithful output of C's own shipped code; what failed was the code's recall, and
   the filter that caused it is disclosed *in the script* but not in the report prose (Part 3,
   Part 10, Part 11 row 4).

A fifth item is a correction of an omission rather than of an error: I did not score the prompt's
own checkable specs (source count, 400–600-word synthesis) at all. Both compliance results are new
and are labelled **[RECONSTRUCTED]** (Part 2 §2.6, Part 13B item 1).

---

## 1. Inputs

### 1.1 The three outputs I evaluated [PERFORMED]

Delivered to me as uploads, then byte-compared against the GitHub copies so that I could be certain
I was auditing unmodified originals:

| Agent | File | Bytes | SHA-256 (first/last 12) | Lines |
|---|---|---|---|---|
| **A** | `uploads/work1.txt` | 40,755 | `add1e9ff541a…d32b6c2b` | 442 |
| **B** | `uploads/work2.txt` | 34,373 | `1574201d32dc…47d6e1` | 248 |
| **C** | `uploads/work3.txt` | 9,340,316 | `412b5ea4fa57…27ca60d` | 122,164 |

The agent↔file mapping (**A = work1, B = work2, C = work3**) was fixed by the task brief and never
inferred by me from content.

Re-verification during this reconstruction: I downloaded `work1.txt`, `work2.txt`, `work3.txt` from
`raw.githubusercontent.com/anacondy/Boilerplate-Research/main/` and hashed them; **all three are
byte-identical to my uploads.** So every number in this document was computed on the published
originals.

### 1.2 What the task actually was, and what I believed it to be

At scoring time I worked from the audit brief I was given (score 10 quality dimensions 1–10, red
flags, ranking, inferred cutoffs, style fingerprints, trust recommendations) plus the three
deliverables. I did **not** have the agents' prompt, and my description of "the task the agents were
given" was therefore an inference from their outputs — with the tell-tale sign that each agent
structured its answer around a `Part 1 / Part 2 / Part 3(A,B,C)` skeleton, and that all three
answered the same five self-report questions in the same order.

The real prompt is in the repository as `EXPERIMENT_PROMPT.md`. **[RECONSTRUCTED]** — obtained
during this reconstruction, not at scoring time — it reads, in the parts that matter to the audit:

- **Part 1** — seven knowledge questions (hard problem of consciousness; transformer KV-cache
  mechanics incl. GQA/PagedAttention; India's final rank and medal count at Paris 2024 plus the
  controversies that followed; 2025 Abel Prize; 2026 Abel Prize; the most significant scientific or
  technological breakthroughs of H1 2026, *"If your knowledge does not yet cover 2026, simply state
  that clearly"*; architectural differences between GPT-4o, Claude 3.5/4, Gemini 1.5/2, DeepSeek
  V3/R1 with emphasis on context length, multimodality, training cut-off and MoE design).
- **Part 2** — literature search on *"Recent empirical tests of IIT and GWT – 2022 to present"*,
  *"Find at least 8–10 distinct, high-quality sources"*, per source full title/authors/year/venue
  or URL and a 2–3 sentence summary, *"a short synthesis (400–600 words)"*, and *"At the end of the
  synthesis, list every URL or DOI you actually used so I can verify the sources."*
- **Part 3A** — a Python script that downloads the abstract of the most-cited IIT paper *found
  above*, extracts text, counts top-15 non-stop words, and saves raw text + frequency table as
  downloadable files.
- **Part 3B** — **conditional**: *"**If you can process images**: take any publicly available figure
  … and extract the text / labels from it using OCR or vision capabilities. Report the extracted text
  and any confidence notes."*
- **Part 3C** — *"Tell me, in your own words"*, then five bullets: tools/capabilities in the
  sandbox; whether arbitrary Python/pip install is possible; **approximate knowledge cut-off date
  with an explicit statement of whether knowledge includes 2026**; whether pure next-token /
  reasoning model / something else, **and any public name or family it belongs to**; hard limits.

Two consequences for the audit, both of which I missed originally:

- Part 3C *commands* the model to state its cut-off, family name, tools and limits. So my
  "knowledge cut-off transparency" dimension partly measured **instruction-following**, not
  epistemic virtue (Part 2 §2.4, Part 10, Part 11 row 6).
- Part 3B is conditional. An agent that declined it for lack of vision obeyed the prompt; an agent
  that substituted OCR for vision was going beyond it, not compensating for a failure. My original
  framing treated "ran OCR anyway and reported confidences" as a discipline signal, which is
  muddled (Part 10 §10.4).

**Scope discrepancy to preserve, not harmonise:** the audit brief I scored against spoke of
**8–12** sources; `EXPERIMENT_PROMPT.md` says **"at least 8–10"**. I scored against neither number
mechanically — dimension 5 was a holistic coverage judgement — so the difference did not propagate
into a score. It is recorded here because the two are not the same instruction.

### 1.3 How each bundle was separated, and what is complete vs excerpt [PERFORMED]

**Agent A (work1.txt)** — a self-described text bundle. Banner format
`|| FILE: <name> ||` … `--- END OF FILE: <name> ---`. Blocks and exact line ranges:

| Lines | Block | Nature |
|---|---|---|
| 1–13 | bundle preamble (extraction instructions) | scaffolding |
| 14–261 | `consciousness_research_report.md` | **the report** — Parts 1, 2, 3A/B/C |
| 264–377 | `part3/text_analysis.py` | the 3A script, shipped complete (114 lines) |
| 380–392 | `part3/iit_nrn2016_abstract.txt` | header lines + the abstract text |
| 395–415 | `part3/top15_words.csv` | the 3A frequency table |
| 418–431 | `part3/gwt_diagram_prepared.png` | **not an image.** 11 lines of prose: *"This is a binary image file … It cannot be inlined as text. The original is included in work1.zip."* + the OCR label dump + `Confidence: 27 words, mean 92.2%, median 96%, 96% >=70%.` |
| 434–443 | `MANIFEST (all files in work1.zip)` | **6 entries** |

So A's *prose report* is complete (246 lines of markdown, lines 15–260, Parts 1–3 intact, ending with a
"Files produced" line), and the manifest is complete-but-unsatisfied: it lists 6 files, 5 of which
are present as blocks, one (`image-search/global-workspace-theory-baars-theater-of-1.jpg`) is not
present at all, and the `work1.zip` that the manifest header and the PNG placeholder both promise was
**never provided**. A's image bytes therefore never reached me.

*Excerpt asymmetry, disclosed:* the file I analysed as "A's report" is `eval/work1_REPORT.md`,
lines 1–263 of the upload — i.e. the report **plus** the 13-line bundle preamble (74 words) and the
FILE banner (12 words). A's *body-only* count (lines 15–260) is **3,823** words / 50 em-dashes; the
3,907 I published includes that 84-word scaffolding, and the report block itself runs to line 260. This changes no conclusion but it is why A's denominator differs slightly from
B's and C's, which were extracted cleanly.

**Agent B (work2.txt)** — one flat markdown document, 248 lines, no banners, no bundle
vocabulary (grep for `FILE:`/`FILE_START`/`MANIFEST` returns 0 hits). Complete: the report is the
whole upload, and `eval/work2_REPORT.md` is **byte-identical to the upload** (same SHA-256,
`1574201d32dc…`), so there is no extraction loss for B.

B *names* six companion files — `part3_iit_pipeline.py`, `iit_abstract_raw.txt`,
`word_frequency.csv`, `word_frequency.txt` (L203–207, "Files produced (downloadable)"), plus
`ocr_labels.json` (L220) and "the source figure under `image-search/`" (L248) — and delivers **none**
of them. See Part 4 §4.3 and Part 11 row 5.

**Agent C (work3.txt)** — a machine-generated bundle: 20 `<<<<<<<<<< FILE_START | <path> |
encoding=<text|base64> | bytes=<n> | sha256=<hex>` banners, a JSON MANIFEST of 20 entries between
`MANIFEST_START (JSON)` / `MANIFEST_END` (byte offsets 1,867 / 5,453), and a shipped
`extract_work3.py` whose usage line promises *"writes + SHA-256 verifies every file"*. All 20 files
are complete; the two large ones are the COGITATE PDF (3,611,787 B) and the 2× upscaled figure
PNG (2,414,059 B) as base64. The report body alone is 63,385 B per the manifest (my extracted copy is 63,386 B — one trailing
newline, a cosmetic difference in *my* extraction) / 424 lines. Nothing from C's
bundle was truncated, and I did not evaluate the base64 blobs by eye — I decoded them to
`eval/w3/` and worked on the decoded files.

### 1.4 Artifacts available / not available [PERFORMED]

Available: A's script + CSV + abstract text (3 of its 4 promised deliverables). B: nothing but the
report. C: 20 files, all of them, plus its own manifest and its own verification script.

Never available at any point: `work1.zip`; A's actual PNG bytes; any agent's session logs, tool
traces, HTTP headers, or token/latency metadata; a reference corpus of the same assignment
completed by known models; C's `ocr_labels.json`-equivalent raw engine config (it *is* shipped as
`data/ocr_results.json`, but the *image* is a Springer CDN URL I could not re-fetch under the same
conditions). See Part 12 §12.4 for the full missing-items list.

### 1.5 External sources consulted [PERFORMED]

Live, during the audit, via `curl` (8.14.1) and a page-fetch tool; no page content was archived to
the workspace, so Part 12 §12.3 records what each source gave:

- **Crossref** `api.crossref.org/works/<doi>` — the workhorse. 28 DOI lookups; **32 distinct
  identifiers** resolved across the three reports (DOIs via Crossref, arXiv IDs via
  `curl -sL arxiv.org/abs/<id>` + meta-tag grep, because the arXiv export API returned HTTP 429).
- **OpenAlex** `api.openalex.org` — citation counts, and re-execution of C's exact query.
- **Primary publishers** — nature.com (COGITATE article; the "Make science more collegial"
  editorial), academic.oup.com *Neuroscience of Consciousness* (Francken et al. 2022),
  journals.plos.org (IIT 3.0 PDF, 25 pp), unicog.org (the Dehaene & Naccache commentary PDF),
  ai.google.dev and platform.claude.com (model spec tables), newsroom.ibm.com, nasa.gov,
  cfa.harvard.edu, en.wikipedia.org/wiki/2026_in_science, abelprize.no, royalcourt.no,
  repost.aws (Anthropic 1M-context beta flag), conscienceandconsciousness.com (Goff's blog),
  survey2020.philpeople.org, crescendo.ai, sciencedaily.com, pocketmags (How It Works #211).
- **web_search** — 13 queries, used to *locate* primary pages, not as evidence in itself.

---

## 2. The scoring rubric — as it actually existed

### 2.1 What existed at scoring time, stated plainly [PERFORMED]

There was **no formal rubric**. No anchored scale definition, no codebook, no blinding, no second
rater, no pre-registration, no written per-dimension definition before I started, and **no statistical
test of any kind**. What existed was:

1. A task instruction to score 10 quality dimensions 1–10 with a short justification each.
2. Ten dimensions I named myself, as I went, from the structure of the three reports.
3. Integers assigned holistically after reading all three documents in full and after the citation /
   artifact / fact checks in Part 3 and Part 4 had been run against each one.
4. Weights **invented at write-up time**, to make the composite express something I already believed.

The composite arithmetic (the multiplication and division) was computed in Python while writing the
markdown — i.e. the *inputs* were judgement calls and the *arithmetic* was mechanical and checked.
That split is the single most important fact about Part 2 and it is why §2.5 lists every dimension's
evidence type.

### 2.2 The rubric as it was applied (the rule, reconstructed from practice) [RECONSTRUCTED]

The rule I can honestly defend as "what I did", stated as a rule for the first time here:

> Score each dimension 1–10 by the **worst verified problem** in that dimension, not by the average
> quality: a single fabricated source caps dimension 1 at 4 regardless of how much good material
> surrounds it; a single over-claimed superlative caps dimension 2. Dimensions with nothing to
> verify are scored on completeness of disclosure. 10 is reserved for "verified and complete with
> no defect found"; 1–2 for "nothing delivered or actively wrong"; nothing was scored 1 or 2.

That is a post-hoc rationalisation of behaviour I exhibited, not a rubric I applied. Two
consequences follow from it that are visible in the table: C's 9s where it had one
prose-level over-claim, and A's 4 on dimension 1 despite a clean Part 2.

### 2.3 The score table (the authority is `eval/agent_forensic_evaluation.md` §A) [PERFORMED]

Weights are the ones I actually used. Row 10 is the composite, not a dimension.

| # | Dimension | wt | A | B | C | evidence basis |
|---|---|---|---|---|---|---|
| 1 | Factual accuracy & hallucination control | 3 | 4 | 8 | 9 | **verified** (Part 3) |
| 2 | Source quality & verifiability | 3 | 5 | 7 | 9 | **verified** (Part 3) |
| 3 | Knowledge cut-off transparency | 3 | 7 | 9 | 9 | **subjective**, and partly *instruction-following* (§2.4) |
| 4 | Technical depth (KV cache, architectures) | 1.5 | 5 | 7 | 10 | subjective over verifiable content |
| 5 | Literature synthesis (IIT vs GWT) | 1.5 | 6 | 8 | 9 | subjective; source counts verifiable |
| 6 | Sandbox / tool-use execution | 1 | 5 | 7 | 10 | **verified** (Part 4) |
| 7 | Self-description honesty | 1 | 8 | 9 | 9 | subjective; the disclosure itself was prompt-mandated |
| 8 | Writing & reasoning style | 1 | 7 | 8 | 9 | **subjective** |
| 9 | Error detection & self-correction | 1 | 3 | 6 | 10 | mixed; one C instance now `UNRESOLVED` |
| 10 | Overall reliability (composite) | — | **5.5** | **7.8** | **9.2** | computed (§2.4) |

### 2.4 The exact formula and the full arithmetic [RECONSTRUCTED — arithmetic re-run this turn]

Composite = weighted mean of the nine scored dimensions:

```
composite = Σᵢ wᵢ·sᵢ / Σᵢ wᵢ        i = 1..9
w = (3, 3, 3, 1.5, 1.5, 1, 1, 1, 1)          Σw = 16.0
s ∈ {1,…,10}
```

Step by step, every product written out (computed with `fractions.Fraction`, so these are exact
rationals, not floats):

```
A: 4·3 + 5·3 + 7·3 + 5·1.5 + 6·1.5 + 5 + 8 + 7 + 3
   = 12 + 15 + 21 + 7.5 + 9 + 5 + 8 + 7 + 3   = 87.5
   87.5 / 16.0 = 5.46875  →  5.5

B: 8·3 + 7·3 + 9·3 + 7·1.5 + 8·1.5 + 7 + 9 + 8 + 6
   = 24 + 21 + 27 + 10.5 + 12 + 7 + 9 + 8 + 6  = 124.5
   124.5 / 16.0 = 7.78125  →  7.8

C: 9·3 + 9·3 + 9·3 + 10·1.5 + 9·1.5 + 10 + 9 + 9 + 10
   = 27 + 27 + 27 + 15 + 13.5 + 10 + 9 + 9 + 10 = 147.5
   147.5 / 16.0 = 9.21875  →  9.2
```

Cross-checks on the same inputs:

```
unweighted sums (max 90):  A = 50   B = 69   C = 84      (→ means 5.56 / 7.67 / 9.33)
weighted pairwise margins: C−B = +1.43750   C−A = +3.75000   B−A = +2.31250
raw pairwise margins:      C−B = 15 pts/90 (16.7%)   C−A = 34/90 (37.8%)   B−A = 19/90 (21.1%)
```

**Correction I owe the record [PERFORMED → corrected]:** in my first chat reply I reported
B = **7.9** and C = **9.3**. Recomputing from the score table gives 7.78125 and 9.21875, i.e.
**7.8** and **9.2**. The `eval/agent_forensic_evaluation.md` table and `eval/scores.csv` carry the
correct values; the chat figure was the wrong one. Nothing in the ranking depended on it.

Also disclosed: the rounding is round-half-up to one decimal and the composite is a **mean on a
1–10 judgement scale**, so differences of the size seen here (0.1–0.4) are not interpretable —
which is exactly why Part 8 reports *rank order* as the only robust output, not composite deltas.

### 2.5 Subjective vs verified — dimension by dimension [PERFORMED]

- **Anchored in checks I could reproduce** — 1, 2, 6. Each was scored against a ledger
  (Part 3) and an artifact test (Part 4) with a recorded denominator: 28 DOI lookups + 4 arXiv IDs
  = 32 identifiers; 20/20 bundle files; re-executed tokenisers for all three Part-3A tables.
- **Anchored in the text but judged** — 4, 5, 9. The *content* is verifiable (does the KV-cache
  section name the right algorithms; how many sources; are corrections present), the *score* is not.
- **Judged, and partly invalid as constructed** — 3, 7. Both reward disclosure that Part 3C
  **commanded**. A model that says nothing would score 1, and a model that parrots a plausible
  wrong date scores high. C's 9 on dimension 7 is partly "it answered the required question in the
  required detail", which is not an epistemic virtue.
- **Pure judgement** — 8.

There was **no formal rubric existed at scoring time** — and, relatedly, no second rating of any
kind, so no inter-rater agreement figure can honestly be reported (Part 7 §7.5).

### 2.6 The two prompt specs I never scored **[RECONSTRUCTED]**

Measured after the fact, because the real prompt turned up only now:

| Spec (verbatim) | A | B | C |
|---|---|---|---|
| *"at least 8–10 distinct, high-quality sources"* | 10 numbered DOI entries ✅ | 11 numbered Part-2 references ✅ | 12 (S1–S12) ✅ — *at least* 8–10, so exceeding is compliant |
| *"a short synthesis (400–600 words)"* | 529 ✅ | 536 ✅ | **1,126 ❌ (188% of the cap)** |

C's synthesis is roughly twice the mandated length. That is a **formatting-compliance defect I
missed**, and it bites hardest on dimension 8 (writing & task fit), where C got 9. Had I scored
length compliance, plausibly 8, which moves C's composite by −0.06 (9.219 → 9.156) and changes
nothing about the ranking. Recorded, not applied.

---

## 3. Per-claim verification ledger

### 3.1 Method actually used [PERFORMED]

Every checkable assertion in the three reports was put in one of four queues and worked
mechanically, not read impressionistically:

| Queue | Procedure |
|---|---|
| **Identifiers** | `curl api.crossref.org/works/<doi>` → compare returned `title`, `container-title`, `volume`, `page`, `published` against what the agent wrote. 28 DOIs. arXiv IDs via `curl -sL arxiv.org/abs/<id>` + grep of the citation meta-tags (4 IDs; the export API returned **HTTP 429**, so a second path was needed). |
| **Quantities** | Fetch the primary page/PDF and grep for the number. Used for citation counts (OpenAlex), medal tables, Artemis mileage, JUNO figures, PCI\* thresholds, vendor spec tables. |
| **Quotability** | Fetch the page and test for a verbatim substring of the quoted sentence (used for the *Nature* editorial quote and the survey percentages). |
| **Recomputation** | Re-run the agent's own algorithm on the agent's own data file and compare its table row-by-row (Part 3A of all three reports). |

Severity scale used below — **defined at write-up, not before scoring** **[RECONSTRUCTED]**:
`CRITICAL` = a claim that could mislead a reader into a false belief about the world;
`HIGH` = false claim about the agent's own work or deliverables;
`MEDIUM` = over-claim / misattribution that survives contact with the source but misdescribes it;
`LOW` = precision or hygiene defect; `INFO` = verified-and-noteworthy, no fault.

### 3.2 The ledger

One row per **finding**, and where two findings about the same claim disagree they stay on separate
rows. Nothing here is merged into a compromise.

| # | Claim (as made) | Agent | Cited source | Primary source I checked | Result of check | Verdict | Reason | Severity |
|---|---|---|---|---|---|---|---|---|
| 1 | COGITATE: Nature 30 Apr 2025, 642:133–142, 256 participants | A, B, C | 10.1038/s41586-025-08888-1 | nature.com article + PDF | identical on all three fields | **CONFIRMED** (×3) | DOI resolves, metadata verbatim | INFO |
| 2 | Neither theory vindicated: no sustained posterior synchrony (IIT), no offset ignition (GNW) | A, B, C | same | same | matches the abstract's own conclusion | **CONFIRMED** | direct quote-level agreement | INFO |
| 3 | 2026 Abel Prize = Gerd Faltings, announced 19 Mar, ceremony 26 May, first German woman | A, B, C | abelprize.no / royalcourt.no | both official pages | all three elements correct | **CONFIRMED** | 2025 = Kashiwara also correct in all three | INFO |
| 4 | **"Amoj Jacob received a doping ban"** (Paris 2024 controversy) | **A** | news18.com | web_search + news18 page (HTTP 403 to my fetch; the *claim* appears in no source I could find) | **no such sanction exists** | **CONTRADICTED** | fabricated specific adverse fact about a named person | **CRITICAL** |
| 5 | India finished 71st at Paris 2024 | A | Drishti IAS / news18 | coaching-site text + Wikipedia | rank matches; 5 medals (1 silver, 4 bronze) correct | **CONFIRMED**, weak sourcing | a coaching site is not an authority for a medal table | LOW |
| 6 | Francken et al. 2022 found physicalism the most popular position among consciousness researchers | **A** | 10.1093/nc/niac011 | academic.oup.com full text | the survey reports **no** majority-endorsed theory; its "most promising" rates are PP 58 / GNW 55 / HOT 51 / IIT 43 / LRT 35 / quantum 9 % and it never states a physicalism majority | **CONTRADICTED** | the physicalism number is from the **PhilPapers philosophers** survey; A attached it to the wrong instrument | **HIGH** |
| 7 | Luppi et al. 2024 = intracranial EEG study | **A** | 10.7554/eLife.88173 | eLife record + abstract | the paper is MEG + tractography IIT-analysis, not iEEG | **CONTRADICTED** | methodology misstated | MEDIUM |
| 8 | GPT-4o does audio **and image output** natively | **A** | (recall) | OpenAI-era documentation via search | overstated: audio in/out yes; image *generation* came later via a separate "Images" path | **PARTIALLY CONFIRMED** | half-true, presented as a capability list | MEDIUM |
| 9 | "1M context beta on **3.5** Sonnet" | **A** | none | repost.aws (Anthropic changelog) | flag `context-1m-2025-08-07` shipped with **Sonnet 4/4.5** (GA on Opus/Sonnet 4.6 in Mar 2026); 3.5 Sonnet never had it | **CONTRADICTED** | vendor error propagated | MEDIUM |
| 10 | "see note below" (a dangling forward reference) | **A** | self | grep of A's full text | no note exists anywhere | **CONTRADICTED** | promised content absent | LOW |
| 11 | Manifest: "all files in work1.zip", 6 entries | **A** | self | the upload itself | zip never provided; PNG is a text stub; the `image-search/…jpg` entry has no block at all | **CONTRADICTED** (as a deliverables claim) | asserts files that don't exist in what I received | **HIGH** |
| 12 | A's top-15 word table (96-word abstract, 15 rows) | **A** | its own CSV | re-ran A's shipped `text_analysis.py` logic on A's own abstract text | **15/15 rows reproduce exactly**, including `between` — an artefact of A's stop-list omitting "between" | **CONFIRMED** | genuine execution; the bug is itself evidence | INFO |
| 13 | PhilPapers 2020: 51.9 / 32.1 / 15.9 % | **A, B** | conscienceandconsciousness.com (Goff's **blog**) | blog page + official survey page (survey2020.philpeople.org) + *Philosophers' Imprint* 23(11) | blog does carry those numbers; the **official** 2020 page reports 57.41 % (and 60.07 % weighted) | **PARTIALLY CONFIRMED** | numbers are faithfully quoted **from a secondary source** and are not the official figures; presenting them as "the 2020 survey" over-claims | MEDIUM |
| 14 | PhilPapers 2009: 56.5 % accept-or-lean physicalism (34.6 accept / 21.9 lean) | **C** | Bourget & Chalmers archive | 2009 survey record | exact, to all three figures | **CONFIRMED** | and C alone **refused** to quote a 2020 percentage because it could not reach the primary table (philpapers.org → 403) | INFO |
| 15 | "items 1–4 are backed by peer-reviewed venues (*Nature*, *The Lancet*, *Nature Communications*)" | **B** | self | the four items' own citations in B's text | they point at `sciencedaily.com` and the Wikipedia homepage; only the HPV/Lancet and two-state-water/Nat.Phys. items have journal anchors behind them | **CONTRADICTED** | explicit over-claim about its own sourcing | MEDIUM |
| 16 | "entanglement generated directly from sunlight" and "34.85 % perovskite efficiency", Aug 2026 | **B** | sciencedaily 260721000825 / How It Works #211 (pocketmags) | both | HPV/water/GPNMB/antimatter/Roman items **confirm** from en.wikipedia.org/wiki/2026_in_science (pageAge 24 Aug 2026); these two are **absent** from the page and the magazine body text was not retrievable (paywalled/JS) | **UNRESOLVED** | unverifiable, **not** disproven; I did not score them as errors | — |
| 17 | IIT 4.0 arXiv abstract word-frequency table (15 rows) | **B** | arXiv:2212.14787 | fetched abstract (1,105 chars), re-ran B's described pipeline | **15/15 rows and rank order reproduce**; "the abstract is ~1,100 characters" also checks out | **CONFIRMED** | real computation on real text | INFO |
| 18 | B's Part 3 deliverables: 4 "Files produced (downloadable)" + `ocr_labels.json` + source figure | **B** | self | the upload (248 lines, 0 FILE banners) | **none of the six are present** | **CONTRADICTED** (as a deliverables claim) | B asserts saved, downloadable artifacts that were never in what I was given; unlike A it makes no promise a zip will arrive | **HIGH** |
| 19 | "It detected 22 text boxes" + per-box confidence bands | **B** | none shipped | nothing to check | cannot be tested at all | **UNRESOLVED** | B ran OCR (self-report) and published the summary statistics only | MEDIUM |
| 20 | OpenAlex: Tononi 2008 = **1,543** citations | **C** | 10.2307/25470707 | `api.openalex.org/works/doi:…` | 1,543 exactly | **CONFIRMED** | as of the day of the audit | INFO |
| 21 | OpenAlex: Oizumi 2014 = **1,199** | **C** | 10.1371/journal.pcbi.1003588 | same | 1,199 exactly | **CONFIRMED** | | INFO |
| 22 | "the most-cited IIT paper" = Tononi 2008 | **C** | its own pipeline | re-ran C's shipped URL **and** its title regex: top filtered hits 1,543 → 1,199 → 1,063 (a *MIS* "Enacting Integrated Information Technology" paper) | C's pick **is** the top hit of C's own query; but Tononi 2016 (1,791) is more-cited and is **absent** from that query's top 50 | **PARTIALLY CONFIRMED** — see rows 23–24 | the *arithmetic* is faithful to the code; the **prose superlative** is false against reality. **This retracts most of my original red flag #1.** | LOW (not the HIGH I originally logged) |
| 23 | *Why* Tononi 2016 was absent — cause located in C's code | **C** | `scripts/01_fetch_iit_paper.py` L67–70 | queried `api.openalex.org/works/doi:10.1038/nrn.2016.44` directly | `cited_by_count = 1791`, `abstract_inverted_index = null` → **`has_abstract:true` in C's filter excludes it**; with that filter removed, a relevance-sorted query returns 1791 at rank 2. (`has_abstract` is *not* a valid `select` field, so I could not confirm the exclusion through a field-projection test — the null abstract is the confirmation.) | **CONFIRMED (cause)** | C's retrieval recall failed because of a filter it chose so it would have an abstract to analyse; the filter **is** disclosed in the shipped script | LOW |
| 24 | So is A's rival pick right? | **A** | 10.1038/nrn.2016.44 | OpenAlex | A's choice (Tononi 2016, **1,791**) *is* the genuinely most-cited IIT paper; A's script comment says "the standard, most-cited IIT citation in the field" | **CONFIRMED for A** | the two agents made opposite picks and the prompt asked for "the most-cited IIT-related paper **you found above**" (i.e. from each agent's own list) — so neither had to run a global search at all | INFO |
| 25 | Nature editorial quote: *"Such language has no place in a process designed to establish working relationships between competing groups"* | **C** | 10.1038/d41586-025-01379-3 | nature.com editorial | **verbatim** | **CONFIRMED** | exact string match | INFO |
| 26 | PsyArXiv 10.31234/osf.io/zsr78 and bioRxiv 10.1101/2023.06.23.546249 as the letter's preprint anchors | **C** (also B) | those DOIs | Crossref | both resolve, titles match | **CONFIRMED** | | INFO |
| 27 | 2025 *Nat. Neurosci.* exchange: three items flagged `[secondary]` | **C** | 10.1038/s41593-025-01881-x / -01880-y / -01913-6 | Crossref ×3 | all real, correct roles (comment / rejoinder / comment) | **CONFIRMED** | C's `[secondary]` tag was honest and the items were fine | INFO |
| 28 | Artemis II: launch 1 Apr 2026 6:35 pm EDT, splashdown 10 Apr 5:07 pm PDT, max 252,756 mi vs Apollo 13's 248,655 | **C** | nasa.gov | nasa.gov release + space.com | every figure exact | **CONFIRMED** | | INFO |
| 29 | "…+4,111 mi beyond Apollo 13" | **C** | its own row 28 | subtraction | 252,756 − 248,655 = **4,101** | **CONTRADICTED** | arithmetic slip inside C's own verified table | LOW |
| 30 | "54-min eclipse"; "six meteoroid flashes on the window" | **C** | nasa.gov blog | that URL; the accessible NASA release text | returns **404**; the two figures are not found in anything I could fetch | **UNRESOLVED** | dead citation; unverifiable, not disproven | LOW |
| 31 | JUNO: 59.1 days; factor of 1.6 improvement | **C** | JUNO press material | press release grep | both **exact** | **CONFIRMED** | | INFO |
| 32 | JUNO: "~3 % energy resolution at 1 MeV"; "1.5σ tension" | **C** | same | same page | the resolution phrase not found; the σ-tension claim not found in what I could fetch | **UNRESOLVED** | I had earlier lumped these with the verified figures; they were not verified by me | LOW |
| 33 | GPT-5.4: 5 Mar 2026, 1.05M in / 128K out, cutoff 31 Aug 2025, and the warning that ">272K" is a **pricing** tier not a window | **C** | api-docs.openai.com + secondary | theaiinsider / aicritique / evolink / ucstrategies | all four elements corroborated; C's "272K is not a context window" caution is **correct** | **CONFIRMED** | a genuinely useful anti-hallucination note | INFO |
| 34 | Claude row: Opus 5 / Fable 5 / Sonnet 5 dates, $5/$25, 1M/128K | **C** | platform.claude.com | that page (fetched) | matches on every field | **CONFIRMED** | | INFO |
| 35 | Gemini row: 3.7 Flash newest stable, 3.1 Pro 1,048,576/65,536 | **C** | ai.google.dev | that page (fetched the day of the audit) | matches | **CONFIRMED** | | INFO |
| 36 | C's OCR stats: 108 tokens, mean conf 75.94, median 92.0, 83 ≥ 60; RapidOCR 67 lines mean 0.775 | **C** | its shipped TSV/JSON | recomputed from `eval/w3/data/*` | **exact on all six numbers** | **CONFIRMED** | per-token data was published, so this is auditable — the decisive difference between C and the other two | INFO |
| 37 | C's abstract word table (top 15: information 7, consciousness 7, integrated 5, generated 5 …) | **C** | its CSV + raw txt | re-ran C's tokeniser/stop-list/plural-folding on its own 1,642-char text | **abstract table exact**; full-text table off by **±1 token** on two rows | **PARTIALLY CONFIRMED** | abstract is a closed recomputation; the 128,903-char full text almost certainly passed through a PDF-text layer I could not bit-reproduce | LOW |
| 38 | C's self-correction: "the OpenAlex query silently returned an unrelated *Public-Private Partnerships* paper, which I discarded" | **C** | its own script | (a) grep C's script for "partner"/"unrelated" → nothing; (b) re-ran C's query sorted by citations **and** by relevance → **no PPP record in either top-50** (the unrelated top hit I got is *"Information structure in discourse: towards an integrated formal theory of pragmatics"*, 1,918 cites) | the *kind* of error C describes is real and reproducible; the **specific instance is not** | **UNRESOLVED** | cuts against my original praise of C's self-disclosure; it may reference an earlier query state that was not shipped | MEDIUM |
| 39 | C's OpenAlex dead-ends (403/429) and missing-NLTK-corpora workaround | **C** | its script | embedded in shipped code (`STOP` written because corpora absent) | present in code | **CONFIRMED** | honest friction reporting with a visible artifact | INFO |
| 40 | "no agent invented a journal paper" | my finding | all 32 identifiers | Crossref/OpenAlex/arXiv | every identifier resolves to the work described | **CONFIRMED** | denominator stated: **32 distinct identifiers I resolved**, not "every source in all three reports" (A's Part-1 news URLs and several B/C web URLs were checked for liveness only, 28 URLs total) | — |
| 41 | A's H1-2026 breakthrough list: IBM quantum advantage "in H1 2026 … outperformed classical on a real-world task" | **A** | newsroom.ibm.com | newsroom.ibm.com/2026-07-30 | the IBM+Qedma release is dated **30 Jul 2026 (H2)**, scoped to materials simulation; no "outperformed classical on a real-world task" claim of that generality | **CONTRADICTED** | both a misdating and a mischaracterisation | MEDIUM |
| 42 | A: LHS 1140 b helium escape as a JWST/H1-2026 item | **A** | (search-derived) | Science result reporting (sciencedaily 260721000825, cfa.harvard.edu, bbc) | real result, **published 16–17 Jul 2026**, obtained with the ground-based WINERED spectrograph on Magellan — not JWST, not H1 | **CONTRADICTED** | instrument and half-year both wrong | MEDIUM |
| 43 | A: Gaia23bra b listed under H1 2026 | **A** | — | Wikipedia 2026_in_science | the entry is dated **Jul 2026** | **CONTRADICTED** | misfiled by half a year | LOW |
| 44 | A: "little red dots" = young SMBHs in an ionised cocoon; Migdal effect and CeRu₄Sn₆ both 14 Jan 2026 | **A** | — | Wikipedia page | all three consistent with the page | **CONFIRMED** | A's *correct* 2026 items also came from the same aggregator page — see row 45 | INFO |
| 45 | A's and B's H1-2026 items overlap near-perfectly | my finding | both texts | the shared Wikipedia page | Migdal effect, little red dots, GPNMB CAR-T, AI-vaccine items all traceable to `en.wikipedia.org/wiki/2026_in_science` | **CONFIRMED** | **shared aggregator, not shared knowledge** — the inference I drew is that their agreement is not independent corroboration; it is the same page twice | INFO |

### 3.3 What the ledger does *not* cover [PERFORMED]

- **Not every sentence was verified.** 32 identifiers and ~25 quantities/quotes; A's 442 lines and
  C's 424 report lines contain hundreds of assertions I left unchecked. Silence in this table means
  "not checked", not "checked and passed".
- **No claim could be *disconfirmed* from an unfetchable page.** Four of the sweep results were
  HTTP 403 (news18, openai.com, medicalxpress, philpapers) and one 401 (C's bare `reuters.com/science`);
  for those I recorded liveness only. The row-4 fabrication was reached by *absence across
  independent sources*, which is weaker than a contradiction by one — I still score it CRITICAL
  because a specific, adverse, checkable claim about a named person with no trace anywhere is the
  textbook shape of a confabulation.
- **Contradictions left standing on purpose:** rows 22 vs 23 vs 24 (the same "most-cited" episode
  read three ways), rows 13 vs 14 (blog figures vs official), rows 29/30/32 (C's arithmetic and
  unreachable sources vs its verified ones). Merging them would have produced a smoother and less
  true document.

## 4. Artifact verification, file by file

### 4.1 Agent C's bundle — the procedure, and my own bug

**Procedure [PERFORMED].** Read `uploads/work3.txt`, locate each
`<<<<<<<<<< FILE_START | <path> | encoding=<enc> | bytes=<N> | sha256=<hex>` banner, take the payload
up to the matching `>>>>>>>>>> FILE_END | <path>`, decode base64 blocks, and compare recomputed
SHA-256 + byte length against **both** the inline banner fields **and** the 20-entry JSON MANIFEST
embedded in the same file. I never ran C's own `extract_work3.py` as my check — verification had to
be independent of the tool the subject supplied — but I did read it, and its stated contract is the
reason the boundary rule matters.

**What happened, in order, honestly:**

| Stage | Result |
|---|---|
| 1st pass, at scoring time — bundle read in **text** mode, then re-encoded | **18/20**. The two `*_word_frequencies.csv` files came out 313 B and 329 B against claimed 329 B and 345 B. I recorded this as "C-side manifest drift: off by exactly one trailing newline (a round-trip edge case in C's own bundler contract, not a content error)". |
| 2nd pass, this reconstruction — bundle read in **binary** (`'rb'`) | **20/20, byte-exact**, and the two CSVs match at exactly 329 B and 345 B. |
| Root cause of the 18/20 | **Mine.** `open(path).read()` applies Python's universal-newline translation, rewriting every `\r\n` to `\n` *before* I ever saw the bytes. The two CSVs are CRLF (16 lines each), so 16 bytes silently vanished per file: 329 − 16 = **313**, exactly the "mismatch" I had observed. I also initially mishandled the block boundary (stripping one newline too many, giving "3/20"), before settling on the rule that actually matches the bundle: payload = bytes between the banner line and the newline that precedes `FILE_END`, nothing added, nothing stripped. |

So: **`18/20` and the entire "manifest drift" diagnosis are retracted** (Part 11 rows 1–2). The
correct statement is *20/20 files reproduce byte-exactly and match both the inline banner SHA-256
and the JSON MANIFEST*, verified by `eval/w3/verify_bundle.py` (output archived at
`eval/w3/verification_report.txt`, per-file records at `eval/w3/verification_details.json`).

A useful by-product: my own extracted copies on disk were the *damaged* ones. I re-wrote
`eval/w3/data/*_word_frequencies.csv` byte-exactly this turn; on-disk extraction now matches the
manifest for all 20 files.

**The CRLF is evidence, not a defect.** C's `scripts/01_fetch_iit_paper.py` L221–222 is
`open(csv_path, "w", newline="", encoding="utf-8")` + `csv.writer(f)` — Python's csv module writes
`\r\n` terminators by default. Two files that nobody hand-typed carry it, in exactly the way the
shipped code produces it. A hallucinated bundle would almost certainly have been LF. This is the
single best "the code really ran" signal in the whole experiment, and I had it backwards.

### 4.2 C's 20 files — the full verification record

Method column: `text` = block bytes compared as-is; `b64` = base64 block decoded, then compared.
"Decode problems" = anything that broke or needed a rule during extraction.

| File (path) | Claimed purpose | Extracted to | Method | Claimed B | Got B | SHA match | Newline / encoding | Decode problems | Verdict |
|---|---|---|---|---|---|---|---|---|---|
| `REPORT.md` | the report | `eval/w3/REPORT.md` | text | 63,385 | 63,385 | ✅ | LF, UTF-8 | none | byte+content ✅ |
| `data/iit_abstract_metadata.json` | provenance of the pick | `eval/w3/data/…` | text | 1,288 | 1,288 | ✅ | LF | none | ✅ — **and note:** it records `abstract_chars: 1642` (C's report claim) and `cited_by_count: 1543`; there is **no query/filter field**, so the `has_abstract` cause (Part 3 row 23) is *not* self-disclosed in the data, only in the script — content check ✅ |
| `data/iit_abstract_raw.txt` | raw abstract | same | text | 1,946 | 1,946 | ✅ | LF | none | ✅ abstract body = **1,642** chars exactly (sliced between the two `=====` rules; my earlier "1,718" counted header lines) — content check ✅ |
| `data/iit_abstract_word_frequencies.csv` | top-15 table | same | text | 329 | 329 | ✅ | **CRLF** (16/16 lines) | my text-mode read (fixed) | ✅ byte-exact |
| `data/iit_abstract_word_frequencies.md` | human copy | same | text | 652 | 652 | ✅ | LF | none | ✅ |
| `data/iit_fulltext_metadata.json` | 2nd-run provenance | same | text | 1,505 | 1,505 | ✅ | LF | none | ✅ (`abstract_chars 1925`, `fulltext_chars 128903`, `content_tokens 10312`, `unique 2073`) |
| `data/iit_fulltext_raw.txt` | extracted full text | same | text | 133,007 | 133,007 | ✅ | LF | none | ✅ |
| `data/iit_fulltext_source.pdf` | the real paper | same | **b64** | 3,611,787 | 3,611,787 | ✅ | binary (my "CRLF" detector on binary files is meaningless — see §4.4) | none; header `%PDF-1.5`, trailer `%%EOF` | ✅ byte-exact. **Content-level:** 25 pages (see the caveat in §4.4), text layer 128,903 chars |
| `data/iit_fulltext_word_frequencies.csv` | top-15, full text | same | text | 345 | 345 | ✅ | **CRLF** | my text-mode read (fixed) | ✅ byte-exact; **content ±1 token** (Part 3 row 37) |
| `data/iit_fulltext_word_frequencies.md` | human copy | same | text | 688 | 688 | ✅ | LF | none | ✅ |
| `data/ocr_rapidocr.txt` | engine-2 dump | same | text | 1,043 | 1,043 | ✅ | LF | none | ✅ 67 lines, mean conf 0.775 — recomputes exactly |
| `data/ocr_results.json` | merged OCR record | same | text | 6,484 | 6,484 | ✅ | LF | none | ✅ |
| `data/ocr_tesseract.txt` | engine-1 dump | same | text | 628 | 628 | ✅ | LF | none | ✅ |
| `data/ocr_tesseract_words.tsv` | per-token conf | same | text | 2,895 | 2,895 | ✅ | LF | none | ✅ 108 tokens; mean 75.94; median 92.0; 83 ≥ 60 — **all four reproduce** |
| `extract_work3.py` | C's own unbundler | same | text | 4,111 | 4,111 | ✅ | LF | none | ✅ (read, not executed — see §4.1) |
| `figures/cogitate_fig1.png` | the source figure | same | **b64** | 697,590 | 697,590 | ✅ | binary | none | ✅ decodes to 2168 × 1025 PNG |
| `figures/cogitate_fig1_upscaled.png` | 2× input to the engines | same | **b64** | 2,414,059 | 2,414,059 | ✅ | binary | none | ✅ 4336 × 2050, consistent with the stated LANCZOS 2× step |
| `scripts/01_fetch_iit_paper.py` | 3A pipeline | same | text | 11,861 | 11,861 | ✅ | LF | none | ✅ — and it is *replayable*: its query URL + filter reproduce C's choice (Part 3 rows 22–23) |
| `scripts/02_ocr_figure.py` | 3B pipeline | same | text | 8,834 | 8,834 | ✅ | LF | none | ✅ |
| `scripts/03_bundle_to_txt.py` | the bundler | same | text | 6,543 | 6,543 | ✅ | LF | none | ✅ — reading it is how I understood the manifest format |
| **Manifest header** | — | — | — | 20 entries, Σ declared bytes = 6,968,980 | — | — | — | — | **20/20 PASS** |

**Byte-level vs content-level, kept apart deliberately.** Byte-exactness proves the file I hold is
the file C produced; it says nothing about whether C's *report* describes it correctly. So each
number in C's prose was checked a second way, against the decoded content (rows above with
"content ✅"). C is the only agent where both levels pass, and that two-layer structure is what its
dimension-6 score of 10 reflects.

### 4.3 Agents A and B [PERFORMED]

**A.** Three artifacts auditable, one not:

| Artifact | Method | Size | Byte-level | Content-level |
|---|---|---|---|---|
| `part3/text_analysis.py` | read + reimplemented | 114 lines | n/a (no hash promised) | ✅ its `STOPWORDS` omits `between`; re-running it reproduces A's table |
| `part3/iit_nrn2016_abstract.txt` | text slice | 96 words | n/a | ⚠️ cannot be compared to the publisher abstract — nature.com serves a paywalled/JS shell, so the ground truth was unreachable. "Not in retrievable HTML" ≠ "disproven". |
| `part3/top15_words.csv` | csv parse | 15 rows | n/a | ✅ **15/15 exact** |
| `part3/gwt_diagram_prepared.png` | — | **no bytes at all** | ❌ absent — the block is 11 lines of prose | ❌ **the 27-word / 92.2 %-mean / 96 %-median / "96 % ≥ 70 %" OCR statistics are unverifiable.** `UNRESOLVED`. |

A supplied **no hashes whatsoever**, so byte-level verification was structurally impossible for A
even had the zip arrived; only content recomputation was available. The manifest is a claim about
files, not a checksum record.

**B.** No artifacts, no hashes, no banners. Byte-level verification: **0 files, nothing to verify.**
Content-level: I could still recompute its 3A table from the public arXiv abstract (row 17,
15/15 ✅), and I could **not** check its 3B OCR claims at all (row 19, UNRESOLVED). B's six named
companion files (row 18) are the reason B's "no artifacts" is scored as a *broken promise* rather
than a *choice*.

### 4.4 My own verification tooling's limits, stated [RECONSTRUCTED]

- **PDF page count**: `pypdf` is not installed here, so "25 pages" came from regexing `/Type /Page`
  in the PDF object tree. 25 by that method; it is an approximation of the real page-tree walk, and
  I did not install a parser to confirm it.
- **Image tail check**: my "does the PNG end with `IEND`" test printed `ok=False` for *both* images
  even though their SHA-256 matched. Trailing bytes after `IEND` are legal. **Trust the hash, not
  the tail.** A similar false negative would have been a real mistake had I not cross-checked.
- **Binary newline detection**: flagging `CRLF` on a base64-decoded PDF/PNG is meaningless —
  `\r\n` byte pairs occur naturally in binary. My script now reports `binary` for those files.
- **Extraction is now reproducible**: `python3 eval/w3/verify_bundle.py` prints the table above and
  writes the JSON. It must read `'rb'`; the docstring warns why.

## 5. Stylometry — exact specification

This is the analysis behind the "0.812 vs 0.599" figure I published in
`eval/model_attribution_appendix.md` §A1. Everything below is now reproducible with one command
(Part 6); where my published numbers were computed at full precision and then rounded, that is said
explicitly.

### 5.1 What was selected, and why

| Question | Answer |
|---|---|
| Which texts? | Two sections per agent, chosen **before** any feature computation: the **Part-1 KV-cache technical explanation** and the **Part-2 empirical synthesis** → 6 texts: `A_kv, A_syn, B_kv, B_syn, C_kv, C_syn`. |
| Why those two? | They are the only pair every agent was *required* to produce on the same two subjects by the same prompt, so cross-agent comparison is at least nominally like-for-like. |
| Why not the whole reports? | Tested separately (Part 5.8). Whole-report comparison is confounded by length, tables, and reference lists. |
| What was excluded? | A's script/CSV/manifest blocks, C's 19 non-report files, all base64, all bundle banners and `END OF FILE` markers, and B's Part 3C tool list. |
| Selection bias I did not control for | Section boundaries were picked by *heading-string match*, so an agent that titled things differently got a slightly different span. C's KV span (5,930 B) is 2.6× A's (2,276 B) — the comparison is not length-matched, and cosine on proportions is length-sensitive at these sizes. |

**Segmentation, exactly as run:** split the file on `\n`; `i` = first line with
`line.startswith(START)`; `j` = first line after `i` with `line.startswith(END)`; span = lines `i..j-1`
joined with `\n`. The heading line itself is **retained**. The anchors used:

| Text | START anchor | END anchor |
|---|---|---|
| `A_kv` | `## 1.2 Transformer KV cache` | `## 1.3` |
| `A_syn` | `## 2.2 Synthesis` | `# PART 3` |
| `B_kv` | `## Q2. Transformer KV cache` | `## Q3` |
| `B_syn` | `### Synthesis: empirical status` | `## 3A` |
| `C_kv` | `## 1.2 Transformer KV cache` | `## 1.3` |
| `C_syn` | `## 2.2 Synthesis: IIT vs GWT` | `# Part 3` |

The six resulting spans are saved verbatim at `eval/stylo/{A,B,C}_{kv,syn}.txt`
(2,276 / 5,810 / 2,478 / 4,082 / 5,930 / 8,002 bytes), so the input to the analysis is itself
auditable.

### 5.2 Preprocessing — the complete ordered list, nothing else

Applied in this order, to each span, and this is the **entire** preprocessing:

```
1. re.sub(r'`[^`]*`', ' ', t)      # inline code spans → single space
2. re.sub(r'\|', ' ', t)           # markdown table cell separators → space
3. re.sub(r'https?://\S+', ' ', t) # URLs → space
4. re.sub(r'\*+|#{1,4}', ' ', t)   # emphasis asterisks and 1–4 '#' heading markers → space
5. re.sub(r'\s+', ' ', t).strip()  # collapse all whitespace runs, trim
6. (inside the n-gram function only) t = t.lower()
```

Every number quoted in the table below comes from `eval/stylo/preprocess_audit.py`
(`python3 eval/stylo/preprocess_audit.py`), which applies the six steps above and reports what
survives them — added during this reconstruction so the claims are checkable, not asserted.

Consequences, itemised, including the ones that are not flattering:

| Aspect | What actually happened |
|---|---|
| **Case** | Folded at step 6, *after* cleaning, so cleaning regexes saw original case (irrelevant: they are case-insensitive by construction here). |
| **Unicode** | **No NFC normalisation, ever.** Tested: `t != unicodedata.normalize('NFC', t)` is `False` for all six spans, so this omission changed nothing *for these texts* (21–31 non-ASCII chars each: em-dashes, `Φ`, `₂`, curly quotes) — accented letters were fed to the trigram window in whatever form the file held. |
| **Whitespace** | Every run (incl. newlines) → one space, then `.strip()`. |
| **Punctuation** | **Kept**, except `\|`, `*`, `#`. Periods, commas, brackets, colons, hyphens, apostrophes all survive and participate in trigrams. |
| **URLs** | Removed by regex — but B and C cite as `[12](https://doi.org/…)`, so removing the URL leaves the **citation-number residue** `[12]`. Measured residue: 0 `[n]()` artefacts in 5 of 6 spans, **8 leftover `[` + digit sequences in `C_kv`** (its links are not all `http…\S+`-shaped). Not cleaned, not removed: a small, unquantified contamination in one text. |
| **Citations** | Bracketed numbers, `DOI:`, author-year parentheticals: **retained** (no citation-specific rule existed). |
| **Markdown / headings** | Marker `#`s stripped, **heading text kept** ("1.2 Transformer KV cache" stays in the vector). Section-number tokens (`\d+\.\d+`) survive: 1 each in the KV spans, 13 in both synthesis spans (equations/refs), 3 in C's. |
| **Tables** | `\|`s → spaces, so table *content* (digits, labels) remains as prose. A's and C's KV spans contain tables; B's contains one. |
| **Code** | Inline code spans removed (step 1). **C's KV span contains a fenced code block** — my regex has no fence handling, so the opening fence paired with the closing fence and everything between them was deleted as if it were one long inline span. `eval/stylo/preprocess_audit.py` shows `ticks=0` for all six spans and `brack=8` for `C_kv` only. A real, disclosed defect in one of six inputs. |
| **Boilerplate** | **None removed** — no header/footer/"End of report" stripping. C's `C_kv` retains a table-of-contents-free body, and A's spans include its `**Answer:**`-style lead-ins. |
| **Stopwords / lemmatisation / tokenisation** | **None.** No stopword list, no stemmer, no word-level segmentation for the cosine at all. |
| **Min / max length** | The only length rule was in an *unused* sentence splitter (`len(s.split()) > 2`), applied when computing sentence statistics; the cosine path has no min and no max. All six spans were used whole. |

### 5.3 Features and vectors

| Choice | Value |
|---|---|
| n-gram unit | **character** trigrams, not words |
| Extraction | `Counter(t[i:i+3] for i in range(len(t)-2))` over the lowercased cleaned text — **spaces included in the window** (so `' th'`, `'ing'`, `'the'` are features) |
| Raw vocabulary size per text | `A_kv` 982 · `B_kv` 1,105 · `C_kv` 1,944 · `A_syn` 1,791 · `B_syn` 1,520 · `C_syn` 2,036 distinct trigrams |
| Trigram instances (with repeats) | 2,081 / 2,282 / 5,150 / 4,452 / 3,188 / 6,520 |
| Truncated to top-k? | **No.** Full vocabulary used in both vectors for every pair. |
| Counts or TF-IDF? | **Raw counts.** No TF-IDF, no sublinear scaling, no idf anywhere. |
| Normalisation | `x / ‖x‖₂` (equivalently: proportions `x/Σx` then L2 — algebraically identical for cosine, and this is what the code does) |
| Vector form | sparse count dict `Counter`, embedded on the **union** of the two keys; zero for absent features. No shared-vocabulary pre-selection. |
| Cosine | `cos(x,y) = (x·y) / (‖x‖₂ ‖y‖₂)` |
| Pairwise procedure | for each of the C(6,2) = 15 unordered pairs: union the key sets, dot-product over the union, divide by the product of the two L2 norms of the **full** vectors (norms are computed on each text's own complete vector, not on the pair intersection) |
| Self-pairs | excluded from every group mean; diagonal printed as 1.000000 |

**Worked example, all the way down — `cos(A_kv, B_kv) = 0.844406`:**

```
|union vocabulary| = 1,488 trigrams
Σ_v A[v]·B[v]        = 8,684          (over all 1,488 union keys)
‖A_kv‖₂             =   97.072138
‖B_kv‖₂             =  105.943381
product             = 10,284.150524
cos = 8,684 / 10,284.150524 = 0.844406   (0.844406154864 at full precision)

Only 599 of the 1,488 union keys (40.3%) are shared, so 1,488−599 = 889 keys
contribute zero to the dot product while still inflating both norms:
the union-vocab/zero-padding choice is NOT cosmetic here.
```

### 5.4 The COMPLETE pairwise matrix (all 36 cells; also at `eval/stylo/cosine_matrix.csv`)

Order `A_kv, A_syn, B_kv, B_syn, C_kv, C_syn`:

| | A_kv | A_syn | B_kv | B_syn | C_kv | C_syn |
|---|---|---|---|---|---|---|
| **A_kv** | 1.000000 | 0.585252 | 0.844406 | 0.507762 | 0.794344 | 0.570154 |
| **A_syn** | 0.585252 | 1.000000 | 0.596756 | 0.788254 | 0.685972 | 0.840418 |
| **B_kv** | 0.844406 | 0.596756 | 1.000000 | 0.526443 | 0.829077 | 0.597400 |
| **B_syn** | 0.507762 | 0.788254 | 0.526443 | 1.000000 | 0.590880 | 0.774796 |
| **C_kv** | 0.794344 | 0.685972 | 0.829077 | 0.590880 | 1.000000 | 0.684187 |
| **C_syn** | 0.570154 | 0.840418 | 0.597400 | 0.774796 | 0.684187 | 1.000000 |

15 off-diagonal values, each with its group membership:

| pair | cos | group |
|---|---|---|
| A_kv × B_kv | 0.844406 | same topic, cross-agent |
| A_kv × C_kv | 0.794344 | same topic, cross-agent |
| B_kv × C_kv | 0.829077 | same topic, cross-agent |
| A_syn × B_syn | 0.788254 | same topic, cross-agent |
| A_syn × C_syn | 0.840418 | same topic, cross-agent |
| B_syn × C_syn | 0.774796 | same topic, cross-agent |
| A_kv × A_syn | 0.585252 | **same agent**, cross-topic |
| B_kv × B_syn | 0.526443 | **same agent**, cross-topic |
| C_kv × C_syn | 0.684187 | **same agent**, cross-topic |
| A_kv × B_syn | 0.507762 | cross both |
| A_kv × C_syn | 0.570154 | cross both |
| A_syn × B_kv | 0.596756 | cross both |
| A_syn × C_kv | 0.685972 | cross both |
| B_kv × C_syn | 0.597400 | cross both |
| B_syn × C_kv | 0.590880 | cross both |

Note `A_syn × C_kv = 0.685972` **exceeds** two of the three same-agent values
(0.585252, 0.526443) and nearly ties with C's own self-pair (0.684187). A cross-agent,
cross-topic pair beating a same-author pair is the clearest single number in this matrix, and it is
why the group split is not the interesting part.

### 5.5 Every intermediate behind 0.812 and 0.599 [RECONSTRUCTED — full precision, this turn]

```
SAME TOPIC, DIFFERENT AGENT (n = 6)
  0.844406 + 0.794344 + 0.829077 + 0.788254 + 0.840418 + 0.774796
  Σ = 4.871295895      mean = 4.871295895 / 6 = 0.811882649117  → 0.812
  sd (population, ÷n)     = 0.027108        range = 0.774796 … 0.844406
  sd (sample, ÷n−1)       = 0.029695        variance (pop) = 7.3485e-4

SAME AGENT, DIFFERENT TOPIC (n = 3)
  0.585252 + 0.526443 + 0.684187
  Σ = 1.795881565      mean = 1.795881565 / 3 = 0.598627188450  → 0.599
  sd (population)         = 0.065090        range = 0.526443 … 0.684187
  sd (sample)             = 0.079718        variance (pop) = 4.2367e-3

DIFFERENCE            0.811882649117 − 0.598627188450 = 0.213255460667 → "0.213"
```

The published text said "0.812 vs 0.599, Δ 0.213". Those three are mutually consistent at 3 dp.
One honest wrinkle: if the means are taken over the **already-rounded** cells (as a hand reader
would), Σ = 4.870 → 0.811667 and Σ = 1.795 → 0.598333, Δ = 0.213333. Both routes round to the
published figures, so no correction is needed — but the two routes are not identical, and the
full-precision one above is authoritative.

The group sizes are **unbalanced by design** (6 vs 3): with 3 agents there are 3 same-agent pairs
but C(3,2)·2 = 6 same-topic pairs. I did not weight or resample for this; it matters because the
n=3 mean has 2 degrees of freedom.

### 5.6 Feature diagnostics actually computed (and which are unusable)

Retained at `eval/stylo/features.json` (29 metrics × 6 texts). **Three of its rows must not be
quoted** — see Part 6 for the fuller disclosure:

- `words` — the dict key was overwritten, so this row holds a token *list*, not a count.
- there is **no `MTLD` row at all**: the metric I remember attempting is simply absent from the 29
  keys actually saved (`arxiv, avg_word_len, bold_markers, bullets, colon, comma, digits_per_1k,
  emdash, endash, first_person, flesch, func_share, hedge, hedges_per_100w, ing_rate, long_words,
  numerics, of_rate, ous_rate, paren, semi, sent_max, sent_mean, sent_med, sent_sd, that_rate,
  the_rate, type_ttr, words`). My working note had claimed an all-zero MTLD row; that note was wrong.
- a row labelled `arxiv` — it actually counted `DOI`-or-`arXiv` literal strings, so its name is wrong
  (values A_syn 10, B_syn 12, C_syn 0 line up with the `DOI:` counts in §5.8, not with arXiv usage).
- `sent_max` (longest "sentence" in words: A_kv 48 / A_syn 63, B_kv 46 / B_syn 62, C_kv **106** /
  C_syn 47) and every `flesch` value (A_kv 21.7, B_kv 28.8, C_kv 30.8, A_syn **4.7**, B_syn **−4.3**,
  C_syn 15.1) were computed on cleaned span text with an ad-hoc vowel-group syllable counter, so
  markdown/table residue pollutes them — C_kv's 106-word "sentence" is a table row, and negative
  Flesch scores are arithmetically possible but diagnostically meaningless. Reported as curiosity,
  **not** as readability findings.

The cosine path never touched any of these; it is a separate code path and is unaffected.

### 5.7 "Keyness" — what it was and why it is exploratory only

Method: for each agent, words over-used relative to the mean of the *other two agents'* same-section
frequency, with +0.5 additive smoothing on a raw ratio, no significance floor, no minimum count.
Result: A → `keys, both, status, even, workspace`; B → `vectors, journal, step, batch, complexity`;
C → `connectivity, data` — **all explainable by topic or by which sources it cited**. No authorial
vocabulary emerged. A first attempt crashed (`TypeError: unsupported operand … dict_values + int`),
which is also why no threshold was ever applied. This is screening, not a test.

### 5.8 Whole-report structural tics (over `eval/work*_REPORT.md`)

| metric | A | B | C |
|---|---|---|---|
| words (`[A-Za-z][A-Za-z'\-]+`) | 3,907 | 3,928 | 7,371 |
| inline markdown links `](http` per 1k words | **0.00** | 8.40 | 9.23 |
| literal `DOI:` strings | **10** | 0 | 0 |
| em-dashes per 1k words | 13.05 | 16.29 | 12.07 |

All four rows re-verified this turn and reproduce the published table exactly (including that A's
3,907 counts the 84-word bundle preamble and banner; A's body-only figures are 3,823 words and
50 em-dashes ⇒ 13.08/1k, i.e. the published 13.05/1k is an artefact of including the preamble in
the denominator (immaterial, but not the body-only rate).
The `DOI:` row is the one to read carefully: my first re-derivation attempt reported 21 and 14
because I counted the bare token `DOI` (`\bDOI\b`) instead of the literal `DOI:` — the published
numbers (10/0/0) are the literal-string counts and are the correct ones.

Interpretation I stand by: these are **citation-formatting conventions**, plausibly scaffold- or
prompt-driven, not model voices. Interpretation I retract: see Part 9.

---

## 6. Retained vs regenerated

### 6.1 The one-sentence answer, verbatim as required

**RAW FEATURE MATRIX WAS NOT RETAINED.**

At scoring time I computed the 6 character-trigram count vectors **in a throwaway `python3 - <<'PY'`
heredoc**, printed the 6×6 matrix and the two group means to stdout, read them, and used them in
`model_attribution_appendix.md` §A1. No `.npy`, no `.json`, no saved vector file existed until this
reconstruction. **What *was* retained** and is still on disk:

| Artifact | Status |
|---|---|
| `eval/stylo/{A,B,C}_{kv,syn}.txt` — the 6 input spans | **retained from the original session**; these are the actual bytes the analysis consumed |
| `eval/stylo/features.json` — the 29-metric table | retained, but partly defective (§5.6, Part 6.3) |
| cosine vectors, the 6×6 matrix, the group sums | **not retained at scoring time** |
| `eval/stylo/cosine_matrix.{csv,json}`, `trigram_top40.csv`, `text_stats.json` | **generated during this reconstruction** by `eval/regenerate.py` |

### 6.2 The complete regeneration code

Ship file: `eval/regenerate.py` (98 lines, stdlib only — `re, math, csv, json, collections,
itertools, statistics` — deterministic, no network). It reads `eval/work{1,2,3}_REPORT.md` and
`eval/stylo/*.txt`, so those must be present. Run from the repo root:

```bash
python3 eval/regenerate.py
```

It re-derives, in order: segmentation → cleaning → trigram counts → full 6×6 cosine → group means,
sds, Δ → the exact 15-partition test → the CSV/JSON outputs. Its SHA-256 is in Part 12. Rather
than duplicate 98 lines here, the parts that must be checked by eye:

```python
def clean(t):                                   # 5.2, verbatim
    t = re.sub(r'`[^`]*`', ' ', t)              # inline code spans
    t = re.sub(r'\|', ' ', t)                   # table separators
    t = re.sub(r'https?://\S+', ' ', t)         # URLs
    t = re.sub(r'\*+|#{1,4}', ' ', t)           # emphasis + heading markers
    return re.sub(r'\s+', ' ', t).strip()

def trigrams(t):                               # 5.3, verbatim
    t = t.lower()
    return Counter(t[i:i+3] for i in range(len(t) - 2))

def cosine(a, b):                              # 5.3, verbatim
    keys = set(a) | set(b)                     # union; absent features are 0
    dot = sum(a.get(k, 0) * b.get(k, 0) for k in keys)
    na  = sqrt(sum(v * v for v in a.values()))
    nb  = sqrt(sum(v * v for v in b.values()))
    return dot / (na * nb)
```

and the exact test:

```python
def W(part):            # part = ((i,j),(k,l),(m,n)) = the three same-"agent" pairs
    within    = [COS[x][y] for p in part for (x, y) in combinations(p, 2)]
    between   = [COS[x][y] for p in ALL15 if p not in [tuple(sorted(q)) for q in part] for (x,y) in [p]]
    between   = [COS[i][j] for (i, j) in ALL15 if (i, j) not in within_pairs]
    return mean(within) - mean(between)
```

**Reproduction check [RECONSTRUCTED]:** regenerating now returns `0.812` and `0.599` to 3 dp and
`Δ = 0.213255`, i.e. the published numbers reproduce from the retained inputs. That is the strongest
statement available — it is a *reproduction from the same input files with code written later*, not a
replay of the original session's code, because that code no longer exists. I do **not** claim to have
the original vectors; the vectors in Part 5 are newly computed.

### 6.3 Things I will not launder

`features.json` is retained **and flawed**; it stays in the repo as a retained-but-flawed artifact
rather than being quietly fixed or deleted, because deleting it would erase the evidence that the
flaws existed (Part 5.6 lists which rows are unusable). The three earlier scripts I ran in
disposable heredocs (`eval/ax.py` for arXiv, an aborted keyness attempt, the first feature pass) are
present in the workspace as half-finished files; they are not part of the recommended pipeline and
are not cited as such.

---

## 7. Every statistic, with its formula, inputs, result, interpretation, assumptions, limits

Notation: `COS[i][j]` = cosine from Part 5.4. Text order `A_kv, A_syn, B_kv, B_syn, C_kv, C_syn`.

| # | Statistic | Formula | Inputs | Result | Interpretation | Assumptions | Limitations |
|---|---|---|---|---|---|---|---|
| S1 | Weighted composite | `Σ wᵢsᵢ / Σ wᵢ` | 9 scores × weights `(3,3,3,1.5,1.5,1,1,1,1)` | A **5.46875**, B **7.78125**, C **9.21875** | C > B > A | weights meaningful | judgement scores, 1 rater, unanchored scale; ±0.4 meaningless |
| S2 | Unweighted sums / means | `Σ sᵢ`, `Σ sᵢ/9` | same 27 cells | A 50 (5.5556), B 69 (7.6667), C 84 (9.3333) | same order without any weighting | dims equally important | same |
| S3 | Cell dispersion, all 27 | pop. sd, var | the score matrix | mean 7.519, sd **1.873**, var 3.509, min 3, max 10 | the spread is wide relative to the composite gap | — | descriptive only |
| S4 | Per-agent spread | pop. sd over its 9 dims | A/B/C rows | A **1.499**, B 0.943, C **0.471** | A's quality is uneven, C's uniformly high | — | n=9 cells, same rater → *not* reliability evidence |
| S5 | Per-dimension spread | pop. sd across agents | column-wise | d9 **2.867**, d1 2.160, d4/d6 2.055, d5 1.247, d2 1.633, d3 0.943, d8 0.816, d7 **0.471** | the ranking is driven by d1/d9/d4/d6; d7 barely separates anyone | — | with n=3, sd is dominated by one agent |
| S6 | Weighted pairwise margins | `Σwᵢ(sᵢ−s'ᵢ)/16` | score matrix | C−B **+1.4375**, C−A +3.7500, B−A +2.3125 | ordering not marginal on paper | weights as above | the numbers are not interpretable as measured distances |
| S7 | Sensitivity (7 schemes) | S1 with different `w` | Part 8 | order **C>B>A in all 7** | ranking robust to weighting | — | robust to *weights*, not to *my per-dimension judgements* |
| S8 | Group means of similarity | mean of 6 / mean of 3 | Part 5.5 | **0.811883** vs **0.598627**, Δ 0.213255 | topic-matched pairs more similar | pairs comparable | unbalanced n (6 vs 3); **confounded by design — see S10** |
| S9 | Group sds / ranges | pop & sample sd | same | 0.027108/0.029695, range 0.7748–0.8444; 0.065090/0.079718, range 0.5264–0.6842 | the within-agent spread is 2.4× the cross-agent spread | — | n=3 |
| S10 | **Exact partition test** | `W = mean(within) − mean(between)`, null = agent labels exchangeable, **all 15** distinct pair partitions, no RNG | Part 5.4, 15 cosines | **W = −0.103058**; one-sided `P(W ≤ obs) = 6/15 = 0.4000`; `P(W ≥ obs) = 10/15 = 0.6667`; two-sided by \|W\| = 8/15 = 0.5333 | the observed split is **unremarkable** under label exchangeability | exchangeability of labels under H₀ | **smallest attainable one-sided p = 1/15 = 0.0667** → cannot reach 0.05; the design aliases agent with topic |
| S11 | Nearest-neighbour attribution | for each text: is its most-similar other text same-agent? same-topic? | Part 5.4 | **agent 0/6 = 0.000**; **topic 6/6 = 1.000** | every text's closest match is someone else's write-up on the same subject | 1-NN as the decision rule | exact null over 15 labellings: 7 give 0/6, so `P(recall ≤ 0) = 7/15 = 0.4667` — again no resolution |
| S12 | Style-surface counts | literal/regex counts per 1k words | 3 report files | Part 5.8 | formatting conventions differ | files are the reports proper | no model-level inference licensed |
| S13 | Keyness ratios | `(f_a+0.5)/(mean f_others+0.5)` | per-section word freqs | Part 5.7 | nothing author-specific found | — | no significance floor; screening only |
| S14 | Verification coverage | `verified / claimed` | Part 3–4 | C **20/20** files, **6/6** OCR stats, **15/15** abstract rows; A **15/15** rows, 0/1 image; B **15/15** rows, 0/6 files; **32/32** identifiers resolve | auditability, not quality | my checks are a sample | absence of a found error ≠ error-free |
| S15 | MAE between agents | `mean\|sᵢ−sⱼ\|` | score matrix | A–B **2.111**, A–C **3.778**, B–C **1.667** | B sits nearer C than A does | — | same-rater scale artefact |
| S16 | Rank correlation (dimension vs composite) | Spearman ρ, n=3 | each column vs S1 | **ρ = +1.000 for all 9 dimensions** | every dimension alone reproduces the ranking → **the composite adds no information here** | — | with 3 points ρ=1 is nearly unavoidable; it *demonstrates* fragility, it does not show agreement between raters |

**Statistics I did *not* compute, stated so they are not mistaken for omissions of detail:** no
bootstrap, no confidence interval of any kind, no inter-rater agreement (κ/ICC — there was one
rater), no effect size in any standardised form, no correction for multiple comparisons, no
Bayesian model. Part 13B specifies them as *recommended additions*, not as things that were done.

**Vocabulary discipline.** In Part 7 "statistically unremarkable" and the S10/S11 p-values are the
output of the defined enumeration in §5/Part 9, so they may be called p-values. The `0.95 / 0.45`
numbers printed in `model_attribution_appendix.md` §A3 under a column literally headed "Confidence"
are **not** — see Part 11 row 15.

---

## 8. Re-ranking under alternative weightings

Same 27 scores, seven weighting schemes, no re-judging of any dimension.

| Scheme | weights `d1…d9` | A | B | C | ranking |
|---|---|---|---|---|---|
| **1. Original** | 3, 3, 3, 1.5, 1.5, 1, 1, 1, 1 | 5.47 | 7.78 | 9.22 | **C > B > A** |
| **2. Equal** | 1×9 | 5.56 | 7.67 | 9.33 | C > B > A |
| **3. Drop dim 3** (the subjective cut-off dimension; renormalise over 13) | 3, 3, 0, 1.5, 1.5, 1, 1, 1, 1 | 5.12 | 7.50 | 9.27 | C > B > A |
| **4. Drop dim 2** (source verification; over 13) | 3, 0, 3, 1.5, 1.5, 1, 1, 1, 1 | 5.58 | 7.96 | 9.27 | C > B > A |
| **5. Alternative: accuracy-heavy** | 2, 2, 2, 1, 2, 1, 1, 0.5, 1 | 5.48 | 7.76 | 9.24 | C > B > A |
| **6. Verification-only** (keep 1, 2, 6, 9) | 1, 1, 0, 0, 0, 1, 0, 0, 1 | 4.25 | 7.00 | 9.50 | C > B > A |
| **7. Prose-only** (keep 3, 7, 8, 9) | 0, 0, 1, 0, 0, 0, 1, 1, 1 | 6.25 | 8.00 | 9.25 | C > B > A |

Schemes 3 and 4 implement the brief's "drop the subjective cut-off dimension" / "drop the
source-verification dimension" by **zeroing the weight and renormalising** (Σw: 16 → 13), not by
renumbering.

**Correction, disclosed:** the sensitivity figures I quoted in chat at scoring time — equal weights
→ 5.0 / 6.9 / 8.4, drop-cut-off → ≈5.6 / 8.0 / 9.6 — **do not reproduce from this table and are
superseded by it.** The equal-weight row was computed as a sum divided by 10 (the composite row was
still being counted as a 10th dimension) instead of a mean over the 9 scored dimensions, and the
drop-cut-off row renormalised by 12 rather than 13. Part 8 is the first time these schemes were
computed exactly; it is authoritative. **[RECONSTRUCTED]**

**Pairwise orderings and their robustness:**

- **C > B — robust in all 7 schemes**, margins 1.25 (scheme 7) to 2.50 (scheme 6). This is not
  really a weighting result: C ≥ B on **all 9** dimensions (strictly greater on 7, tied on d3 and d7
  at 9 = 9), so C > B holds for every non-negative weight vector. No weighting choice can invert it.
- **B > A — robust in all 7 schemes**, margins 1.75 (scheme 7, 8.00 vs 6.25) to 2.75 (scheme 6).
  B > A on *every* dimension individually (8>4, 7>5, 9>7, 7>5, 8>6, 7>5, 9>8, 8>7, 6>3), so it too
  is weighting-independent.
- **C > A — robust in all 7 schemes**, margins 3.00 (scheme 7) to 5.25 (scheme 6); component-wise
  as well (C > A on all 9).

Full margin table (B−A, C−B, C−A per scheme), since "robust" needs its numbers:

| scheme | B−A | C−B | C−A |
|---|---|---|---|
| 1 original | +2.3125 | +1.4375 | +3.7500 |
| 2 equal | +2.1111 | +1.6667 | +3.7778 |
| 3 drop d3 | +2.3846 | +1.7692 | +4.1538 |
| 4 drop d2 | +2.3846 | +1.3077 | +3.6923 |
| 5 accuracy-heavy | +2.2800 | +1.4800 | +3.7600 |
| 6 verification-only | +2.7500 | +2.5000 | +5.2500 |
| 7 prose-only | +1.7500 | +1.2500 | +3.0000 |

All 21 margins positive; the minimum across the whole grid is C−B = +1.25 in scheme 7.


**The honest limit of this section:** component-wise dominance means the *order* is
over-determined and the *composite* is doing nothing (see S16, ρ = +1 on every dimension). The
ranking could therefore only be overturned by changing a score, not the weights — and the scores are
the part with no rubric, no blinding and no second rater. Re-weighting is the wrong sensitivity
analysis for this data, and I ran it anyway because it was asked for. **[RECOMMENDED]** would be to
perturb *scores* (e.g. rescore each dimension ±1 at random, 10⁴ draws, and report the fraction of
draws preserving C>B>A) — not run, not part of the original evaluation.

For completeness, the two score changes my new findings would justify (Part 11 rows 8 and 6: C's
synthesis length violation; the cut-off praise partly measuring instruction-following) were **not**
applied anywhere in this document. Applying the length finding alone (d8 9 → 8) moves C's composite from 9.21875 to
9.15625 (Δ = −1/16 = −0.0625) and leaves every ordering intact.

---

## 9. Is a permutation test even appropriate here?

The question the brief asks, answered as "mostly no, and here is the exact number that shows why".

**Null hypothesis.** Agent labels carry no information about similarity: the assignment of the six
texts to three "authors" is exchangeable, i.e. any partition of the 6 texts into 3 labelled pairs is
as likely as any other.

**Statistic.** Two were used. (a) `W = mean(cos over the 3 within-pair cells) − mean(cos over the
remaining 12 cells)` — the *between* term runs over all other pairs, so `between ≠ topic` and the
sign carries extra meaning (a negative W means "the putative author-pairs are less similar than the
rest"). (b) top-1 nearest-neighbour attribution accuracy, which is scale-free and easier to read.

**Procedure.**
1. Build the 6×6 cosine matrix exactly as Part 5.4.
2. Enumerate **every** partition of 6 texts into 3 unordered pairs: 15 of them
   ( `(6−1)!! = 5·3·1` ), deduplicated by sorting within-pair and between-pair indices.
3. For each, compute W (or NN recall) from the *same* fixed matrix.
4. p = fraction of the 15 with statistic at least as extreme as observed. No sampling, **no RNG,
   no seed** — the null is exact by construction. The full null distribution of W is
   `[−0.1189, −0.1124, −0.1086, −0.1060, −0.1050, −0.1031, +0.0492, +0.0511, +0.0558, +0.0570,
   +0.0601, +0.0608, +0.0969, +0.1091, +0.1140]`; observed **−0.103058**, rank 6 of 15 →
   `P(W ≤ obs) = 6/15 = 0.4000`, `P(W ≥ obs) = 0.6667`, two-sided-by-|W| = 8/15 = 0.5333.
   NN recall null: `{0: 7, 1: 2, 2: 3, 3: 2, 4: 1}`; observed 0/6 → `P(≤ 0) = 7/15 = 0.4667`.
   **[PERFORMED + re-run this turn: `eval/regenerate.py` Section D prints all 15.]**

**What is inappropriate about it, precisely:**

1. **n = 3 agents.** The inferential universe is three reports. There is no population from which 3
   authors were sampled, so no sampling variance exists to make a claim *about*; the honest reading is
   descriptive.
2. **A floor on p.** 15 partitions ⇒ the smallest possible one-sided p is **1/15 = 0.0667**. The
   test *cannot* be significant at 0.05, so reporting "p = 0.40, not significant" is close to
   vacuous — the procedure had no chance of a small p whatever the data. I should have said that in
   the appendix and did not.
3. **Complete aliasing.** The design is a 3 × 2 grid: 3 agents × 2 mandated topics, one text per
   cell. Therefore *every* same-agent pair is cross-topic and *every* same-topic pair is cross-agent;
   the agent factor and the topic-pair factor are perfectly crossed and **cannot be separated**.
   That is why S10's W is negative and why "topic dominates" is a claim the data cannot license.
   What the data *do* license is the negative statement: if authorship dominated similarity,
   within-agent pairs would be the most similar; they are the least similar in 3 of 3 cases, and
   1-NN attribution to author is 0/6 while topic attribution is 6/6. That is evidence *against* a
   strong authorial signal, not evidence *for* topic dominance.
4. **Pseudo-replication risk, addressed.** The tempting move — treating each of my ~45 claim checks,
   or each of the 15 matrix cells, or each of 29 features as an "observation" and getting n=45 for
   an agent-level conclusion — is invalid: they are correlated measurements *within* three documents,
   not independent subjects. In particular the 15 cosine cells share 6 vectors; any two cells that
   use the same text are not independent, which is yet another reason the W test's null is
   optimistic. No such aggregation was used in Part 7, and none should be used later.
5. **Multiple looks.** I computed W, NN recall, keyness, four tics and 16 statistics on the same 6
   texts. With 15-point nulls and no correction, some of these will look interesting by chance.

**Verdict for the record.** A permutation test is *defensible* as a transparent, seedless way to show
"nothing author-specific was found", and that is the only claim I make with it. It is *not* adequate
to rank authors, to detect style at n=3, or to support a headline about topic versus authorship. My
published headline was stronger than the design permits and is now downgraded to:

> Directionally consistent with topic-driven similarity, and untestable at this sample size
> (smallest attainable one-sided p = 0.0667; observed W = −0.103058, exact P(W ≤ obs) = 0.4000).

---

## 10. Model-family / generation / cut-off / vendor / style / tool fingerprinting

Every line carries the tag the brief asked for. `DIRECT` = a fact read off the artifacts;
`INDIRECT` = inference with a stated mechanism; `WEAK` = consistent but multiply explainable;
`SPECULATION` = preference of mine without independent support; `UNRESOLVED` = checked and could not
be settled.

### 10.1 What retrieval contamination does to this whole exercise

All three agents had **live web search** during generation (all three say so, and all three cite URLs
that were fetched during their runs). Therefore:

- Any "knows a 2026 fact" observation says nothing about a training cut-off. Cut-off inference from
  knowledge is **invalid here** in general; only *failures* to know, or *timing of retrieval
  disclosures*, carry information — and even those are contaminated by what the agent chose to
  retrieve.
- Any "does not know X" observation could mean "did not search for X".
- The shared-corpus effect is real and measured here: A's and B's H1-2026 lists overlap because both
  trace to `en.wikipedia.org/wiki/2026_in_science` (Part 3 row 45), which *looks* like two models
  agreeing and is one page quoted twice.

### 10.2 The table

| # | Observation | Agent(s) | Tag | What it supports | What it does **not** support |
|---|---|---|---|---|---|
| 1 | All three describe the same platform: "`/home/user`", bind-to-`0.0.0.0` live preview, "~10 speech clips per turn", "sandboxed workspace", the *same* model-family disclaimer naming Claude / ChatGPT / Gemini / Grok / Qwen / Kimi | A, B, C | **DIRECT** | same **platform/scaffold** (Arena.ai Agent Mode) at the same time | same model, or any particular model — the disclaimer is shared boilerplate |
| 2 | All three timestamp the audit date identically and use identical path conventions | A, B, C | **DIRECT** | same harness clock/filesystem | nothing about generators |
| 3 | All three answered 3C's cut-off / family / tools / limits questions **because Part 3C commanded them to** (prompt text now in hand) | A, B, C | **DIRECT** | compliance | epistemic virtue; my "cut-off transparency" dimension is partly measuring this |
| 4 | A: "the read of the diagram returned `no vision capabilities`" (quoted platform string) | A | **DIRECT** | a real tool failure occurred and was reported verbatim | model identity |
| 5 | A's bundle has 6 manifest entries, hashes none, ships the PNG as prose | A | **DIRECT** | A's bundling convention; also see Part 11 row 13 | — |
| 6 | C ships `sudo apt-get install tesseract-ocr` **and** `pip install pypdf pdfminer.six pytesseract rapidocr-onnxruntime` as past-tense actions | C | **INDIRECT** | real execution in a sandbox with root — consistent with this environment | model identity; install logs were not shipped |
| 7 | C self-declares reliable parametric knowledge only "through ~2024, patchy in 2025" and labels every 2025–26 item as retrieved | C | **INDIRECT** | a *disclosed* recall horizon of ≈2024 (its own words) | the true cut-off — it had web access, and disclosures can be wrong |
| 8 | C's vendor table lists releases up to Jun–Aug 2026 with exact spec fields, all of which matched live docs when I checked | C | **INDIRECT** | heavy retrieval + verification effort; plausibly a high-effort reasoning configuration | a *specific* family — the numbers came from the web, not from weights |
| 9 | C warns that ">272K" in OpenAI pricing is a **billing tier, not a context window** | C | **DIRECT** (its text) | unusually careful reading of vendor docs | identity |
| 10 | B's knowledge markers: lists Claude Opus 4.5 and "Gemini 3 (late 2025)" from recall, misses GPT-5.1/5.2/5.4 and Opus 4.6/4.7 | B | **INDIRECT** | a recall horizon around **mid/late-2025** | any family; also confounded by what B chose to search |
| 11 | A's knowledge markers: core competence mid/late-2024, one propagated vendor error (Part 3 row 9), one fabrication (row 4) | A | **WEAK** | a *mid/late-2024-ish* horizon and weaker retrieval discipline | the specific model; error patterns are not model signatures |
| 12 | "Three different bundle conventions ⇒ three different families" | A, B, C | **SPECULATION** | nothing | I explicitly rated this 0.45 in the appendix, i.e. a coin flip — see row 15 of Part 11 for why the number itself was illegitimate |
| 13 | Char-trigram stylometry: no authorial signal (S10 W = −0.103058, P(W ≤ obs) = 0.4000; S11 0/6) | all | **DIRECT** (as a negative) | the texts are **not** separable by this method at n=3 | that models *have* no stylistic signature; the test has a floor of p = 0.0667 and cannot detect what it did not power |
| 14 | Shared surface tics: 0 inline links for A vs 8.4/9.2 per 1k for B/C; literal `DOI:` only in A | A vs B/C | **INDIRECT** | different citation-formatting habits | model identity — formatting is the part most plausibly scaffold/prompt-driven |
| 15 | Any claim of the form "Agent X is model Y" | — | **UNRESOLVED** | — | **This is the honest overall result: the attribution attempt failed.** No vendor name appears in any report; the boilerplate disclaimer forecloses the one direct channel; and the two artefacts that could settle it (session logs / headers) were never provided. |

### 10.3 The negative result, restated

`model_attribution_appendix.md` §A3 asserted, with numbers attached:

| claim | number I printed | tag it deserves |
|---|---|---|
| "None of the three can be identified to a specific LLM family from these texts" | 0.95 | **SPECULATION**-weighted conclusion; the *conclusion* is right, the *0.95* was never computed |
| "All three ran on the same platform" | 0.95 | the conclusion is **DIRECT** (row 1) — the number was not |
| "The three were served by three different model families" | 0.45 | **SPECULATION**, and the number was invented |

### 10.4 Vision fallback — the specific inference I must retract

My appendix contrasted A and C ("claimed no vision, then produced precise OCR confidence numbers")
against B, which I characterised as having *"refused to produce word counts or OCR confidence
numbers"*. **That is false.** `uploads/work2.txt` L176–181 is a 15-row word-frequency table and
L211–219 reads: *"My session did **not** grant me direct image-vision, so I installed and ran a real
OCR engine (**RapidOCR / ONNX** …). It detected **22 text boxes**"* followed by a confidence table.
So **all three** reported no direct vision and **all three** published OCR confidences anyway —
A via Tesseract (27 words, mean 92.2 %), B via RapidOCR (22 boxes, 0.74–0.91), C via both engines on
a different figure. The "distinctive A+C behaviour" I built an inference on **does not exist**; it is
replaced by a three-way common behaviour, which if anything strengthens the shared-platform reading
(a shared platform constraint: no vision tool) and removes my only behavioural argument that B was
different in kind. Consequences: appendix §A2's second bullet is void, and my framing "ran OCR anyway
= discipline signal" is muddled, because Part 3B is *conditional* on image capability (§1.2).

---

## 11. Self-audit — my own claims, re-checked against the artifacts

Format demanded by the brief: original claim, where it appeared, the evidence I had then, the
evidence now, and a verdict. **Nothing here is hidden, minimised, or moved to a footnote, and each
correction says whether it changes a score.**

| # | My original claim | Where it appeared | Evidence I had then | Evidence now | Verdict |
|---|---|---|---|---|---|
| 1 | "18/20 of C's files hash-verify byte-for-byte on my first independent decode" | `agent_forensic_evaluation.md` §0 table L14; §C L118; §F L153; chat reply | text-mode extraction + my hash script output | binary re-read + `eval/w3/verify_bundle.py`: **20/20**, PDF/PNGs/CSVs all byte-exact | **CONTRADICTED** — my bug (universal-newline translation). No score change (C was already 10 on d6); the *reason* changes from "one nit" to "flawless" |
| 2 | "The two CSVs miss by exactly one trailing newline (a round-trip edge case in C's own bundler contract, not a content error)"; also "C-side manifest drift" | §0 L16; §F L153 | my 313 B / 329 B extractions vs claimed 329 B / 345 B | both CSVs are 329 B and 345 B **exactly**; the 16-byte gaps were 16 CRLF lines collapsed by *my* reader; `csv.writer` + `newline=""` at C's script L221–222 predicts CRLF, so the CRLF is *evidence of execution* | **CONTRADICTED** (diagnosis reversed: from "their defect" to "my defect") |
| 3 | "Topic explains the signal; authorship does not" / "0.812 vs 0.599" as a finding about authors | appendix §A1 Result 1 | the two group means | 3×2 grid ⇒ agent and topic are **perfectly aliased**; exact partition test W = −0.103058, P(W ≤ obs) = 0.4000, floor 1/15 = 0.0667; NN: agent 0/6, topic 6/6 | **PARTIALLY CONFIRMED / DOWNGRADED** — the numbers are right and reproducible; the interpretation was not licensed. Now: "directionally consistent, not testable with this design" |
| 4 | Red flag #1 on C: false "most-cited IIT paper" superlative, attributed to a title-search scope limit C did not disclose | §B (Agent C) item 1 | OpenAlex counts (1,543 vs 1,791) | replaying **C's own URL + own regex** returns 1,543 first and does *not* return Tononi 2016 in the top 50; the cause is C's `has_abstract:true` filter — Tononi 2016 has `abstract_inverted_index: null` — and that filter **is** visible in the shipped script | **PARTIALLY CONFIRMED** (prose superlative still false) + my *diagnosis* **CONTRADICTED**; severity HIGH → LOW. My original "not disclosed" was unfair: it is disclosed in code |
| 5 | "B: no artifacts — by choice / deliberately produced none" (framing that treated it as an aesthetic) | §0 L13; §B item 5; §F L153 | absence of FILE blocks | B's own text **promises six files** (L203–207 "Files produced (downloadable)", L220 `ocr_labels.json`, L248 closing line) and none are present | **CONTRADICTED as a benign reading** — this is a deliverables over-claim, the same class as A's `work1.zip` (row 13) and A's dangling note (Part 3 row 10). **Would lower B's d6 from 7 toward 5**; not applied (Part 13B item 4) |
| 6 | Praise for "cut-off transparency" (d3) and "self-description honesty" (d7) as epistemic virtues | §A justifications; §D | the disclosures themselves | `EXPERIMENT_PROMPT.md` Part 3C **requires** cut-off, family, tools and limits statements | **PARTIALLY CONFIRMED** — the disclosures are still more or less accurate, and accuracy is still creditable; but part of what I scored is instruction-following. Also: "identical scripted disclosure ⇒ platform boilerplate" is **WEAKENED** for the same reason |
| 7 | C's praise for four verifiable self-corrections, incl. discarding an unrelated "Public-Private Partnerships" OpenAlex hit | §A d9 justification; §B | I "reproduced" unrelated high-cited hits surfacing | C's query, both sort orders: **no PPP record in the top 50**; grep of C's script: no "partner"/"unrelated"; the unrelated hit I get is a *pragmatics* paper at 1,918 cites | **UNRESOLVED** for the specific instance (may reference an unshipped earlier query state). Cuts against my d9 = 10 for C; my generalisation "the *kind* of error is reproducible" is **CONFIRMED** |
| 8 | I never checked the prompt's own specs | — | n/a (I did not have the prompt) | synthesis length: A 529 ✅, B 536 ✅, **C 1,126 ❌ (188 % of the 400–600 cap)**; sources: A 10 / B 11 / C 12 vs "at least 8–10" ✅✅✅ | **GAP, not an error** — and it is a *formatting* defect against C that my d8 = 9 for C did not price |
| 9 | Scope note I asserted: the rubric's "8–12 sources" | this doc Part 1 §1.2 | my brief | `EXPERIMENT_PROMPT.md` says "at least 8–10" | **DISCREPANCY PRESERVED, NOT HARMONISED** (per instructions). Neither number drove a score |
| 10 | "All 25 cited DOIs/arXiv IDs resolved" | chat reply | my batches | the exact denominators: **28 DOIs via Crossref + 4 arXiv IDs via abs pages = 32 distinct identifiers**, plus a 28-URL liveness sweep (403/401/404 recorded); several URLs were checked for liveness **only** | **UNDERCOUNTED, conclusion intact** — "no invented paper" holds for everything I resolved; I never claimed to have content-checked every URL |
| 11 | Composite B = 7.9, C = 9.3 | chat reply | mental arithmetic | 124.5/16 = **7.78125 → 7.8**; 147.5/16 = **9.21875 → 9.2** | **CONTRADICTED and already corrected** in the md + `scores.csv` |
| 12 | Sensitivity: equal weights → 5.0/6.9/8.4; drop-d3 → 5.6/8.0/9.6 | chat reply | hand arithmetic | 5.56/7.67/9.33 and 5.12/7.50/9.27 (Part 8) — my chat numbers divided by 10 and by 12 respectively | **CONTRADICTED** by Part 8, which supersedes them; ranking unchanged |
| 13 | "A's manifest promised a `work1.zip` never provided" + "21 files claimed vs 4 present" (my working note) | §0 L12 / chat | grep of A's bundle | A's `MANIFEST (all files in work1.zip)` block (L434–443) lists **6 entries**, of which 5 exist as blocks and 1 (`image-search/…jpg`) does not; the PNG block is **11 lines of prose**, not zero bytes — it contains A's label dump and its confidence summary line | **"21 files" is UNSUPPORTED — retract it.** "zero-byte image" is imprecise (no *image bytes*, but 11 text lines). The material point stands: A's OCR statistics remain **UNRESOLVED** |
| 14 | "A's `iit_nrn2016_abstract.txt` cannot be matched to the publisher abstract" | chat | nature.com serves a JS/paywall shell | still true; and A's abstract body is 96 words / the header is 6 lines — internally consistent | **CONFIRMED as a limitation**, not a defect of A |
| 15 | Numbers labelled "Confidence" in the appendix (0.95 / 0.95 / 0.45) | appendix §A3 | none — I had run no statistical procedure at scoring time | nothing has changed: there is still no procedure that outputs these | **UNSUPPORTED, and mislabelled.** They are verbal confidence-like judgements wearing a numeric costume. This also **violates my own rule** for this document (no use of "confidence"/"probability"/"significance"/"p-value" unless computed by a defined procedure). Corrected form: `SPECULATION` for the 0.45, and for the other two the *conclusion* is DIRECT (platform) or the *absence of evidence* (identification). Every `%` I gave in chat for claim-level accuracy is likewise unsourced |
| 16 | C's abstract length: I reported "my abstract body is 1,718 chars vs C's claimed 1,642" | §A/working notes | naive slice | C's `iit_abstract_metadata.json` records `abstract_chars: 1642`, and slicing `iit_abstract_raw.txt` between its two `=====` rules yields 1,642 exactly (1,644 with the separating newlines) | **MY measurement was wrong; C's was right** — verdict **CONFIRMED for C** |
| 17 | "No statistical test of any kind was run at scoring time" | this doc, §2.1 | — | re-confirmed: S10/S11 exist only from this reconstruction | **CONFIRMED** (it is a disclosure, not an error) |
| 18 | My `features.json` table | appendix | — | re-inspected: `words` row holds a token list, not a count; the `arxiv` column counted `DOI`-or-`arXiv` literals; there is **no** `MTLD` row (my working note wrongly described one as all-zero); `sent_max`/`flesch` computed on cleaned text with an ad-hoc syllable counter | **PARTIALLY CONFIRMED** — the table is retained-but-defective (2 mislabelled/polluted rows + 2 unusable ones), **my own note about "MTLD all zeros" is UNSUPPORTED and is retracted here**. The cosine path is separate and unaffected |
| 19 | `sent_max` / `flesch` values (A_syn 4.7, B_syn −4.3, C 106) | appendix | computed on cleaned text | cleaned text still contains table digits and section numbers, and the syllable counter is ad-hoc | **UNSUPPORTED as readability findings**; they are artefacts of my pipeline, and the appendix should not have shown them |
| 20 | Whole-report style metrics for A | appendix Result 3 | `eval/work1_REPORT.md` | that file = lines 1–263 of the upload ⇒ includes the 13-line bundle preamble (74 words) + banner (12); body-only A = 3,830 words, 51 em-dashes ⇒ 13.05/1k either way | **PARTIALLY CONFIRMED** — asymmetric input, numerically immaterial |
| 21 | "A's and C's identical no-vision string ⇒ same platform" | appendix | grep | A: *"my direct vision is not available for this image in the sandbox"*; C: *"**No vision in this session**"*; B: *"My session did **not** grant me direct image-vision"*. Near-identical in meaning, **not** verbatim identical in wording | **PARTIALLY CONFIRMED** — the shared-*constraint* inference survives (see row 22); the "identical string" phrasing over-stated it |
| 22 | "Only A and C show the no-vision-yet-OCR-number behaviour" | appendix §A2 | my extraction | all three, incl. B (Part 3B of B's report) | **CONTRADICTED** — see §10.4. My one behavioural argument for treating B as different in kind collapses |

### 11.1 Claims that survived re-checking (recorded so the audit is not only a list of my errors)

32/32 identifiers resolve (row 10); C's 6/6 OCR statistics reproduce exactly from its shipped
files; C's Artemis/JUNO/OpenAlex/vendor-table figures match primary sources verbatim; the Nature
editorial quote is word-perfect; A's and B's Part-3A word tables reproduce 15/15; C's
`has_abstract:true`-driven selection is a *retrieval-recall* failure and not arithmetic; A's
`between` bug is real and I reproduced it; the 3×2 aliasing discovery and the 1/15 power floor are
new, and they constrain my own conclusions rather than the agents' scores.

### 11.2 Net effect on the original evaluation

- **Scores: unchanged.** I have not rewritten any integer. The candidate revisions (row 4 → C's
  severity HIGH→LOW; row 5 → B's d6 7→5) are recorded in Part 13B with their arithmetic, not applied.
- **Narrative: three changes required.** (a) C's bundle is flawless at byte level, so "the one agent
  whose work survives an adversarial audit" gets *stronger*, not weaker; (b) B's artifact
  non-delivery moves from "chose not to" to "promised and did not"; (c) the stylometry headline must
  lose its causal wording.
- **Attribution: unchanged negative result**, with one supporting argument removed (row 22).

---

## 12. Reproducibility package

Everything needed to re-derive every number in this document, plus an explicit list of what is
missing. Files are paths inside the deliverable repo (my working copy used `eval/`; the published
copies are under `Primary Evaluation/`).

### 12.1 Inputs and their hashes

| File | Bytes | SHA-256 |
|---|---|---|
| `uploads/work1.txt` (Agent A) | 40,755 | `add1e9ff541a8d27f48f521f7f78134eadaa10893f0cc5a7367b5ec3d32b6c2b` |
| `uploads/work2.txt` (Agent B) | 34,373 | `1574201d32dc968a8c14b70410692b0860e67c235c0079e13935de972447d6e1` |
| `uploads/work3.txt` (Agent C) | 9,340,316 | `412b5ea4fa5712dc918024cdef0352312ce98e6ba2671607363bc061127ca60d` |
| `eval/work1_REPORT.md` (analysis copy, lines 1–263) | 33,235 | `17fa790436dbd28ac821017c16ebb65f015bdc6c3cbaf46c9a400742bee3a52a` |
| `eval/work2_REPORT.md` | 34,373 | `1574201d32dc968a8c14b70410692b0860e67c235c0079e13935de972447d6e1` — **identical to the upload**, so B was analysed whole |
| `eval/work3_REPORT.md` | 63,386 | `441dee9cf4ab8c458222e4926bfac268ae14d5f1d2fed686a2a52c645131bbeb` |
| `eval/stylo/A_kv.txt` `A_syn.txt` `B_kv.txt` `B_syn.txt` `C_kv.txt` `C_syn.txt` | 2,276 / 5,810 / 2,478 / 4,082 / 5,930 / 8,002 | the six section spans actually analysed (inputs to §5) |

### 12.2 Code, with hashes and how to run it

```bash
# from the repo root, Python 3.13.14, stdlib only, no network, no seed needed
python3 eval/regenerate.py             # the matrix, group arithmetic, exact test, CSV/JSON outputs
python3 eval/w3/verify_bundle.py       # C's 20-file byte/SHA verification (Part 4)
python3 eval/stylo/preprocess_audit.py # what the preprocessing left behind (Part 5.2)
```

SHA-256 at the time of writing (`regenerate.py` was edited twice during this reconstruction and both
edits were reverted, so this hash is the version that produced the published matrix — re-ran to
confirm):

```
regenerate.py          a2c8e72dc62b1b1f6989c9755b23ae72c5c6919278913b17317838af51ba988c
verify_bundle.py       7517a34bcf643a793f59ea34048f1c2323089d1b472c3fed0bfd12fdee6e18df
preprocess_audit.py    70eac12669c2c05ece94b5e25ce0e8ede3720d5c17df12999fb6a39002854c73
```

`regenerate.py` prints sections A–F (span stats → trigram vocabularies → full 6×6 cosine →
group arithmetic → the 15-partition exact test → top-40 trigrams) and writes
`eval/stylo/cosine_matrix.{csv,json}` and `eval/stylo/trigram_top40.csv`.
`verify_bundle.py` prints the Part 4 table and writes `eval/w3/verification_details.json`;
`preprocess_audit.py` prints the §5.2 residue table. All three are deterministic; **no RNG is used anywhere in either**, so "seed" is not applicable — the
exactness comes from full enumeration (15 partitions), not sampling.

### 12.3 Outputs already in the repo

| File | Content |
|---|---|
| `eval/stylo/cosine_matrix.csv` / `.json` | the complete 6×6 matrix, 6 dp (`8e1400ac…` / `da97965e…`) |
| `eval/stylo/trigram_top40.csv` | top-40 char trigrams × 6 texts, space glyphed as `␣` (`dc0349a4…`) |
| `eval/stylo/text_stats.json` | per-text raw bytes / clean bytes / whitespace tokens / trigram instances / distinct trigrams (`7717c619…`) |
| `eval/stylo/features.json` | the retained 29-metric table, **`words`/`arxiv` rows mislabelled, `sent_max`/`flesch` polluted** (§5.6, Part 11 row 18) (`834540f9…`) |
| `eval/EXPERIMENT_PROMPT.md` | cached copy of the agents' prompt (fetched from the repo this turn), so §1.2's quotations are checkable offline |
| `eval/w3/manifest_parsed.json` | C's 20 manifest entries, parsed (`a7964da1…`) |
| `eval/w3/verification_report.txt`, `verification_details.json` | the 20/20 result, per file, with SHA and newline style |
| `eval/w3/**` | all 20 extracted files, now byte-exact (the two CSVs were re-written this turn) |
| `eval/agent_forensic_evaluation.md` | the original evaluation; §A = the score authority (`3e622729…`) |
| `eval/scores.csv` | machine-readable scores, generated from that table (`8489bba1…`) |
| `eval/model_attribution_appendix.md` | the attribution attempt (`344a1222…`) — **read it together with Part 11 rows 3, 6, 15, 19, 22 and §10.4, which correct four of its statements** |

### 12.4 Environment, and the external sources whose content is *not* archived

- Python **3.13.14**; `requests 2.33.0`, `beautifulsoup4 4.15.0`; **no `pypdf`** (hence the
  `/Type /Page` page-count method, Part 4 §4.4).
- `curl 8.14.1` / OpenSSL 3.5.6 / HTTP/2 — the only tool used for Crossref, OpenAlex and arXiv.
- Web content is **not** archived in the repo; the fetches that matter and their takeaways are
  recorded in Part 3's table. Notable endpoints: `api.crossref.org/works/<doi>` (reliable),
  `api.openalex.org/works[/doi:<doi>][?filter=…]`, `arxiv.org/abs/<id>` (the export API returned
  **429**), `nature.com/articles/<id>` (abstract present in HTML for s41586; JS-only otherwise),
  `platform.claude.com/docs/en/models/overview`, `ai.google.dev/gemini-api/docs/models`,
  `en.wikipedia.org/wiki/2026_in_science` (pageAge 24 Aug 2026), `repost.aws` (the
  `context-1m-2025-08-07` flag history), `conscienceandconsciousness.com` (Goff's blog),
  `survey2020.philpeople.org/survey/results/4874`, `academic.oup.com/nc/article/2022/1/niac011`.

### 12.5 Missing items — what cannot be reproduced from this repo, ever

1. **The raw trigram feature matrix as it existed at scoring time** (Part 6: not retained).
2. **The original scoring scripts** — several one-off heredocs were never saved; Part 8 and the
   composite arithmetic are re-derived, not replayed.
3. **`work1.zip`** and A's `gwt_diagram_prepared.png` bytes, and `image-search/…baars-theater-of-1.jpg`
   → A's entire Part 3B stays unverifiable.
4. **B's six companion files** (`part3_iit_pipeline.py`, `iit_abstract_raw.txt`, `word_frequency.csv`,
   `word_frequency.txt`, `ocr_labels.json`, the source figure) → B's Part 3B stays unverifiable.
5. **Any agent's session log, tool trace, HTTP headers or token/latency metadata** → model identity
   stays `UNRESOLVED` (Part 10 row 15).
6. **A reference corpus** of the same prompt answered by known models → the stylometric features are
   uncalibrated, so "no authorial signal" cannot be converted into "same/different model" either way.
7. **Publisher-abstract ground truth for paywalled items** (A's NRN 2016 abstract text; the
   *Philosophers' Imprint* 2020 table) → rows 13/14 of Part 3 stop at "secondary source says X".
8. **Live page bodies for the four 403s and one 401** (news18, openai.com, medicalxpress, philpapers,
   reuters.com/science) and the 404 nasa.gov blog → `UNRESOLVED`, not disproven.
9. **Rubric provenance**: no scoring document predating the scores exists; Part 2 §2.2 is a
   reconstruction of behaviour, and there is nothing to archive for it.
10. **No second rater** → nothing can support an inter-rater agreement statistic (Part 7).

---

## 13. What was done vs what should be done

### 13.A — Actually performed in the Agent 4 evaluation [PERFORMED]

1. Read all three uploads in full (40,755 + 34,373 + 9,340,316 B; C's bundle read as text blocks +
   decoded binary), and separated each bundle into report body vs artifact blocks.
2. Extracted the three report bodies to `eval/work*_REPORT.md`.
3. Resolved **28 DOIs via Crossref** and **4 arXiv IDs via abs pages** (32 distinct identifiers),
   comparing title / venue / volume / pages / year to what each agent claimed.
4. Swept **28 citation URLs** for liveness; recorded 403/401/404 as "unreachable", not "false".
5. Queried **OpenAlex** for the citation counts the agents quoted (1,543 / 1,199 / 1,791 / 271) and
   for the abstract-presence field that later explained C's error.
6. **Re-ran each agent's own Part-3A algorithm on its own data**: A 15/15 rows (incl. the `between`
   stop-list bug); B 15/15 from the 1,105-char arXiv abstract; C 15/15 abstract + ±1 token on the
   full text; recomputed C's OCR statistics from its TSV/JSON (6/6 exact).
7. **Decoded and hashed C's 20-file bundle** and compared against its manifest (18/20 as reported;
   20/20 is the corrected result — Part 11 row 1).
8. Read A's script, B's inline method description and C's three scripts as text, including the
   OpenAlex URL construction, `csv.writer` usage, and the NLTK-less stop list.
9. Verified ~25 named 2026/2025 facts against primary or near-primary sources (Abel prizes,
   COGITATE, Artemis II, JUNO, IBM/Qedma, LHS 1140 b, Wikipedia's 2026-in-science page, the Nature
   editorial quote, three 2025 *Nat. Neurosci.* items, the PhilPapers figures, the vendor spec tables
   against live first-party docs).
10. Assigned 9 dimension scores 1–10 with prose justifications, then invented weights at write-up and
    computed the weighted composite in Python (catching my own 7.9/9.3 slip → 7.8/9.2).
11. Wrote `eval/agent_forensic_evaluation.md` (§0 scope, §A table, §B red flags, §C ranking,
    §D inferred cut-offs, §E style, §F recommendations) and generated `eval/scores.csv` from it.
12. In the follow-up turn: segmented 6 spans, computed char-trigram cosine similarity + the full
    6×6 matrix, a 29-metric feature table, topic-controlled keyness, and whole-report structural
    tics; wrote `eval/model_attribution_appendix.md`.
13. Ranked the agents C > B > A and gave use-case recommendations.
14. **Not done at scoring time:** no rubric document, no blinding, no second rater, no
    pre-registered hypotheses, no statistical test, no CI/effect size, no prompt-spec compliance
    checks (source count, synthesis length), no artifact verification for A's image or B's files
    (they do not exist), and no preservation of the feature matrix.

### 13.B — Recommended for future validation **[RECOMMENDED] — never part of the Agent 4 evaluation**

These are *new* methods, proposed after the fact. None of them was used to produce the scores above,
and adopting them would produce a different, better audit.

1. **Score the prompt's own checkable specs mechanically** — source count vs "at least 8–10",
   synthesis length vs 400–600 words (measured after the fact: A 529 ✅, B 536 ✅, **C 1,126 ❌**),
   "list every URL or DOI you actually used", and per-source field completeness (title/authors/year/
   venue/2–3 sentences). This is objective, cheap and was simply absent.
2. **Write the rubric before scoring**: 1–10 anchors per dimension, an explicit "worst-verified-
   problem caps the dimension" rule, and a pre-registered weights vector, committed to the repo
   *before* the scores exist. Then publish a second independent rater's scores and report
   agreement (weighted κ or ICC) — with n=1 rater no agreement statistic is possible, which is the
   single largest defect of Part 2.
3. **Separate instruction-following from epistemic virtue.** Since Part 3C *commands* cut-off and
   family disclosure, score "did it answer 3C" (compliance) apart from "was what it said accurate and
   appropriately hedged" (virtue); the latter needs a truth-check per disclosure, not a holistic
   judgement.
4. **Re-score two dimensions with the corrected evidence, in a visible corrigendum rather than an
   edit** — (a) B's d6 7 → 5 (six promised files, zero delivered: the same defect class I penalised
   A for); (b) C's d9 10 → 9 (one of its four self-corrections is `UNRESOLVED`). Recomputed exactly:
   C = 146.5/16 = **9.15625**, B = 122.5/16 = **7.65625**, so the C−B margin goes from +1.4375 to
   **+1.50** (Δ +0.0625) and the ranking is unaffected — which is stated precisely so the correction is
   not read as outcome-determining.
5. **Design for identifiability in the stylometry.** A 3×2 grid cannot separate author from topic
   (Part 9). Use ≥3 sections per author per topic (so same-author *same*-topic pairs exist), ≥20
   authors, length-matched spans, a held-out topic axis, and report the power floor before running
   any exact test. Calibrate against a reference corpus of the same prompt run by known models;
   without it, "no signal" is uninterpretable.
6. **Fix the preprocessing before it is trusted**: Unicode NFC, fenced-code-block removal (not just
   inline spans), citation-marker removal (`[n](url)` → nothing, leaving no `[n]()` residue), heading
   stripping, boilerplate/header removal, and TF-IDF + top-k vocabulary as a robustness arm.
7. **Artifact protocol**: require SHA-256 per file *and* ship every binary (A's zip and B's six files
   would have settled both image claims); always extract in **binary** and record the extraction
   command; publish the extractor. Add a page-count/decoder check with a real PDF library and read
   image headers properly (my `IEND` tail test produced false negatives).
8. **Retrieval-contamination controls**: log each fetch (URL, timestamp, HTTP status) into the
   bundle so "did it know or did it retrieve" becomes answerable; re-verify 2026 claims against two
   independent primaries; record when a claim was *checked and found absent* vs *unfetchable* — my
   four 403s/one 401 belong to the second class.
9. **Reproducibility hygiene**: keep a single analysis script (like `eval/regenerate.py`) instead of
   heredocs, save the feature matrix as CSV/parquet, commit outputs with their hashes, and never
   attach a number to the word "confidence" unless a defined procedure produced it.
10. **Report uncertainty without pretending to have power**: with n=3 subjects, replace "p-values"
    with exact enumeration statements ("6 of 15 partitions", "the smallest attainable one-sided
    p is 0.0667") and replace my invented `0.95 / 0.45` with the DIRECT / WEAK / SPECULATION tags
    used in Part 10.

---

## Appendix — corrections a reader should apply to the two earlier documents

| Document | Location | Replace | With |
|---|---|---|---|
| `agent_forensic_evaluation.md` | §0 table, C row; §C; §F | "18/20 files hash-verify … the two CSVs miss by exactly one trailing newline" | **20/20 files hash-verify byte-for-byte; the CSVs' CRLF endings match `csv.writer` output and are evidence of execution.** (The 18/20 came from my text-mode read.) |
| `agent_forensic_evaluation.md` | §B, Agent C item 1 | "false superlative; scope limit not acknowledged" | "superlative false against reality but **faithfully produced by its own shipped query**; the exclusion is caused by `has_abstract:true` (Tononi 2016 has no OpenAlex abstract) and **is** visible in the script" |
| `agent_forensic_evaluation.md` | §0 table + §F, B row | "no artifacts — by choice" | "B **named six companion files as produced and downloadable** and delivered none; the claims are unverifiable (not false)" |
| `model_attribution_appendix.md` | §A1 Result 1 | "Topic explains the signal; authorship does not" | "Directionally consistent, **not testable**: agent and topic are perfectly aliased in a 3×2 design; exact W = −0.103058, P(W ≤ obs) = 0.4000, floor 1/15" |
| `model_attribution_appendix.md` | §A2 | "only A and C ran OCR and published confidences while claiming no vision; B declined" | "**All three** claimed no direct vision and **all three** published OCR confidence statistics (A Tesseract 27 words/92.2 %; B RapidOCR 22 boxes/0.74–0.91; C both engines). The contrast does not exist." |
| `model_attribution_appendix.md` | §A3 column "Confidence" | 0.95 / 0.95 / 0.45 | tags: **DIRECT** (same platform) / **UNRESOLVED** (identification) / **SPECULATION** (three families). No procedure produced those numbers. |
| both | anywhere a `%` qualified claim-level accuracy | e.g. "~100% of C's 2026 claims survived" | "every one of the 2026/2025 items I checked for C matched its source; I checked a subset and did not compute a rate" |

*End of reconstruction. Sections 1–4, 6 and 11–12 are verifiable from the files in this repo; §5 and
§7 are reproducible with the two commands in §12.2; §13B is explicitly not part of the original work.*
