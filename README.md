# Jev builds

Decision model builds by engineers who build with [Eventum](https://www.eventum.ai). Each one puts Jev, the System One model made by [TypeSafe](https://typesafe.ai), on one real decision and measures it against another way of doing the same job.

Decision models, which TypeSafe calls System One models, answer one narrow question with a typed answer and a probability, and write no text. Jev makes the judgments and code makes the decision. TypeSafe explains its name for the class in [Don't Call It a Decision Model](https://typesafe.ai/blog/dont-call-it-a-decision-model).

Eventum is an official technology partner of TypeSafe and has run Jev in its own systems since before its public launch.

This repository is an index of the [Jev build log](https://www.eventum.ai/jev-builds) on Eventum's site. Where a build's code is published, it lives in the builder's own repository, under the builder's name and license.

## The builds

In the order of the build log. Every figure is the builder's own measurement.

### Jev walks a PDF's table of contents to find the answer

A search that treats a long PDF's own table of contents as a tree. Jev ranks the sections, checks the pages in the most promising one, and stops when it judges that nothing the question needs is missing.

- **What Jev judges:** which section is most likely to hold the answer, whether a page states evidence the question needs, and whether anything is still missing. Python code walks the contents and decides when to stop.
- **Result:** returned pages that support the correct answer in 59 of 60 runs, at about 13x lower cost and 2.6x the speed of OpenAI's gpt-6.1-sol walking the same contents. Jev checking every page and the OpenAI model both scored 60 of 60.
- **Tested:** Jev 1.13.0, 10 November 2026, 20 questions over 4 PDFs, 3 runs each.
- **Built by:** [Gassyr Bakubay](https://www.eventum.ai/jev-builds/pdf-table-of-contents-search)
- **Code:** [bakubay/jev-dfs-document-search](https://github.com/bakubay/jev-dfs-document-search) (Apache 2.0)
- **Write-up:** [the build page](https://www.eventum.ai/jev-builds/pdf-table-of-contents-search), with the comparison, the limits and a 28-second video

### Jev decides which contract clauses an expensive model never has to read

Jev reads every clause of 53 license agreements against six rules. A clause it rejects on all six is skipped and never reaches the expensive model. The loss is counted against the provisions lawyers marked in CUAD.

- **What Jev judges:** six yes-or-no questions per clause, one per rule, in a single request. Code skips a clause only when all six probabilities are below the cutoff.
- **Result:** with the cutoff aimed at losing 1% of marked provisions, 31.5% of clauses skipped and 4 of 378 provisions lost (1.1%). The expensive model was not run, so the saving is an estimate. Depending on which half of the documents set the cutoff, the skipped share moved from 14.6% to 51.0%.
- **Tested:** Jev 1.14.0, 13 November 2026, 53 agreements, 3,246 clauses.
- **Built by:** [Mukammed Alimbet](https://www.eventum.ai/jev-builds/contract-clause-filter)
- **Code:** [MuhammedTech/clause-classification-with-jev](https://github.com/MuhammedTech/clause-classification-with-jev) (MIT), with [the operating point tables](https://github.com/MuhammedTech/clause-classification-with-jev/blob/main/docs/operating-point.md)
- **Write-up:** [the build page](https://www.eventum.ai/jev-builds/contract-clause-filter), with the method and the limits

### Jev sorts payment fraud alerts into five types, against GPT-4o-mini

Each simulated alert carries one transaction and 19 signals. Jev names the type of fraud and says whether to hold the payment.

- **What Jev judges:** which of five alert types this is, and whether the payment should be held, in one request. Code settles an alert only when the probability of the chosen type is 0.85 or more.
- **Result:** macro F1 of 0.891, against 0.474 and 0.577 for GPT-4o-mini (answering directly, reasoning first). An analyst's rules scored 0.831 and a model trained on 10,000 alerts 0.960.
- **Tested:** Jev 1.14.0, 8 November 2026, 500 simulated test alerts.
- **Built by:** [Anuar Sultan](https://www.eventum.ai/jev-builds/fraud-alert-triage)
- **Code:** [cpipi/fraud-triage-jev](https://github.com/cpipi/fraud-triage-jev) (MIT)
- **Write-up:** [the build page](https://www.eventum.ai/jev-builds/fraud-alert-triage), with the method and the limits

### Jev beside chat models in an open-source signal benchmark

An open-source server turns market data into a fixed set of options and asks models to choose. Version 2 adds Jev beside chat models. A benchmark, not trading advice.

- **What Jev judges:** which of the offered target levels to pick, and in backtests the probability that the pick reaches its success condition. Code maps the pick to a direction and applies a fixed stop rule.
- **Result:** 2.21 s per call against 12.35 s for GPT-6 Luna, with the same direction on 96.9% of repeat calls against 62.9%. Its calibration was no better (Brier score 0.263 against 0.214).
- **Tested:** Jev 1.14, server version 2 released 8 November 2026, 35 test windows of BTCUSDT one-hour candles fetched from Binance, 22 October to 7 November 2026 (UTC).
- **Built by:** Haidar Ali
- **Code:** [haidarali0/Trading-Signal-Server](https://github.com/haidarali0/Trading-Signal-Server/tree/v2) (MIT)
- **Write-up:** [the build page](https://www.eventum.ai/jev-builds/trading-signal-benchmark), with the method and the limits

### Jev decides whether an edited document still supports a stored fact

A memory system stores facts taken from documents. When a document changes, a judge checks whether the new text still supports each fact. Jev was measured alone and as a first pass in front of an LLM judge.

- **What Jev judges:** one question per case with four verdicts: supported, contradicted, unmentioned or cannot tell. Code lets the verdict stand at a confidence of 0.85 or more and sends the rest to the LLM judge.
- **Result:** Jev decided 37% of cases alone and was right on 91% of them. In front of GPT-6.1 Terra or Claude Sonnet 5.5 that cut cost by 29 to 31% with accuracy within 1 case of 172. In front of GPT-6 Luna the cost was 20% higher.
- **Tested:** Jev 1.14, 5 and 6 November 2026, 172 hand-labeled cases.
- **Built by:** [Iurii Dremov](https://www.eventum.ai/jev-builds/knowledge-drift-judge)
- **Report:** [JEV-DRIFT-CASCADE.md](knowledge-drift-judge/JEV-DRIFT-CASCADE.md), the builder's full report, with [his chart](knowledge-drift-judge/jev-threshold-sweep.png), published here with his permission
- **Code:** not published. The builder's code and test cases are private.
- **Write-up:** [the build page](https://www.eventum.ai/jev-builds/knowledge-drift-judge), with the method and the limits

## Eventum's own work with Jev

- [How Eventum cut LLM screening costs 104x with TypeSafe](https://www.eventum.ai/case-studies/how-eventum-cut-llm-screening-costs-104x-with-typesafe) (case study)
- [In-context learning at scale: what Jev changes](https://www.eventum.ai/blog/in-context-learning-at-scale-what-jev-changes) (article)

## Have a decision like one of these?

Eventum is an AI engineering consultancy that builds production AI systems. Putting decision models into production is part of that work. [Book a call](https://www.eventum.ai/book-a-call).
