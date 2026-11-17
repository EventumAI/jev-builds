<!-- Draft. The build and profile links point to hidden preview pages on eventum.ai. Swap them for the public addresses before this repository is made public. Each entry waits for its builder's approval. -->

# Jev builds

Working builds on Jev, the decision model made by [TypeSafe](https://typesafe.ai), by engineers who build with [Eventum](https://www.eventum.ai). A decision model answers a narrow question with a typed judgment and a probability, and writes no text. Each build here puts Jev on one real decision and measures it against another way of doing the same job.

Eventum is an official technology partner of TypeSafe and has run Jev in its own systems since before its public launch.

This repository is an index. Where a build's code is published, it lives in the builder's own repository, under the builder's name and license.

## The builds

Newest first. Every figure is the builder's own measurement.

### Jev walks a PDF's table of contents to find the answer

A search that treats a long PDF's own table of contents as a tree. Jev ranks the sections, checks the pages in the most promising one, and stops when it judges that nothing the question needs is missing.

- **What Jev decides:** which section to open next, whether a page states evidence the question needs, and whether anything is still missing.
- **Result:** returned pages that support the correct answer in 59 of 60 runs, at about 13x lower cost and 2.6x the speed of OpenAI's gpt-6.1-sol walking the same contents. Jev checking every page and the OpenAI model both scored 60 of 60.
- **Tested:** Jev 1.13.0, 10 November 2026, 20 questions over 4 PDFs, 3 runs each.
- **Built by:** [Gassyr Bakubay](https://www.eventum.ai/preview/engineers/gassyr-bakubay)
- **Code:** [bakubay/jev-dfs-document-search](https://github.com/bakubay/jev-dfs-document-search) (Apache 2.0)
- **Write-up:** [the build page](https://www.eventum.ai/preview/jev-builds/pdf-table-of-contents-search), with the comparison, the limits and a 28-second video

### Labeling remote jobs: Jev against Claude Haiku 5.5

RVC, a job board for remote work, labels each new posting as worldwide remote, remote in one country, hybrid or on-site. Jev was tested on that label against Claude Haiku 5.5.

- **What Jev decides:** which work arrangement a job posting describes, in one question per posting.
- **Result:** 97.1% accuracy against 94.5% for Claude Haiku 5.5 with reasoning, which the builder calls a tie, with Jev about 50x faster and about 6x cheaper per posting.
- **Tested:** Jev 1.14, October 2026, 200 postings from RVC.
- **Built by:** [Dmitry Ivanov](https://www.eventum.ai/preview/engineers/dmitry-ivanov)
- **Code:** not published. The builder's prompts and question sets are private.
- **Write-up:** [the build page](https://www.eventum.ai/preview/jev-builds/remote-job-labels), with the method and the limits

## Eventum's own work with Jev

- [How Eventum cut LLM screening costs 104x with TypeSafe](https://www.eventum.ai/case-studies/how-eventum-cut-llm-screening-costs-104x-with-typesafe) (case study)
- [In-context learning at scale: what Jev changes](https://www.eventum.ai/blog/in-context-learning-at-scale-what-jev-changes) (article)

## Have a decision like one of these?

Eventum is an AI engineering consultancy that builds production AI systems. Decision models like Jev are part of that work. [Book a call](https://www.eventum.ai/book-a-call).
