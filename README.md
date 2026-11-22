# AI search citations dataset: which websites AI engines list as sources, and who they name

An open dataset of what AI engines (ChatGPT, Google AI Overviews, Google AI Mode, Gemini, Microsoft Copilot and Claude) list and cite as sources, and which businesses and products they name, when buyers ask them questions. It is updated with each study we run.
It is free to use under CC BY 4.0.

| Release | Collected | What was asked | Answers |
|---|---|---|---|
| [2026-10](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/data/2026-10/summary.md) | 2026-10-14 | 23 buyer questions about AI search and GEO, on 6 engines | 138 |
| [2026-11](https://github.com/Cited-io/ai-search-citations-dataset/tree/main/data/2026-11) | 2026-11-19 to 2026-11-21 | Who's the best local business, in 20 or 50 US cities, in 4 trades; the best B2B software, in 20 categories | 786 (300 + 346 + 140) |

## Headline findings (2026-11)

Three studies, one folder each under `data/2026-11/`. Each folder's `summary.md` prints every number below from its own files.

1. **Local businesses: personal injury lawyers, roofers and med spas, 20 cities** ([summary](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/data/2026-11/local-businesses/summary.md), [write-up on cited.io](https://cited.io/who-ai-cites/local-businesses)).
   - **Reviews:** the businesses ChatGPT, Google AI Mode or Gemini named had a median of 368, 225 and 298 Google reviews; the Google Maps top-20 listings none of them named had 68.5, 52 and 90.
   - **Agreement:** all three engines named the same business first in 0 of 12, 2 of 19 and 1 of 13 cities.
   - **Directories:** ChatGPT cited directories and ranking sites most (avvo.com for lawyers, theroofingbrief.com for roofers, nobsmedspareviews.com for med spas). Google AI Mode cited Google's own listings.
2. **Dental implants, 50 cities, five engines** ([summary](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/data/2026-11/dental-implants/summary.md), [write-up](https://cited.io/who-ai-cites/dental-implants)).
   - **Reviews:** practices any engine named had a median of 361 Google reviews; those passed over had 228.5.
   - **Agreement:** all five engines never named the same practice first (0 of 34 cities).
   - **Google Maps:** 17% of the practices ChatGPT named were in Maps' top 3 for the same search.
3. **B2B software, 20 categories, five engines** ([summary](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/data/2026-11/b2b-software/summary.md), [write-up](https://cited.io/who-ai-cites/b2b-software)).
   - **Agreement:** all five engines named the same product first in 5 of 13 categories.
   - **Sources:** every engine cited third-party comparisons and lists most (57% of ChatGPT's cited pages, 87% of Claude's). Vendors' own sites came next on ChatGPT (39%). Google AI Mode added youtube.com (11%) and reddit.com (7%).

A fourth study, eight more local trades in the same 20 cities, joins this release with its write-up on cited.io on 15 December 2026.

"Cited" means the engine quoted the page in its answer; "listed" means the page only appeared among its sources. Answers were asked through the engines' APIs with web search on, not in their apps; see [METHODOLOGY.md](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/METHODOLOGY.md).

## Headline findings (2026-10)

1. **ChatGPT lists 6.6 times as many domains per answer as Google AI Mode.** ChatGPT listed 17.2 domains per answer on average. Claude listed 6.0, Copilot 4.7, Gemini 4.2, Google AI Overviews 3.8 and Google AI Mode 2.6.
2. **Google leans on Reddit and YouTube; Claude and Copilot never listed them.** reddit.com was listed in more answers than any other domain (21 of 138), and youtube.com came second (13). Google AI Overviews listed youtube.com in 43.5% of its answers and reddit.com in 39.1%. Claude and Copilot listed neither in any answer.
3. **Vendors' own sites show up when buyers compare tools.** On the 8 tool questions, 30 of 48 answers (62.5%) listed the website of at least one of 22 named AI visibility vendors. Copilot was the exception, at 2 of 8, partly because it read "Profound alternatives" as a question about the word.

Every 2026-10 number above is computed, with the SQL shown, in [the 2026-10 summary](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/data/2026-10/summary.md).
"Listed as sources" means the sources the engine returned with its answer. It is not the same as a quoted citation.

## Contents

- **2026-10:** [citations.csv](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/data/2026-10/citations.csv), [answers.jsonl](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/data/2026-10/answers.jsonl), [prompts.csv](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/data/2026-10/prompts.csv) and its [summary](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/data/2026-10/summary.md).
- **2026-11**, one folder per study, each with its `summary.md`:
  - `local-businesses/`: `by_city_{law,roofing,medspa}.csv`, `top_domains.csv`, `figures.json`.
  - `dental-implants/`: `practices_by_city.csv`, `top_domains.csv`, `figures.json`.
  - `b2b-software/`: `b2b_by_category.csv`, `top_domains.csv`, `figures.json`.
- [Methodology](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/METHODOLOGY.md): how each release was collected and cleaned, and the limits.
- [Schema](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/SCHEMA.md): every column and field.
- [Contributing](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/CONTRIBUTING.md), [License (CC BY 4.0)](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/LICENSE), [CITATION.cff](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/CITATION.cff).

## What is in the 2026-10 release

| | 2026-10 |
|---|---|
| Collected | 2026-10-14, from the US |
| Engines | ChatGPT, Google AI Overviews, Google AI Mode, Gemini, Microsoft Copilot, Claude |
| Prompts | 23: 8 about tools, 6 about agencies, 9 how-to questions |
| Answers | 138 (each prompt asked once per engine) |
| Listed URLs | 1,001 rows; 830 distinct URLs; 566 distinct domains |
| Answer text | Not included |

Example prompts: "best AI visibility tools", "Profound vs Peec AI", "best GEO agency", "how to get cited in Google AI Overviews", "does llms.txt help AI visibility".

Perplexity is not in this release, because it could not be reached on the collection day. See [METHODOLOGY.md](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/METHODOLOGY.md).

## How to load it

### pandas

```python
import pandas as pd

BASE = "https://raw.githubusercontent.com/Cited-io/ai-search-citations-dataset/main/data/2026-10/"

citations = pd.read_csv(BASE + "citations.csv")
answers = pd.read_json(BASE + "answers.jsonl", lines=True)

# Domains listed per answer, by engine (answers with no sources count as 0)
answers["n_domains"] = answers["domains"].str.len()
print(answers.groupby("engine")["n_domains"].mean().round(1).sort_values(ascending=False))

# Domains listed in the most answers
top = (citations.drop_duplicates(["engine", "prompt_id", "domain"])
       .groupby("domain").size().sort_values(ascending=False).head(10))
print(top)
```

### DuckDB

DuckDB reads the files straight from GitHub.

```sql
-- Domains listed in the most answers
SELECT domain, count(DISTINCT engine || '/' || prompt_id) AS answers
FROM read_csv('https://raw.githubusercontent.com/Cited-io/ai-search-citations-dataset/main/data/2026-10/citations.csv')
GROUP BY domain
ORDER BY answers DESC, domain
LIMIT 10;

-- Domains listed per answer, by engine
SELECT engine, round(avg(len(domains)), 1) AS domains_per_answer
FROM read_json('https://raw.githubusercontent.com/Cited-io/ai-search-citations-dataset/main/data/2026-10/answers.jsonl')
GROUP BY engine
ORDER BY domains_per_answer DESC;
```

Count answers, not rows. An engine can list several pages from one domain in a single answer, and `citations.csv` has a row for each.

## Releases

A new release comes with each study we run, in a new folder under `data/`. The 2026-10 questions were planned as a monthly re-run; November brought new studies instead, so releases now follow the studies rather than the calendar. Prompt ids in the 2026-10 release stay fixed if it is re-run. Every change of engines, countries or settings is listed in the release's notes and in METHODOLOGY.md.

Mirrors on Hugging Face and Zenodo may come later. For now, this repository is the only copy.

## How to cite it

> Cited (cited.io). (2026). *AI search citations dataset* (Version 2026.11) [Data set]. https://github.com/Cited-io/ai-search-citations-dataset

GitHub's "Cite this repository" button gives the same reference from [CITATION.cff](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/CITATION.cff).

## License

The data and docs are licensed under [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/LICENSE).
You can share and adapt them for any purpose, including commercial use. You must give appropriate credit ("Cited (cited.io), AI search citations dataset", with a link to this repository), provide a link to the license, and indicate if you made changes.

The URLs in the data point to third-party pages. This license covers our collection of them, not the pages themselves.

## Related reading

- [How to measure AI visibility](https://cited.io/guides/how-to-measure-ai-visibility): what numbers like these can and cannot tell you about your own brand.
- [How to get cited by ChatGPT](https://cited.io/guides/how-to-get-cited-by-chatgpt)
- [How to rank in Google AI Overviews](https://cited.io/guides/how-to-rank-in-google-ai-overviews)
- [GEO: Generative Engine Optimization](https://arxiv.org/abs/2311.09735) (Aggarwal et al., KDD 2024): the research paper that introduced the term.

---

Maintained by [Cited](https://cited.io), an AI search and SEO agency. Read our guide on [how to measure AI visibility](https://cited.io/guides/how-to-measure-ai-visibility).
