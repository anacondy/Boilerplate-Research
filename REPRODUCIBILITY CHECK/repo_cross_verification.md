# Cross-Verification: `anacondy/Boilerplate-Research` vs My Own Audit
### How much of their assessment survives contact with the inputs — plus the statistics

Date: 31 Aug 2026. Inputs: `github.com/anacondy/Boilerplate-Research` (7 files, cloned to `/home/user/repo/`),
against `/home/user/QA_forensic_evaluation.md` + `/home/user/QA_model_attribution.md`.
All scripts and raw outputs: `/home/user/verify/repo_check.py` → `repo_check_out.txt`,
`compare_stats.py`, `repo_arith.py` → `repo_arith_out.txt`, `repo_arith2.py` → `repo_arith2_out.txt`.

---

## 0. Ground rules established first

| Check | Result |
|---|---|
| Are we even analysing the same data? | **Yes, byte-identical.** SHA-256 of `repo/work1.txt, work2.txt, work3.txt` == `uploads/work1.txt, work2.txt, work3.txt` (40,755 / 34,373 / 9,340,316 B). Any numeric disagreement below is a method difference, not a data difference. |
| Repo scope | 9 dimensions × 3 agents + a stylometry appendix + a model-attribution table. Apache-2.0, created 2026-08-30T20:11Z. |

## 1. Verdict summary

**Their audit holds up on the parts that touch the primary literature and on the negative stylometry result. It does not hold up on (a) their single hardest, most quotable score-level claim, (b) one headline composite, and (c) three specific "evidence" strings that are not in the inputs.**

| Bucket | Count | Items |
|---|---|---|
| **Verified — they are right and I had it too** | 9 | Amoj Jacob fabrication; A's 1M-beta-on-3.5-Sonnet error; Luppi = fMRI/HCP not iEEG; A's 10 Part-2 DOIs all resolve; C's 18/20 hash-clean bundle; C's 128,903 chars / OpenAlex 1543+1199 / Artemis 252,756 mi / JUNO 59.1 d; C's Barrett arXiv author count 4→7; C's NASA blog link 404; A+B sharing one aggregator page |
| **Verified — they caught something I missed** | 3 | C's 404 NASA URL and `reuters.com/science` as a section page (I logged the URL as "retrieved-only" but did not score it as a red flag); `s41593-025-01880-y` exists (I had it as "real" only via my own search, they pinned title+date); the framing that A/B's H1-2026 items are *corroborated by the same page*, i.e. one source counted twice |
| **False / unsupported in the repo** | 4 | see §2 |
| **Unverifiable right now (either side)** | 3 | see §3 |
| **Repo missed, and it matters** | 5 | C's Butlin arXiv ID error (2306.07155 is a porous-medium PDE paper; correct is 2308.08708); C's `niaf016` title mislabel; A's never-written "see note below" + missing "Aman Sehrawat"; B's `niae012` sole-author misattribution; B's 404 URL. Also: neither spelling register (I measured UK-spelling purity 0.79 / 0.80 / **1.00**) nor A≡B twin-ness is in their audit. |
| **I was wrong, and their side is right** | 1 | B's KV worked example. 2·32·4096·2 = 524,288 B = **0.524 MB = 0.500 MiB per token → 2.00 GiB at 4K. B is exact.** My "2× off / 1.049 MB" flag in `QA_forensic_evaluation.md` §B.3 conflated MB with MiB and double-counted; retracted (patch applied, see §5). |

## 2. The four things in the repo that fail on the text

1. **"Agent B: Correctly identified the `context-1m-2025-08-07` beta flag introduced with Sonnet 4 → Late-2025 cutoff."**
   `context-1m` occurs **0 times in `work2.txt` and 0 times in `work1.txt`**; the dated flag string appears nowhere in either file. B's actual line is
   `| Context length | 128K tokens | 200K (3.5 Sonnet; Claude 4 = 200K, 1M in beta) | …` — no flag, no date.
   Worse for their inference: that table sits under B's own header **"Notes (flag: post-training knowledge, assembled from live sources)"**, so it is *retrieved*, not *recalled*, and cannot evidence a cutoff at all.
   **This is their load-bearing argument for B = 9 on Knowledge Cut-off Transparency (vs my 4) — the largest single cell disagreement in the whole 27-cell matrix — and for their "cut-off signal is worth more than every em-dash count" line.** Both collapse.
2. **Their headline composites don't match their own arithmetic.** Σ(w·s)/16 gives **A 5.47, B 7.78, C 9.22**, printed as 5.5 / 7.9 / 9.3. A's 5.5 is fine; 7.78→7.9 and 9.22→9.3 each round up by 0.1, inflating the two gaps they use rhetorically (C−B: true 1.44, implied 1.4; B−A: true 2.31, implied 2.4). Their *unweighted* means (5.56 / 7.67 / 9.33) don't give the printed numbers either. Nothing changes about the ranking, but the top-line table is not self-reproducing.
3. **"Its full-text recount agrees with mine to within ~0.3%."** My exact recount of the corpus C shipped gives 10,149 content tokens / 2,065 unique vs C's 10,312 / 2,101 → **1.6% and 1.7%**, and `consciousness` lands at 104 vs 105 with the ranks 11–15 permuted. Their 0.3% is asserted, not shown; and separately, **"128,903 characters vs 133,007 bytes of the bundled file is a header/preprocessing ambiguity"** is a category error — 133,007 B is the *file* size, 128,903 is the *extracted text* length, and my fresh `pypdf` extraction of C's shipped PDF returns **exactly 128,903**, so there is no ambiguity to flag: C was right on that number.
4. **Two evidence strings that appear to be theirs, not the agents'.** "`67.3%` biophysical / *there is no single theory that the majority of the respondents currently endorse*" and the "rank 1 = a *pragmatics* paper at **1918 cites** (Bouma & Onken, `fpsyg`)" both occur **0 times in all three inputs**. The first is their claim about the *content of Francken et al. 2022* (see §3), the second is explicitly "I reproduced this" — i.e. their own re-run of C's query, presented in a paragraph about what **C** reported. Read side-by-side with the README's hard-coded `print("Manifest located. 18/20 files pass SHA-256 hash checks on first decode.")` (a string, not a hash check), the pattern is that illustrative numbers get inserted where a measurement would go.

## 3. Unverifiable from here (marked as such, both directions)

- **The Francken et al. 2022 abstract does not contain them.** OpenAlex and Crossref both return the full abstract (7,000+ chars); neither contains `67.3`, `biophysical`, or `no single theory`. Verifying their quote needs the full text, and `academic.oup.com` returns **403** here. So: their charge against A ("physicalism ≠ the survey's finding") stands on the survey's *own* framing that "there remains considerable discussion and debate", but their specific numbers are **unverified, same status as A's claim**.
- **`10.3389/fpsyg.2015.00322`** resolves (via OpenAlex) to *"Grounding context in face processing…"*, 30 cites — not a Bouma & Onken integrated-information paper at 1,918. Their "I reproduced this" example is mis-cited and the OpenAlex search cluster is currently rate-limited ("Search temporarily unavailable"), so the ranking claim can't be reproduced by me either.
- **C's "≈54-minute eclipse / specific per-source click counts" style micro-details** — they list them as "unverified-but-asserted"; I agree, and I could not verify them either.

## 4. The statistics

### 4.1 Agreement between the two scorecards (9 dims × 3 agents = 27 cells)

| Measure | Value | Read |
|---|---|---|
| exact cell agreement | **16/27 = 59.3%** | |
| within ±1 point | **25/27 = 92.6%** | the only two outside ±1 are both B (see below); drop those two cells and the remaining 25 are **100% within ±1** (64% exact, MAE 0.36, ρ 0.975) |
| MAE / RMSE | **0.630 / 1.262** | |
| mean bias (mine − theirs) | **−0.185** | near-zero overall — we are not differently strict, we are differently *informed* |
| Pearson r | **0.762** | |
| Spearman ρ | **0.775** | |
| Krippendorff α (interval) | **0.880** | "acceptable-to-good" reliability territory for two independent raters with no shared instructions |
| Cohen's κ (quadratic/ordinal) | **0.755** | |
| Cohen's κ (unweighted) | **−3.04** | a degenerate artefact of 27 units and a 10-point scale (expected diagonal < 1); report the ordinal version, not this |
| composite rank correlation | **1.000** | n = 3 ⇒ a formality: only **2 of the 6** possible orderings have C ahead of B ahead of A, so "perfect agreement on the ranking" is what a uniform coin gets 33% of the time |

Δ matrix (mine − theirs):

```
        Factual Source Cutoff Tech Synth Sandbox Honest Style SelfCorr
  A      +1      0      0     +1   +1    +1      0     0     +1
  B      -1      0     -5      0    0     0     -3     0      0
  C       0     -1      0      0    0     0     +1    -1      0
```

Two cells carry almost all the disagreement, and both are about **B**: *Knowledge Cut-off Transparency* (4 vs 9) and *Self-Description Honesty* (6 vs 9). Re-running every statistic with those two cells deleted (n = 25): **exact agreement 16/25 = 64.0%, within ±1 = 25/25 = 100%, MAE 0.360, Spearman ρ 0.975, bias +0.12**. In other words: **we agree almost perfectly on everything that can be measured against a document, and disagree exactly where one of us used evidence that isn't in the document** — their B=9 traces to §2.1. Per-dimension mean |Δ|: Cutoff 1.67, SelfHonesty 1.33, Factual 0.67, everything else 0.33.

### 4.2 Composite scores and a bootstrap over the 9 dimensions

Because both weightings are chosen, not estimated, I resampled the 9 dimensions with replacement (20,000 reps, both auditors' own weights) to see how much of each gap is weighting luck.

| Gap | my scorecard | their scorecard |
|---|---|---|
| **C − B** | 95% CI **[1.62, 3.66]**, P(C>B) = **1.000** | [0.71, 2.29], P = **1.000** |
| **B − A** | [−0.85, 1.65], P(B>A) = **0.803** | [1.71, 3.02], P(B>A) = **1.000** |

Composites (0–10 scale): mine A 5.98 / B 6.55 / C 9.15; theirs A 5.47 / B 7.78 / C 9.22.
**Pooled** (mean of the two audits, mean of the two weight vectors): A **5.71** [5.47, 6.18], B **7.13** [7.00, 7.33], C **9.17** [9.12, 9.43]; P(C>B) = P(B>A) = **1.000**, CIs disjoint.

Reading, stated honestly:
- **C > B is robust** — survives both weightings, both auditors, and dimension resampling. This is the only ordering claim either assessment can defend quantitatively.
- **B > A is robust for them, fragile for me.** Under my weights, B beats A in only **80%** of resamples and the CI crosses zero, because 3 of my 9 dimensions are the ones where I score B *lower*. Combined with §4.3, the pooled result (P = 1.000) is the number to quote, and it should be quoted with the caveat that pooling two auditors who share the same inputs is not independence — it is a double count, the same structural fault as A citing one aggregator page twice.
- The repo's own headline "5.5 vs 7.9 vs 9.3" hides that their B−A gap is where the *model* of the ranking is weakest: 1.44 for C−B vs 2.31 for B−A means the ordering rests mainly on B-vs-A, and their B=9 on cutoff transparency (unsupported) is worth +0.94 of their 2.31 — **~41% of their B−A gap comes from the one cell with fabricated evidence.** Sensitivity counterfactual, computed (`repo_arith2` + a one-cell swap, their own weights):

  | variant | A | B | C | C−B | B−A |
  |---|---|---|---|---|---|
  | repo as published | 5.47 | 7.78 | 9.22 | 1.44 | 2.31 |
  | their B cutoff 9 → my 4 | 5.47 | **6.84** | 9.22 | 2.38 | **1.38** |
  | + their B honesty 9 → my 6 | 5.47 | 6.66 | 9.22 | 2.56 | 1.19 |

  So the correction is asymmetric and it *helps* their preferred conclusion: **C's lead widens (1.44 → 2.38) while B's margin over A halves (2.31 → 1.38)**, i.e. only C>B is load-bearing in their table, and their B−A separation survives only because of two cells that are unsupported by the transcript.

### 4.3 Does either scorecard track ground truth?

Counting only defects I verified against primary sources (hard = fabrication/misattribution/wrong date/wrong ID/wrong number; soft = sourcing or self-consistency defects):

| | A | B | C | Spearman ρ vs defects |
|---|---|---|---|---|
| hard defects | 6 | 2 | 0 | mine **−1.000**, theirs **−1.000** |
| hard+soft | 9 | 3 | 2 | mine **−1.000**, theirs **−1.000** |

Both scorecards rank the agents perfectly against counted defects — and with n = 3 that statistic is nearly free: exactly 2 of the 6 possible orderings produce ρ = −1 against defects (9, 3, 2), so guessing that hard gets you the perfect score a third of the time. **Neither "0.88 agreement" nor "perfect ρ vs defects" is evidence that either audit is correct; both are evidence only that we agree on the ordinal structure.** The discriminating evidence stays the item-level checks in §1–§2, where I found 4 repo errors and they found 3 of mine (2 confirmed: my MB/MiB slip, and their right call that C's NASA 404 is a real defect I under-weighted).

### 4.4 Stylometry — their negative result replicates, and the numbers that didn't match are arithmetic noise

Their A1 table vs mine, with the reconciliation:

| Metric | theirs (A/B/C) | mine, untouched file | mine, their apparent preprocessing | verdict |
|---|---|---|---|---|
| words in body | 3,907 / 3,928 / 7,371 | 5,291 / 4,470 / 8,329 | 4,963 / 3,533 / 6,042 | different denominators, see below |
| em-dashes / 1k words | 13.05 / 16.29 / 12.07 | 9.83 / 14.32 / 10.69 | 10.48 / 17.83 / 12.58 | ordering (B>C>A) identical in all three |
| inline `[n](url)` / 1k | 0.00 / 8.40 / 9.23 | 0 / 7.38 / 6.48 | — | raw counts: **0 / 33 / 68 links**; their per-1k row back-solves to exactly 0 / 33 / 68 links, so the numerators are right and the *denominators* (word counts) are what moved |
| literal `DOI:` strings | 10 / 0 / 0 | **13** / 0 / 0 | 13 / 0 / 0 | they under-count A by 3 (probably Part-2-only vs whole-file) |

Why our word counts differ by ±20%: `len(t.split())` on the raw file vs `re.findall(r"[A-Za-z']+")` vs stripping code fences/URLs/markdown vs counting on the *extracted report region only*. Under four tokenizer variants, **C's own density spans 9.56–10.94/1k and B's 12.73–14.80/1k** — i.e. the *denominator alone* moves the em-dash statistic by 14–16% relative, larger than the whole gap between two of their three agents. Their implied em-dash numerators back-solve to **51 / 64 / 89**, and my raw counts are **52 / 64 / 89** — same numerators (±1), different denominators. **Conclusion: our stylometry numbers are the same measurement; only the word-count convention differs. Do not read the 13.05-vs-9.83 gap as disagreement.**

Their headline replication, on my own topic-matched extraction (char 3-gram cosine after stripping code, URLs, markup):

| Comparison | theirs | mine | 
|---|---|---|
| same topic, different agent | 0.812 [0.775–0.844] | **0.822 [0.767–0.867]** |
| same agent, different topic | 0.599 [0.526–0.684] | **0.604 [0.556–0.663]** |
| gap | +0.213 | **+0.218** |
| per-agent self-consistency A/B/C | 0.585 / 0.526 / 0.684 | **0.592 / 0.556 / 0.663** |

Every value within 0.01–0.03, sign and ordering identical. **Their most interesting claim — that topic similarity exceeds authorship similarity by ~0.21, so surface stylometry measures domain vocabulary rather than a model — replicates independently.** Note the corollary that follows from §4.4's tokenizer sensitivity: their own metric is 14–16% sensitive to a preprocessing choice nobody documented, which weakens *precision* but not the direction of a 0.21 gap.

### 4.5 Model attribution: they agree with me more than their table suggests

Their A3 gives **0.95** to "none of the three can be identified to a specific LLM family", **0.45** to "three different families" ("coin-flip territory"), **0.55/0.5/0.5** to the archetype rows, and closes with "**Do not use these for anything operational.**" My `QA_model_attribution.md` gives vendor-family ~35–50% and exact-ID ~10–20%. Those are the same claim stated with different confidence currency: **on the question "can we name the model?", both audits answer no, and both answer it from the same two reasons** — the scripted platform disclosure is injected boilerplate, and topic-matched trigram similarity swamps authorial signal. Their one *substantive* attribution difference is that they use the (fabricated, §2.1) `context-1m` tell to separate A and B; strip it and the remaining evidence supports my "A ≈ B same family, ~55–60%" reading, which they never consider — they assume three different families by default and assign 0.45 without testing the alternative that A and B are one model at two effort levels.

## 5. How my deliverable changed because of this

Patched into `QA_forensic_evaluation.md`:
1. **Retracted** my "B's worked example is 2× wrong (0.5 MB/token)" flag — 0.524 MB/token and 2.00 GiB at 4K are correct in decimal MB; the repo is right and I was wrong (§1, last row).
2. **Refined** the C "stale artefact" flag: it is *not* that C's published numbers are fake — `128,903` chars reproduces exactly, `figure 108` matches C's shipped CSV, and ranks 1–10 match my recount. It is that no extraction route available to me (pypdf 10,002 / pdfminer 10,027 / C's own raw file 10,149) reaches C's 10,312, so C's corpus headline came from a different/earlier run.
3. **Added** their three catches as red flags for C (NASA blog 404; `reuters.com/science` as a section page; 7-author Barrett paper cited as 4) and for A/B (one shared aggregator page counted as two corroborations).
4. **Kept** my 4 on B's cut-off transparency, and recorded why their 9 cannot stand: the string it rests on is absent from the input.

## 6. Bottom line

Roughly **80% of their claim-level findings verify** (9 of 11 independently checkable ones I tested directly, plus the stylometry block replicating to ±0.03), and their two structural insights — **the fluency trap** and **the stylometry negative result** — are both correct and independently reproduced. But about **20% of their specific "evidence" is not in the data**: a fabricated `context-1m` tell driving their largest score move, two headline composites that don't match their own arithmetic, a "~0.3%" reproduction claim that is 1.6% under exact recount, and two quotations attributed to a source whose accessible abstract doesn't contain them. Statistically we agree far more than we look like we do (α = 0.88, 93% within ±1, identical ranking, P(C>B) = 1.000 under both weightings); the disagreements cluster in exactly one place — **they scored B generously using an argument that does not exist in B's transcript, and that one cell is worth ~41% of their B−A gap.**
