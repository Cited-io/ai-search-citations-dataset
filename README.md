# AI search citations dataset: which websites AI engines list as sources

An open, monthly dataset of the websites that ChatGPT, Google AI Overviews, Google AI Mode, Gemini, Microsoft Copilot and Claude list as sources when buyers ask about AI search, generative engine optimization (GEO) and AI visibility.
The first release, 2026-10, covers 138 answers (23 prompts on 6 engines, asked from the US on 2026-10-14), with 1,001 listed URLs from 566 domains.
It is free to use under CC BY 4.0.

## Headline findings (2026-10)

1. **ChatGPT lists 6.6 times as many domains per answer as Google AI Mode.** ChatGPT listed 17.2 domains per answer on average. Claude listed 6.0, Copilot 4.7, Gemini 4.2, Google AI Overviews 3.8 and Google AI Mode 2.6.
2. **Google leans on Reddit and YouTube; Claude and Copilot never listed them.** reddit.com was listed in more answers than any other domain (21 of 138), and youtube.com came second (13). Google AI Overviews listed youtube.com in 43.5% of its answers and reddit.com in 39.1%. Claude and Copilot listed neither in any answer.
3. **Vendors' own sites show up when buyers compare tools.** On the 8 tool questions, 30 of 48 answers (62.5%) listed the website of at least one of 22 named AI visibility vendors. Copilot was the exception, at 2 of 8, partly because it read "Profound alternatives" as a question about the word.

Every number above is computed, with the SQL shown, in [the 2026-10 summary](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/data/2026-10/summary.md).
"Listed as sources" means the sources the engine returned with its answer. It is not the same as a quoted citation.

## Contents

- [Data: citations.csv (2026-10)](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/data/2026-10/citations.csv): one row per answer and listed URL, in order.
- [Data: answers.jsonl (2026-10)](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/data/2026-10/answers.jsonl): one object per answer, with its domain and URL lists and fan-out queries.
- [Data: prompts.csv (2026-10)](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/data/2026-10/prompts.csv): the 23 prompts, their ids and groups.
- [Summary (2026-10)](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/data/2026-10/summary.md): top domains overall and per engine, domains per answer, Reddit, YouTube and G2, vendor sites on tool questions.
- [Methodology](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/METHODOLOGY.md): how the answers were collected and cleaned, and the limits.
- [Schema](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/SCHEMA.md): every column and field.
- [Contributing](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/CONTRIBUTING.md): how to report a problem or suggest a prompt.
- [License (CC BY 4.0)](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/LICENSE)
- [CITATION.cff](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/CITATION.cff)

## What is in the data

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

## Release schedule

We plan a new release every month, in a new folder (`data/2026-11/`, `data/2026-12/` and so on), with the same prompts and settings so months can be compared.
Prompt ids stay fixed across releases. Any change to engines, countries or settings will be listed here.

| Release | Collected | Answers | Notes |
|---|---|---|---|
| [2026-10](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/data/2026-10/summary.md) | 2026-10-14 | 138 | First release. Perplexity unavailable. |

Mirrors on Hugging Face and Zenodo may come later. For now, this repository is the only copy.

## How to cite it

> Cited (cited.io). (2026). *AI search citations dataset* (Version 2026.10) [Data set]. https://github.com/Cited-io/ai-search-citations-dataset

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

Maintained by [Cited](https://cited.io): AI SEO and GEO agency with its own AI visibility platform. Read our guide on [how to measure AI visibility](https://cited.io/guides/how-to-measure-ai-visibility).
