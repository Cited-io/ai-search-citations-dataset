# AI search citations, October 2026: summary

In October 2026, six AI engines listed 566 distinct domains as sources across 138 answers to 23 buyer questions about AI search and generative engine optimization.
ChatGPT listed far more sources than any other engine: 17.2 domains per answer, against 2.6 for Google AI Mode.
Google AI Overviews and Google AI Mode leaned on reddit.com and youtube.com, while Claude and Copilot listed neither in any answer.

Every number below comes from the SQL printed above its table. Run it in DuckDB from this folder (`data/2026-10/`), and you get the same table. It was tested with DuckDB 1.5.

## Contents

- [Setup](#setup)
- [Scope](#scope)
- [Top domains overall](#top-domains-overall)
- [Top domains per engine](#top-domains-per-engine)
- [Domains listed per answer, by engine](#domains-listed-per-answer-by-engine)
- [Answers listing reddit.com, youtube.com and g2.com](#answers-listing-redditcom-youtubecom-and-g2com)
- [Vendor sites on tool questions](#vendor-sites-on-tool-questions)
- [Things to keep in mind](#things-to-keep-in-mind)

## Setup

Run this once in a DuckDB session. It defines the two files as views and the vendor list used in the last section.

```sql
CREATE VIEW citations AS SELECT * FROM read_csv('citations.csv');
CREATE VIEW answers AS SELECT * FROM read_json('answers.jsonl');
CREATE TABLE vendors (vendor VARCHAR, kind VARCHAR, site VARCHAR);
INSERT INTO vendors VALUES
  ('Profound',     'AI visibility platform', 'tryprofound.com'),
  ('Peec AI',      'AI visibility platform', 'peec.ai'),
  ('Otterly.AI',   'AI visibility platform', 'otterly.ai'),
  ('Rankscale',    'AI visibility platform', 'rankscale.ai'),
  ('Analyze AI',   'AI visibility platform', 'tryanalyze.ai'),
  ('Geoptie',      'AI visibility platform', 'geoptie.com'),
  ('Dageno',       'AI visibility platform', 'dageno.ai'),
  ('LLM Pulse',    'AI visibility platform', 'llmpulse.ai'),
  ('Searchable',   'AI visibility platform', 'searchable.com'),
  ('Elmo',         'AI visibility platform', 'elmohq.com'),
  ('Evertune',     'AI visibility platform', 'evertune.ai'),
  ('Promptwatch',  'AI visibility platform', 'promptwatch.com'),
  ('ZipTie',       'AI visibility platform', 'ziptie.ai'),
  ('Semrush',      'SEO or content suite',   'semrush.com'),
  ('Ahrefs',       'SEO or content suite',   'ahrefs.com'),
  ('SE Ranking',   'SEO or content suite',   'seranking.com'),
  ('Conductor',    'SEO or content suite',   'conductor.com'),
  ('BrightEdge',   'SEO or content suite',   'brightedge.com'),
  ('Similarweb',   'SEO or content suite',   'similarweb.com'),
  ('Surfer',       'SEO or content suite',   'surferseo.com'),
  ('Writesonic',   'SEO or content suite',   'writesonic.com'),
  ('AirOps',       'SEO or content suite',   'airops.com');
```

Engine ids: `chatgpt` (ChatGPT), `google_ai_overviews` (Google AI Overviews), `google_ai_mode` (Google AI Mode), `gemini` (Gemini), `copilot` (Microsoft Copilot), `claude` (Claude). See [SCHEMA.md](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/SCHEMA.md).

## Scope

This release has 138 answers: 23 prompts, each asked once on each of 6 engines.
Five answers listed no sources at all. They stay in the denominators below.

```sql
SELECT
  (SELECT count(*) FROM answers)                     AS answers,
  (SELECT count(DISTINCT prompt_id) FROM answers)    AS prompts,
  (SELECT count(DISTINCT engine) FROM answers)       AS engines,
  count(*)                                           AS citation_rows,
  count(DISTINCT url)                                AS distinct_urls,
  count(DISTINCT domain)                             AS distinct_domains,
  (SELECT count(*) FROM answers WHERE len(urls) = 0) AS answers_with_no_sources
FROM citations;
```

| answers | prompts | engines | citation_rows | distinct_urls | distinct_domains | answers_with_no_sources |
|---|---|---|---|---|---|---|
| 138 | 23 | 6 | 1001 | 830 | 566 | 5 |

## Top domains overall

reddit.com was listed in more answers than any other domain: 21 of 138 (15.2%).
semrush.com was the only domain that all six engines listed at least once.
A domain counts once per answer, however many of its pages the answer listed. Ties are broken alphabetically.

```sql
SELECT
  domain,
  count(DISTINCT engine || '/' || prompt_id)                                         AS answers,
  round(100.0 * count(DISTINCT engine || '/' || prompt_id) / (SELECT count(*) FROM answers), 1) AS pct_of_answers,
  count(DISTINCT engine)                                                             AS engines
FROM citations
GROUP BY domain
ORDER BY answers DESC, domain
LIMIT 20;
```

| domain | answers | pct_of_answers | engines |
|---|---|---|---|
| reddit.com | 21 | 15.2 | 4 |
| youtube.com | 13 | 9.4 | 2 |
| semrush.com | 12 | 8.7 | 6 |
| developers.google.com | 8 | 5.8 | 2 |
| geoptie.com | 7 | 5.1 | 4 |
| otterly.ai | 7 | 5.1 | 4 |
| tryprofound.com | 7 | 5.1 | 5 |
| yesoptimist.com | 7 | 5.1 | 4 |
| airops.com | 6 | 4.3 | 3 |
| contentful.com | 6 | 4.3 | 4 |
| dageno.ai | 6 | 4.3 | 3 |
| llmpulse.ai | 6 | 4.3 | 4 |
| peec.ai | 6 | 4.3 | 4 |
| position.digital | 6 | 4.3 | 4 |
| thriveagency.com | 6 | 4.3 | 4 |
| writesonic.com | 6 | 4.3 | 2 |
| authoritytech.io | 5 | 3.6 | 4 |
| blog.google | 5 | 3.6 | 1 |
| ccn.web.tr | 5 | 3.6 | 1 |
| github.com | 5 | 3.6 | 1 |

## Top domains per engine

Each cell is a domain and the number of that engine's 23 answers that listed it.
Only domains listed in at least 2 answers are shown, up to 10 per engine. Ties are broken alphabetically, so a domain with the same count as the 10th row can be cut.

```sql
SELECT engine, domain, count(DISTINCT prompt_id) AS answers
FROM citations
GROUP BY engine, domain
HAVING count(DISTINCT prompt_id) >= 2
QUALIFY row_number() OVER (PARTITION BY engine ORDER BY answers DESC, domain) <= 10
ORDER BY engine, answers DESC, domain;
```

| # | chatgpt | google_ai_overviews | google_ai_mode | gemini | copilot | claude |
|---|---|---|---|---|---|---|
| 1 | developers.google.com (7) | youtube.com (10) | reddit.com (7) | reddit.com (4) | aeolabs.ai (2) | airops.com (4) |
| 2 | blog.google (5) | reddit.com (9) | youtube.com (3) | geoptie.com (3) | atomikdigital.com (2) | dageno.ai (4) |
| 3 | ccn.web.tr (5) | contentful.com (2) | contentful.com (2) | writesonic.com (3) | gtm8020.com (2) | position.digital (3) |
| 4 | github.com (5) | linkedin.com (2) | otterly.ai (2) | broadcastwell.com (2) | llmpulse.ai (2) | semrush.com (3) |
| 5 | techradar.com (5) | nathanojaokomo.com (2) | peec.ai (2) | growbydata.com (2) | superframeworks.com (2) | seoprofy.com (3) |
| 6 | support.google.com (4) | semrush.com (2) | semrush.com (2) | semrush.com (2) |  | writesonic.com (3) |
| 7 | ahrefs.com (3) | wpengine.com (2) | thriveagency.com (2) | workduo.ai (2) |  | authoritytech.io (2) |
| 8 | developers.google.cn (3) | yesoptimist.com (2) | tryprofound.com (2) | yesoptimist.com (2) |  | gen-optima.com (2) |
| 9 | itechguides.com (3) |  |  |  |  | geoptie.com (2) |
| 10 | loudface.co (3) |  |  |  |  | llmpulse.ai (2) |

ChatGPT's list leans on Google's own documentation (developers.google.com, blog.google, support.google.com).
It also listed copies of that documentation on other hosts: `ccn.web.tr` is a proxy page wrapping Google's docs, and `developers.google.cn` is Google's China mirror. The data keeps them as returned.

## Domains listed per answer, by engine

ChatGPT listed 17.2 domains per answer on average. Google AI Mode listed 2.6.
"Domains" counts distinct domains in one answer. "URLs" counts distinct pages.
Averages divide by all 23 answers per engine, including answers with no sources.

```sql
SELECT
  coalesce(engine, 'all engines')                 AS engine,
  count(*)                                        AS answers,
  round(avg(len(domains)), 1)                     AS domains_per_answer,
  median(len(domains))                            AS median_domains,
  max(len(domains))                               AS max_domains,
  round(avg(len(urls)), 1)                        AS urls_per_answer,
  count(*) FILTER (WHERE len(urls) = 0)           AS answers_with_no_sources
FROM answers
GROUP BY GROUPING SETS ((engine), ())
ORDER BY engine IS NULL, domains_per_answer DESC;
```

| engine | answers | domains_per_answer | median_domains | max_domains | urls_per_answer | answers_with_no_sources |
|---|---|---|---|---|---|---|
| chatgpt | 23 | 17.2 | 16.0 | 43 | 21.6 | 0 |
| claude | 23 | 6.0 | 6.0 | 9 | 6.1 | 1 |
| copilot | 23 | 4.7 | 5.0 | 8 | 4.9 | 1 |
| gemini | 23 | 4.2 | 5.0 | 6 | 4.2 | 1 |
| google_ai_overviews | 23 | 3.8 | 3.0 | 15 | 4.0 | 0 |
| google_ai_mode | 23 | 2.6 | 3.0 | 4 | 2.7 | 2 |
| all engines | 138 | 6.4 | 4.0 | 43 | 7.3 | 5 |

## Answers listing reddit.com, youtube.com and g2.com

An answer counts if it listed at least one page on the domain or one of its subdomains.
Google AI Overviews listed youtube.com in 10 of 23 answers (43.5%) and reddit.com in 9 (39.1%).
Claude and Copilot listed none of the three sites in any answer.
g2.com, a software review site, appeared in only 3 answers, although 8 of the 23 prompts ask about software.

```sql
WITH flags AS (
  SELECT engine,
    len(list_filter(domains, lambda d: d = 'reddit.com'  OR d LIKE '%.reddit.com'))  > 0 AS reddit,
    len(list_filter(domains, lambda d: d = 'youtube.com' OR d LIKE '%.youtube.com')) > 0 AS youtube,
    len(list_filter(domains, lambda d: d = 'g2.com'      OR d LIKE '%.g2.com'))      > 0 AS g2
  FROM answers
)
SELECT
  coalesce(engine, 'all engines')                                       AS engine,
  count(*)                                                              AS answers,
  count(*) FILTER (WHERE reddit)                                        AS reddit,
  round(100.0 * count(*) FILTER (WHERE reddit) / count(*), 1)           AS reddit_pct,
  count(*) FILTER (WHERE youtube)                                       AS youtube,
  round(100.0 * count(*) FILTER (WHERE youtube) / count(*), 1)          AS youtube_pct,
  count(*) FILTER (WHERE g2)                                            AS g2,
  round(100.0 * count(*) FILTER (WHERE g2) / count(*), 1)               AS g2_pct
FROM flags
GROUP BY GROUPING SETS ((engine), ())
ORDER BY engine IS NULL, reddit DESC, engine;
```

| engine | answers | reddit | reddit_pct | youtube | youtube_pct | g2 | g2_pct |
|---|---|---|---|---|---|---|---|
| google_ai_overviews | 23 | 9 | 39.1 | 10 | 43.5 | 0 | 0.0 |
| google_ai_mode | 23 | 7 | 30.4 | 3 | 13.0 | 1 | 4.3 |
| gemini | 23 | 4 | 17.4 | 0 | 0.0 | 0 | 0.0 |
| chatgpt | 23 | 1 | 4.3 | 0 | 0.0 | 2 | 8.7 |
| claude | 23 | 0 | 0.0 | 0 | 0.0 | 0 | 0.0 |
| copilot | 23 | 0 | 0.0 | 0 | 0.0 | 0 | 0.0 |
| all engines | 138 | 21 | 15.2 | 13 | 9.4 | 3 | 2.2 |

## Vendor sites on tool questions

On the 8 tool questions (`prompt_group = 'tools'`), 30 of 48 answers (62.5%) listed at least one site from the vendor list below.
Those vendor sites made up 71 of the 323 domains listed (22.0%).
Copilot was the exception: 2 of its 8 tool answers listed a vendor site.
Part of the reason is that Copilot read "Profound alternatives" as a question about the word "profound" and listed dictionary and thesaurus sites.
With 8 tool answers per engine, one answer moves an engine's percentage by 12.5 points.

**The vendor list.** It is the 22 rows of the `vendors` table in [Setup](#setup), and nothing else. A site is on it if it is the company's own website, and the company sells software that tracks how brands appear in AI answers. There are two kinds:

- **AI visibility platforms:** companies whose main product is that tracking.
- **SEO or content suites:** broader products that sell AI visibility tracking as one feature.

A vendor's subdomains count as the vendor, for example `help.otterly.ai`, `docs.peec.ai`, `help.tryprofound.com` and `visible.seranking.com`.

Each vendor's product page, checked on 2026-10-15:

| Vendor | Product page |
|---|---|
| Profound | https://www.tryprofound.com/ |
| Peec AI | https://peec.ai/ |
| Otterly.AI | https://otterly.ai/ |
| Rankscale | https://rankscale.ai/ |
| Analyze AI | https://www.tryanalyze.ai/ |
| Geoptie | https://geoptie.com/ |
| Dageno | https://dageno.ai/ |
| LLM Pulse | https://llmpulse.ai/ |
| Searchable | https://searchable.com/ |
| Elmo | https://www.elmohq.com/ |
| Evertune | https://evertune.ai/ |
| Promptwatch | https://www.promptwatch.com/ |
| ZipTie | https://ziptie.ai/ |
| Semrush (AI Visibility Toolkit) | https://www.semrush.com/kb/1493-ai-toolkit |
| Ahrefs (Brand Radar) | https://ahrefs.com/brand-radar |
| SE Ranking (SE Visible) | https://visible.seranking.com/ |
| Conductor | https://www.conductor.com/ |
| BrightEdge (AI Catalyst) | https://www.brightedge.com/ai-catalyst |
| Similarweb (AI Search Intelligence) | https://aisearch.similarweb.com/ |
| Surfer (AI Tracker) | https://surferseo.com/ai-tracker/ |
| Writesonic (AI Visibility Tracker) | https://writesonic.com/ai-visibility-tracker |
| AirOps (Insights) | https://www.airops.com/insights |

The list is a lower bound. Many smaller sites in the data also sell this kind of software and are not on it. Look-alike domains are not folded into any vendor.

Disclosure: Cited (cited.io), which publishes this dataset, also sells in this category. cited.io was not listed in any answer in this release, so leaving it off the list changes no number.

```sql
WITH tool_answers AS (
  SELECT engine, prompt_id, domains FROM answers WHERE prompt_group = 'tools'
),
slots AS (
  SELECT engine, prompt_id, unnest(domains) AS domain FROM tool_answers
),
per_answer AS (
  SELECT t.engine, t.prompt_id,
         len(any_value(t.domains)) AS listed,
         count(v.site)             AS vendor_domains
  FROM tool_answers t
  LEFT JOIN slots s   ON s.engine = t.engine AND s.prompt_id = t.prompt_id
  LEFT JOIN vendors v ON s.domain = v.site OR s.domain LIKE '%.' || v.site
  GROUP BY t.engine, t.prompt_id
)
SELECT
  coalesce(engine, 'all engines')                                          AS engine,
  count(*)                                                                 AS tool_answers,
  count(*) FILTER (WHERE vendor_domains > 0)                               AS answers_listing_a_vendor,
  round(100.0 * count(*) FILTER (WHERE vendor_domains > 0) / count(*), 1)  AS answers_pct,
  sum(listed)                                                              AS domains_listed,
  sum(vendor_domains)                                                      AS vendor_domains,
  round(100.0 * sum(vendor_domains) / nullif(sum(listed), 0), 1)           AS vendor_domain_pct
FROM per_answer
GROUP BY GROUPING SETS ((engine), ())
ORDER BY engine IS NULL, answers_listing_a_vendor DESC, engine;
```

| engine | tool_answers | answers_listing_a_vendor | answers_pct | domains_listed | vendor_domains | vendor_domain_pct |
|---|---|---|---|---|---|---|
| chatgpt | 8 | 7 | 87.5 | 143 | 16 | 11.2 |
| claude | 8 | 6 | 75.0 | 55 | 21 | 38.2 |
| gemini | 8 | 5 | 62.5 | 34 | 11 | 32.4 |
| google_ai_mode | 8 | 5 | 62.5 | 21 | 10 | 47.6 |
| google_ai_overviews | 8 | 5 | 62.5 | 34 | 11 | 32.4 |
| copilot | 8 | 2 | 25.0 | 36 | 2 | 5.6 |
| all engines | 48 | 30 | 62.5 | 323 | 71 | 22.0 |

ChatGPT listed a vendor site in the most tool answers, but vendor sites were only 11.2% of its long source lists.
Google AI Mode's lists were short, and nearly half of their domains (47.6%) were vendor sites.

Which vendors were listed, in how many of the 48 tool answers, and on how many engines:

```sql
WITH slots AS (
  SELECT engine, prompt_id, unnest(domains) AS domain FROM answers WHERE prompt_group = 'tools'
)
SELECT
  v.vendor, v.kind, v.site,
  count(DISTINCT s.engine || '/' || s.prompt_id) AS tool_answers,
  count(DISTINCT s.engine)                       AS engines
FROM vendors v
LEFT JOIN slots s ON s.domain = v.site OR s.domain LIKE '%.' || v.site
GROUP BY ALL
ORDER BY tool_answers DESC, v.vendor;
```

| vendor | kind | site | tool_answers | engines |
|---|---|---|---|---|
| Geoptie | AI visibility platform | geoptie.com | 7 | 4 |
| Profound | AI visibility platform | tryprofound.com | 7 | 5 |
| Peec AI | AI visibility platform | peec.ai | 6 | 4 |
| Semrush | SEO or content suite | semrush.com | 6 | 4 |
| Analyze AI | AI visibility platform | tryanalyze.ai | 5 | 5 |
| Dageno | AI visibility platform | dageno.ai | 5 | 2 |
| Otterly.AI | AI visibility platform | otterly.ai | 5 | 3 |
| Writesonic | SEO or content suite | writesonic.com | 5 | 2 |
| AirOps | SEO or content suite | airops.com | 4 | 3 |
| Rankscale | AI visibility platform | rankscale.ai | 4 | 4 |
| SE Ranking | SEO or content suite | seranking.com | 4 | 2 |
| LLM Pulse | AI visibility platform | llmpulse.ai | 2 | 2 |
| Conductor | SEO or content suite | conductor.com | 1 | 1 |
| Elmo | AI visibility platform | elmohq.com | 1 | 1 |
| Evertune | AI visibility platform | evertune.ai | 1 | 1 |
| Promptwatch | AI visibility platform | promptwatch.com | 1 | 1 |
| Searchable | AI visibility platform | searchable.com | 1 | 1 |
| Surfer | SEO or content suite | surferseo.com | 1 | 1 |
| ZipTie | AI visibility platform | ziptie.ai | 1 | 1 |
| Ahrefs | SEO or content suite | ahrefs.com | 0 | 0 |
| BrightEdge | SEO or content suite | brightedge.com | 0 | 0 |
| Similarweb | SEO or content suite | similarweb.com | 0 | 0 |

## Things to keep in mind

- **One run.** Each prompt was asked once per engine. The same question asked again can return different sources.
- **Small sample.** There are 23 prompts and 23 answers per engine. A difference of one or two answers moves a percentage by 4 to 9 points. On the 8 tool answers per engine, one answer moves it by 12.5 points.
- **Listed, not quoted.** These are the sources each engine returned with its answer. An engine can list a page without quoting it.
- **One country.** All answers were collected from the US.

Full method and limits: [METHODOLOGY.md](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/METHODOLOGY.md).
