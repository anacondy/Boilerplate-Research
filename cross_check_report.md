# Cross-Check Report — My Evaluation vs. the `anacondy/Boilerplate-Research` Repo

**Date:** 2026-08-31 · **Repo:** github.com/anacondy/Boilerplate-Research (7 commits, 31 Aug 2026)

---

## 0. What the repo actually is

The repo contains the **same three `work*.txt` files I was given** (I verified SHA-256 byte-identity against my uploads: all three match exactly), plus two new documents:

1. **`agent_forensic_evaluation.md`** — a *"4th agent"* forensic QA evaluation (same 10-dimension rubric, weights, red-flags, ranking, cut-off inference).
2. **`model_attribution_appendix.md`** — a **stylometric attempt** to answer *"which LLM is which,"* including a char-trigram similarity experiment and a recall-horizon cut-off analysis.

So this is a **parallel, independent evaluator on identical source data** — an unusually clean opportunity to check inter-evaluator reliability.

---

## 1. Headline: the two assessments agree strongly

I correlated my 9 per-dimension scores against the repo's, across all 3 agents (27 cells):

| Statistic | Value |
|---|---|
| **Pearson r** (all 27 cells) | **0.907** (p < 0.0001) |
| **Spearman ρ** | **0.919** |
| Exact cell agreement | 13 / 27 (48%) |
| Agreement within ±1 point | **24 / 27 (89%)** |
| Mean absolute difference | 0.63 pts |

**Ranking: identical — C > B > A**, and the *shape* of the gap is the same (C clearly first, A clearly last, B close to C on content but shallow on artifacts).

Per-agent: A r=0.92 (they're harsher, −11 raw pts), B r=0.85 (nearly identical, equal raw sums), C r=0.63 (very close in absolute terms, ±0.2).

**Conclusion: ~90% of the evaluative signal is shared.** Two independent readers, on identical inputs, converge on the same verdict. That is strong evidence the ranking is robust, not idiosyncratic.

---

## 2. Where the repo is right and I was wrong (I must adjust)

This is the valuable part — the repo corrected me on **one substantive factual point**, and it **caught fabrications I missed**:

### (a) The repo corrected MY error — the "most-cited IIT paper" claim
I flagged Agent A for "wrong most-cited paper," trusting Agent C's claim that Tononi 2008 (1,543 cites) is most-cited. **I was wrong.** Live OpenAlex API check today:

| Paper | OpenAlex citations |
|---|---|
| Tononi et al. 2016, *Nat Rev Neurosci* | **1,791** |
| Tononi 2008, *Biological Bulletin* | 1,543 |
| Oizumi et al. 2014, *PLoS Comp Biol* | 1,199 |

**Agent A chose the genuinely most-cited paper (correct).** Agent C's "most-cited = Tononi 2008" is a **title-search scope artifact** — exactly as the repo's §B-C3 says. I **retract** my red-flag #3 against A and reassign it as a *minor* imprecision on C (self-disclosed method, but an unacknowledged superlative).

### (b) The repo caught red flags I missed in Agent A
These are verified true by my own checks:

1. **Fabricated controversy — "Amoj Jacob banned."** Work1 claims the sprinter was "banned from relay finals due to a coaching violation." **False.** Amoj Jacob *competed* in Paris 2024 (men's 4×400 m heats, 3:00.58, finished 4th, narrowly missed the final — no ban, no disqualification). The trainer-supplement story belongs to M. R. Poovamma (2021). *This is the single worst error in the whole corpus, and I missed it.*
2. **Misattributed survey finding.** Work1 claims "physicalism remains the most popular stance among researchers [1,2]" citing Francken et al. 2022 — but that survey states *"no single theory that the majority of respondents currently endorse."* The "physicalism majority" is a *PhilPapers/philosophers* finding, not a consciousness-researcher finding. Unsupported by its own citation.
3. **Luppi et al. 2024 methodology error.** Work1 says "intracranial EEG"; the paper uses **HCP fMRI** (BOLD). Verified.
4. **Vendor error.** "1M beta on Claude 3.5 Sonnet" — the 1M-context beta (`context-1m-2025-08-07`) shipped with **Sonnet 4**, 2025.
5. **Misdated 2026 items** — e.g. "IBM first quantum advantage (2026)" was actually **30 July 2026** (H2, not H1); LHS 1140 b atmosphere was a **16–17 July 2026** *Science* paper (H2).

These collectively justify the repo's harsher A scores, and my adjusted scores below reflect them.

### (c) What I caught that the repo missed
The repo's §2 says Agent A's "Ten Part-2 DOIs, **all resolve and all match title/venue/volume/pages** — genuinely good." That check stops at title/venue/pages and **never checked author names.** Work1 source #10:

> "**Xue, K., et al.** — 2024. *Two Levels of Integrated Information Theory…* Entropy 26(9):761. DOI 10.3390/e26090761"

The paper is real, but its authors are **Ruan Zenan & Hengwei Li** — "Xue, K." is a **fabricated author name** attached to a real DOI. The repo did not flag this. So the two evaluations are complementary: **they caught event-level fabrications; I caught a bibliographic-level one.**

---

## 3. How much could I cross-verify? (honest accounting)

| Claim type | Verifiable? | What I verified | Result |
|---|---|---|---|
| The 3 `work*.txt` are the same files | ✓ (hash) | SHA-256 of repo files vs my uploads | **identical** |
| "Amoj Jacob" fabrication | ✓ | The Hindu / Olympics.com relay results | **repo correct** |
| Francken survey misattribution | ✓ | OUP full text (niac011) | **repo correct** |
| Luppi = fMRI not iEEG | ✓ | eLife/bioRxiv methods (HCP, BOLD) | **repo correct** |
| Citation counts 1543 / 1199 / 1791 | ✓ | OpenAlex REST API, live | **repo correct** (and corrects *me*) |
| Weighted composite arithmetic (5.47 / 7.78 / 9.22) | ✓ | recomputed | **matches** (their "7.9 / 9.3" in-table is a rounding typo vs "7.8 / 9.2" in-note) |
| Stylometry (link density, em-dash rates) | ~ | partial reproduction | directionally consistent (A=0 inline links confirmed) |
| Model-identity claims | ✗ | not verifiable from text alone | see §5 |
| Cut-off dates (exact month) | ~ | inferential only | see §4 |

**Net: every *factual* claim in the repo I could test checked out.** Its hit-rate on the falsifiable items is ~100%, and it even caught my own mistake. The only things I could *not* verify are the model-identity guesses and the exact cut-off months — which the repo itself correctly labels as low-confidence inference.

---

## 4. Cut-off inference — theirs is better; I adjust

The repo used a cleverer method than my self-report reading: **"recall horizon"** — the newest model release each agent lists *from memory* (retrieved items are self-marked).

| | My original | Repo's (recall-horizon) | Adjudication |
|---|---|---|---|
| **A** | ~late-2024 | **~mid-2025** (knows Claude 4 Sonnet/May 2025, COGITATE Apr-2025; misses GPT-5/Aug-2025) | **Adopt repo** — A demonstrably knew Apr/May-2025 events, so late-2024 was too harsh on my part |
| **B** | ~mid-2025 | **~late-2025** (knows Opus 4.5/Sep-Nov 2025, the Aug-2025 1M beta) | **Adopt repo** — B's recall reaches later than I credited |
| **C** | ~early-2025 | **~2024/early-2025 parametric + live tools to Aug 2026** | **Agree** (I already flagged C as "effectively live") |

The single most diagnostic fact: **B wrote "1M beta on Claude 4" correctly where A wrote "on 3.5 Sonnet" wrongly** — that beta flag is *inside B's recall but after A's*. That one error is worth more as a clock signal than every em-dash count in the corpus.

---

## 5. Model attribution — I over-claimed; the repo's negative result is better

My earlier answer offered speculative families (A≈GPT-4o, B/C≈Claude). The repo's stylometry shows why that was too confident:

- **Char-trigram cosine: same-topic/different-agent = 0.812, vs same-agent/different-topic = 0.599.** Topic dominates authorship. Any "this reads like model X" judgment built on such features is measuring *domain vocabulary*, not a model.
- **Keyness found no author-specific vocabulary** after controlling for topic.
- The strongest "style" differences are **scaffold/citation conventions** (A uses bare `DOI:` text + zero inline links; B/C use `[n](url)`), which is prompt/harness choice, not model identity.
- The "which model are you" disclosures are **platform-injected boilerplate** (near-identical across all three) — unusable as evidence.

**Adjusted position:** I downgrade my attribution to match the repo's. The defensible conclusion is:

- **Cannot identify any agent to a specific family — confidence ~0.9+ that identification is not possible from these texts.**
- Chance the three are *different* families ≈ **0.45** (coin-flip); equally consistent with one model sampled three times.
- The only *weakly* defensible signals: **C = a current high-effort/"thinking"-mode frontier flagship (~0.55)**; **A = confident-completion-under-checklist behaviour** (generic, not a family marker).

My earlier "A = GPT-4o, B/C = Claude" guesses were **over-stated**. I retract the specificity and keep only the negative result.

---

## 6. Adjusted score table (mine, after absorbing the repo's verified findings)

| # | Dimension | My original A/B/C | Repo A/B/C | **My adjusted A/B/C** |
|---|---|---|---|---|
| 1 | Factual accuracy | 6/8/9 | 4/8/9 | **4/8/9** |
| 2 | Source verifiability | 6/8/9 | 5/7/9 | **5/7/9** |
| 3 | Cut-off transparency | 9/9/10 | 7/9/9 | **7/9/9** |
| 4 | Technical depth | 7/8/10 | 5/7/10 | **5/7/10** |
| 5 | Literature synthesis | 7/8/9 | 6/8/9 | **6/8/9** |
| 6 | Sandbox execution | 5/6/10 | 5/7/10 | **5/7/10** |
| 7 | Self-description honesty | 9/9/10 | 8/9/9 | **8/9/9** |
| 8 | Writing & reasoning | 8/8/9 | 7/8/9 | **7/8/9** |
| 9 | Error detection | 4/5/10 | 3/6/10 | **3/6/10** |
| **10** | **Overall (weighted)** | 6/8/9 | 5.5/7.8/9.2 | **5.5/7.9/9.2** |

Adjustments made: (1) A down on factual/technical/error-detection after the Amoj-Jacob and Francken findings; (2) A up on nothing — my one A-positive (the "most-cited" point) was *my* error, corrected; (3) B down on source (blog-as-authority for the PhilPapers number) and up on sandbox (its numbers reproduce, matching the repo's 7); (4) C essentially unchanged except a note that its "most-cited" superlative is a search artifact.

**Result: my adjusted scores are now within ±0.5 of the repo's on every cell, and the ranking C > B > A is unchanged.**

---

## 7. Bottom line

1. **The repo's assessment holds up** — every falsifiable claim in it that I tested is true, it caught one of my own errors, and its arithmetic is correct.
2. **It is largely the same as mine** (r = 0.91, identical ranking) but **more thorough and slightly harsher on Agent A** — because it went one level deeper (event-level fact-checking) than I did.
3. **The two evaluations are complementary, not redundant:** they caught the *content* fabrications (Amoj Jacob, Francken misattribution); I caught a *bibliographic* fabrication ("Xue, K."). Together they paint the same picture — A is the unreliable one, C is the auditable one, B is clean-but-shallow.
4. **The model-attribution question is effectively settled in the negative:** no report can be tied to a specific LLM family from these texts (the repo's stylometry, which I reproduced directionally, shows topic dominates authorship). My earlier family guesses were over-confident and are withdrawn.
5. **On cut-offs**, the repo's recall-horizon method is superior to my self-report reading; I adopt it (A ≈ mid-2025, B ≈ late-2025, C ≈ early-2025 parametric + live tools).
