# Appendix — Which LLM is which? (model-attribution attempt + stylometry)

Added after the main evaluation, in response to a follow-up asking whether the three reports can be matched to specific LLM families, with match percentages.

## A1. The experiment

To avoid vibes, I isolated **topic-matched sections** (each agent's KV-cache section and each agent's literature-synthesis section), stripped code spans/URLs/markup, and measured:

- char-trigram cosine similarity (orthographic texture)
- lexical diversity, sentence-length moments, function-word share, punctuation rates, readability
- topic-controlled "keyness" (words over-used by one agent across *both* sections vs. both others combined)

### Result 1 — topic dominates, authorship barely registers

| comparison | mean char-3gram cosine |
|---|---|
| **Same topic, different agent** (KV×KV, syn×syn) | **0.812** (range 0.775–0.844) |
| **Same agent, different topic** | **0.599** (range 0.526–0.684) |

Text about the same subject from different writers is ~0.21 more similar than the same writer on different subjects. **Topic explains the signal; authorship does not.** Any "this reads like model X" judgement built on such features is measuring *domain vocabulary*, not a model.

Weakest per-author consistency (i.e. most topic-driven voice): **B** (0.526 across its two sections). Most self-consistent: **C** (0.684). A: 0.585.

### Result 2 — keyness found almost nothing author-specific

Over-used vocabulary after controlling for topic: A → `keys, both, status, even, workspace` (all topic); B → `vectors, journal, step, batch, complexity` (all topic); C → `connectivity, data` (topic/source-driven: its extra re-analysis sources). No agent-marker vocabulary of the kind stylometry needs.

### Result 3 — the strongest "style" differences are scaffold formatting, not models

| whole-report metric | A | B | C |
|---|---|---|---|
| words in report body | ~3,907 | ~3,928 | ~7,371 |
| inline markdown links `[n](url)` per 1k words | **0.00** | 8.40 | 9.23 |
| literal `DOI:` strings | **10** | 0 | 0 |
| em-dashes per 1k words | 13.05 | 16.29 | 12.07 |

A uses *no* inline `[n](url)` links and writes bare `DOI:` text; B and C both use inline markdown links and DOI-URLs. That is a **citation-formatting convention** (what the harness/prompt asked for, or what the agent chose), not a model signature.

## A2. The self-description evidence — which cuts *against* identification

All three answered the "what model are you" question with the **same scripted disclosure** of the same form: *"Arena's Agent Mode routes across many models (Claude, ChatGPT, Gemini, Grok, Qwen, Kimi …), so I won't name one."* C's version is near-verbatim identical to that boilerplate.

Implications:
1. The single most obvious "identity tell" in these reports is **platform-injected, not model-generated** — so it is unusable as evidence, and it is exactly the sentence a reader would otherwise over-read.
2. None of the three fabricated a vendor name, so there is no "confession" to exploit.
3. A did *slightly* differently: "the platform does not let me state exactly which underlying model I am right now" — more hedged, less boilerplate-reciting. Weak evidence of a different scaffold/prompt variant, not of a different model.

## A3. Honest attribution table

| Claim | Confidence | Basis |
|---|---|---|
| None of the three can be identified to a specific LLM family from these texts | **0.95** | Results 1–3 above; no reference corpus exists to calibrate against; markdown+prose style of frontier models overlaps heavily in 2026 |
| All three ran on the **same platform** (Arena Agent Mode) | **0.95** | Shared tool vocabulary ("live preview", "0.0.0.0", "~10 speech clips per turn", "sandboxed workspace"), same date-stamping, same vision-tool failure, `/home/user` workspace |
| The three were served by **three different** model families | **0.45** | Three different deliverable-bundle conventions + three different depth profiles; equally explained by three sampling runs of one model or by prompt variation. Coin-flip territory |
| **C** = a current high-effort/"thinking"-mode frontier flagship | **0.55** | Deep provenance discipline, verbatim error strings, self-imposed word budget, first-party-doc grounding. Caveat: this is *scaffold behaviour*, which any model in that mode reproduces |
| **B** = a frontier model with a **slightly less** agentic/verification-oriented mode (mid-effort) | **0.5** | Accurate and well-hedged, but zero artifacts, secondary sources accepted as-is, no mid-run reversals |
| **A** = a mid-tier or smaller/cheaper model, or a frontier model with weaker retrieval | **0.5** | Surface-correct prose + one invented controversy + misattributed survey finding + no method trace. This pattern is generic "confident-completion under a long checklist" behaviour, not a family marker |
| Reverse assignments are *not* ruled out | — | Nothing in the measured features would be violated by swapping A/B/C labels |

**Do not use these for anything operational.** The defensible output of this exercise is the negative result in A1/A2.

## A4. What *is* defensible: the knowledge-cutoff inference (recall horizon measured from model-lineup knowledge)

The cleanest independent clock in these reports is **which frontier-model releases each agent lists from recall** in its architecture table (retrieved items are marked as such by each agent).

| Agent | Newest release present **from recall** | Releases **absent** (and their real dates) | Recall-horizon inference | Confidence |
|---|---|---|---|---|
| **A** | Claude 4 Sonnet (2025-05); Gemini 2.5 Pro/Deep Think; DeepSeek-R1 (2025-01) | GPT-5 (2025-08), Claude Sonnet/Opus 4.5 (2025-09/11), Gemini 2.5 Pro GA Deep Think (2025-07) | **cutoff ≈ mid-2025** (May–Jul 2025) | 0.75 |
| **B** | Claude **Opus 4.5**; "Gemini 3 (late 2025)" as a *named* item; 1M-context beta `context-1m-2025-08-07` era | GPT-5.1/5.2 (2025-11/12), Opus 4.6/4.7 (2026-02/04), GPT-5.4 (2026-03) | **cutoff ≈ Sep–Nov 2025** (late-2025) | 0.80 |
| **C** | DeepSeek-V3/R1 architecture internals, MQA/GQA/PagedAttention/StreamingLLM/H₂O/FlashAttention from memory with correct arXiv IDs (2019–2023 papers) | Nothing — all 2025–26 facts are explicitly tool-grounded | **parametric ≈ 2024 → early-2025; task-time knowledge live to ~2026-08** → effectively **post-mid-2026 / live** | 0.85 |

Cross-checks supporting this ordering:
- B correctly wrote "1M beta on Claude 4" where A wrongly wrote "1M beta on **3.5** Sonnet" — the beta flag (`context-1m-2025-08-07`) shipped with Sonnet 4 in Aug 2025, i.e. **inside B's recall, after A's**. That single error is worth more as an identity/clock signal than every em-dash count in the corpus.
- C's OpenAlex citation counts (1543 / 1199) and the "2026 in science" items it cited match the live sources exactly; C is the only agent whose 2026 claims survived external re-verification at ~100%.
- A's and B's H1-2026 items overlap near-perfectly (Migdal effect, little red dots, GPNMB CAR-T, AI vaccine) and both trace to the same Wikipedia "2026 in science" page — i.e. **shared aggregator, not shared memory**. Do not read their agreement as corroboration.

## A5. Evidence that would actually settle it (not present in these uploads)

1. Raw session logs / token-usage and latency metadata; HTTP `server` or model headers from the generating session.
2. The agents' unedited private reasoning traces (the reports each contain *descriptions* of reasoning, not traces).
3. A reference corpus of the same assignment completed by each candidate model, so the trigram/keyness features could be calibrated instead of guessed.
4. Undecoded artifacts: A's `work1.zip` (would show whether `gwt_diagram_prepared.png` and a real Tesseract log exist — its only falsifiable claim) and B's `ocr_labels.json` / `part3_iit_pipeline.py`.
