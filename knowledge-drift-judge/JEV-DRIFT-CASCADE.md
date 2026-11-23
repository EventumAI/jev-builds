# Jev as a knowledge-drift judge: alone and in a cascade with an LLM

Measured 2026-11-05 and 2026-11-06.

## Summary

Brackets are 95 % confidence intervals.

- **At 91 % precision [83, 97], Jev decides 37 % [30, 44] of the 172 test cases on its own** (confidence 0.85 or
  more) and sends the rest to the LLM judge, which keeps the judge's accuracy to within 1 case of 172. In front of
  GPT-6.1 Terra or Claude Sonnet 5.5 this cuts cost by 29–31 % and the mean wait by 19–30 %.
- **Alone, Jev matches our production judge, GPT-6 Luna, at half the price and 8 times the speed.** Verdict accuracy
  is 0.69–0.72 against Luna's 0.67–0.70; on the drift flag both reach a balanced accuracy of 0.78–0.80 when every Jev
  drift verdict counts. Jev costs $0.09 per 1,000 decisions against $0.17 and answers in 0.3 s against 2.5–2.8 s,
  and its runs agree more often (92–96 % of cases against 83–92 %). Terra and Sonnet are ahead on the flag (0.86 and
  0.83) at 19 and 64 times Jev's price.
- **Jev's confidence is calibrated:** it says 0.69 on average and is right on 69–72 % of cases. Luna and Terra say
  0.98 and are right on 67–72 %; Sonnet says 0.83 and is right on 73 %.
- **Limits.** Jev and the LLM judges fail on the same cases: where Jev is wrong, the judge is also wrong in 56–81 % of
  those cases, against 27–33 % of all cases, so the cascade saves cost and time, not errors. In front of a judge as
  cheap as Luna the cascade costs 20 % more; it pays off above about $0.27 per 1,000 decisions. Under the production
  rule, which drops drift verdicts below confidence 0.6, Jev catches only half the drifts. Jev gives no written
  rationale.

## The task

A memory system stores facts extracted from documents. When a document changes, a judge checks whether the new text
still supports each fact and answers `supported`, `contradicted`, `unmentioned` (the fact's text was removed) or
`cannot_tell`. In production, `contradicted` or `unmentioned` with confidence 0.6 or more flags the fact as drifted.
A flag can close the fact, so a false flag is the costly error.

## Setup

- **Test set:** 184 hand-labelled cases from the documentation of two internal projects. Rules settle 12 before any
  judge. The other 172 are 32 hand-written cases and 140 from production: 70 the production judge had got wrong or
  left open, and 70 random production closures. 113 of the 172 should be flagged and 59 should not, 50 of them
  paraphrases of the fact.
- **Models:** Jev 1.14, asked one `choice` question with the four verdicts as options, on the same text the judge
  reads. GPT-6 Luna without reasoning, the production judge. GPT-6.1 Terra and Claude Sonnet 5.5, both without
  reasoning.
- **Runs:** Jev 8, Luna 6, Terra 2, Sonnet 1. Jev, Luna and Terra changed some answers between runs; ranges below
  span the runs.
- **Intervals:** 95 % bootstrap over cases. **Timing:** one client over the public internet, one call at a time.

## Each model alone

| | Verdict accuracy | Flag decision, balanced accuracy | Drifts caught (of 113) | False flags (of 59) | Mean confidence |
|---|---|---|---|---|---|
| Jev 1.14 | 0.69–0.72 | 0.71–0.73 | 57–61 | 3–6 | 0.69–0.70 |
| GPT-6 Luna | 0.67–0.70 | 0.78–0.80 | 87–93 | 11–14 | 0.98–0.99 |
| GPT-6.1 Terra | 0.72 | 0.86 | 90–91 | 5 | 0.98 |
| Claude Sonnet 5.5 | 0.73 | 0.83 | 92 | 9 | 0.83 |

- **Verdict accuracy does not separate the models:** one run's interval is about ±0.07, so the four overlap.
- **Jev is steadier than Luna:** its runs agree pairwise on 92–96 % of cases, Luna's on 83–92 %, Terra's on 95 %.
- **Production cases are the hard part:** on the 32 hand-written cases every model scores 0.91–1.00, on the 140 from
  production 0.59–0.70.
- **A third of the LLM judges' verdict errors leave the flag right**, mostly `contradicted` called `unmentioned`. The
  most common error that changes the flag is a contradiction read as `supported`.
- **Jev catches fewer drifts because of the 0.6 floor.** The LLM judges almost never give a drift verdict below 0.6
  (Sonnet once in 102), so the floor does not affect them. It drops a third of Jev's drift verdicts: without it Jev
  catches 83–88 drifts with 10–12 false flags, the same balanced accuracy as Luna (0.78–0.80).

## The cascade

Jev answers every case. When its confidence is at least τ, its verdict stands; otherwise the LLM judge decides.
Precision is the share of Jev's own decisions that are right. Each row pairs one Jev run with the judge's first run;
over all pairs, τ 0.85 takes 35.5–39 % of the cases at 90–92 % precision.

At τ 0.85:

| Judge | Jev decides | Precision | Verdict accuracy: cascade / judge alone | Cost per 1,000: cascade / judge alone |
|---|---|---|---|---|
| GPT-6 Luna | 37 % [30, 44] | 91 % [83, 97] | 0.692 / 0.698 | $0.206 / $0.172 |
| GPT-6.1 Terra | 39 % [32, 47] | 91 % [84, 97] | 0.721 / 0.721 | $1.21 / $1.70 |
| Claude Sonnet 5.5 | 37 % [30, 44] | 92 % [85, 98] | 0.727 / 0.727 | $3.96 / $5.72 |

At τ 0.75:

| Judge | Jev decides | Precision | Verdict accuracy: cascade / judge alone | Cost per 1,000: cascade / judge alone |
|---|---|---|---|---|
| GPT-6 Luna | 49 % [41, 56] | 88 % [81, 95] | 0.698 / 0.698 | $0.186 / $0.172 |
| GPT-6.1 Terra | 53 % [45, 60] | 85 % [77, 92] | 0.709 / 0.721 | $0.98 / $1.70 |
| Claude Sonnet 5.5 | 51 % [44, 58] | 85 % [78, 92] | 0.715 / 0.727 | $3.15 / $5.72 |

- **Verdict accuracy:** within 1 case of 172 of the judge alone at τ 0.85, within 2 at τ 0.75.
- **Cost:** at τ 0.85, 29 % less than Terra alone and 31 % less than Sonnet alone; at τ 0.75, 42 % and 45 % less.
  Against Luna, 20 % more at τ 0.85 and 8 % more at τ 0.75. The cases Jev passes on are the longer ones and carry
  66–68 % of the judge's cost, so the cascade pays off only with a judge above about $0.27 per 1,000.
- **False flags:** at both thresholds the cascade has exactly the judge's own false flags (with Terra, one more or one
  fewer): Jev passes those cases on.

List prices per million tokens in / out: Luna $0.10 / $0.50, Terra $1 / $5, Sonnet $2 / $10; Jev $0.042, input only.
Tokens per call: Jev about 2,130 in; Luna and Terra about 1,340 in and 72–75 out; Sonnet 2,190 in and 134 out.

## Speed

Jev answers in 0.26–0.35 s on average; the slowest of its 1,032 timed calls (6 runs) took 0.61 s. The LLM judges
take 2.5–3.2 s on average. The cascade cuts the mean wait by 19–30 % at τ 0.85 and by 36–43 % at τ 0.75, depending
on the judge.

## Jev and the LLM judges fail on the same cases

Where Jev is wrong, the judge is wrong too in 75–81 % of cases for Luna (over its runs), 70 % for Sonnet and 56 % for
Terra, against 30–33 %, 27 % and 28 % of all cases. The same was found for decision models as rubric judges
("Decision Models vs. LLMs as Rubric Judges", arXiv 2609.29769).

## Where they disagree

Jev and the judge disagree on 34 of 172 cases with Luna (Jev right in 15, Luna in 11, both wrong in 8), on 36 with
Sonnet (10, 16, 10) and on 57 with Terra (18, 24, 15). Jev's median confidence on these cases is 0.34, 0.50 and 0.59,
so most of them go to the judge.

Four patterns from the disagreements with Luna, each with a made-up example:

- **Jev misses a contradiction**, such as a value changed inside a rewritten passage. Fact: "Nightly backups are kept
  for 30 days." New text: "Backups run every night. Since the move to new storage, each copy is kept for two weeks."
  Luna: `contradicted`; Jev: `supported`.
- **Luna misnames a contradiction** (the flag is right either way). Fact: "The public API allows 100 requests per
  minute." New text: "The public API allows 60 requests per minute per key." Jev: `contradicted`; Luna:
  `unmentioned`.
- **Luna abstains where the fact's sentence was removed.** Fact: "Reports can be exported as CSV." The edit removed
  the paragraph on exports, and nothing else mentions them. Jev: `unmentioned`; Luna: `cannot_tell`.
- **Luna flags a faithful paraphrase**, the costly error. Fact: "Only the account owner can close an account." New
  text: "Closing an account requires the owner role." Luna: `contradicted`; Jev: `supported`.

## Caveats

- The thresholds were chosen on the same set; a fixed threshold on new data will do worse.
- The set is skewed to hard cases; coverage on routine checks was not measured.
- One domain: software documentation and project notes.
