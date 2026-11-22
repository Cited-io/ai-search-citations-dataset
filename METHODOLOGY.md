# Methodology: how the AI search citations dataset is collected

Each release asks AI engines a set of buyer questions and records what each answer listed and cited as sources, and, from 2026-11, which businesses or products it named.
The October 2026 release (2026-10-14) asked 23 questions about AI search on 6 engines, 138 answers. The November 2026 release (2026-11-19 to 2026-11-21) is four studies of who AI names for local businesses and B2B software; it is described [at the end](#the-2026-11-release).
Every release is a snapshot of one or two runs, not an average. Read the limits before drawing conclusions.

## Contents

- [What was asked](#what-was-asked)
- [How the answers were collected](#how-the-answers-were-collected)
- [What "listed as sources" means](#what-listed-as-sources-means)
- [How the raw sources were cleaned](#how-the-raw-sources-were-cleaned)
- [What is left out](#what-is-left-out)
- [Limits](#limits)
- [Releases](#releases)
- [The 2026-11 release](#the-2026-11-release)

## What was asked

The prompts are short questions a buyer of AI visibility software or services would type. They fall into three groups:

| Group | Prompts | Example |
|---|---|---|
| `tools` | 8 | "best AI visibility tools" |
| `agency` | 6 | "best GEO agency" |
| `how-to` | 9 | "how to get cited in Google AI Overviews" |

The full list is in [`data/2026-10/prompts.csv`](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/data/2026-10/prompts.csv).

Every prompt was asked in English, exactly as written, with no follow-up question.

## How the answers were collected

- **When:** 2026-10-14, between about 16:55 and 17:03 UTC.
- **Where:** the country was set to the United States on every engine.
- **How often:** each prompt was asked once on each engine.
- **ChatGPT, Google AI Overviews, Google AI Mode, Gemini and Microsoft Copilot:** through a commercial AI search monitoring API, which asks each engine the question and returns the answer with its sources. Google AI Overviews was requested as a desktop Google search.
- **Claude:** through Anthropic's Messages API (model `claude-sonnet-5-5`) with Anthropic's web search tool turned on and the user location set to the US. See Anthropic's [web search tool documentation](https://platform.claude.com/docs/en/agents-and-tools/tool-use/web-search-tool).
- **Perplexity** is not in this release. The monitoring API could not reach it on the day.

## What "listed as sources" means

A source is a URL the engine returned alongside its answer: the links in ChatGPT's sources list, the links in a Google AI Overview, or the URLs Claude's web search citations point to.

That is not the same as a quoted citation. An engine can list a page it barely used, and it can use a page's content without listing it.
This dataset records only what each engine returned as its sources, in the order it returned them.

## How the raw sources were cleaned

We changed as little as possible:

1. **ChatGPT's referral tag removed.** ChatGPT appends `utm_source=chatgpt.com` to many links. We removed that one parameter. Every other part of each URL is kept exactly as returned, including other tracking parameters.
2. **Exact duplicates removed.** ChatGPT often lists the same page twice, once with the tag and once without. After step 1, a URL that repeats within one answer is kept only at its first position. `rank` is the position in that deduplicated list, starting at 1.
3. **Domain derived from the URL.** `domain` is the URL's hostname, lowercased, with a leading `www.` removed. Other subdomains are kept as they are, so `developers.google.com` and `blog.google` are different domains from `google.com`.

Nothing else was normalised. Proxy pages and mirrors (for example a proxy host wrapping Google's documentation) stay in the data as the engine returned them.

Five answers listed no sources at all. They appear in `answers.jsonl` with empty lists and have no rows in `citations.csv`.

**Fan-out queries.** Some engines show the web searches they ran to build the answer. We recorded them for ChatGPT, Claude and Copilot. For the other three engines the field is `null`, meaning not collected. An empty list means the engine was checked and showed none for that answer.

## What is left out

- **Answer text.** We publish which sources each engine listed, not what it said.
- **Two self-referential prompts.** The original run had 25 prompts. Two asked about Cited, the company that publishes this dataset. We dropped them, so every prompt here is about the market, not about us.
- **Request settings beyond those above,** and any analysis of which brands each answer named.

## Limits

- **One run.** Each prompt was asked once per engine. AI answers change from run to run, even minutes apart. Treat any single row as one sample, not a fixed ranking.
- **One country, one language.** Every answer was collected from the US, in English. Other countries see different answers and sources.
- **Small sample.** There are 23 prompts and 23 answers per engine. One or two answers move a per-engine percentage by 4 to 9 points. Within one prompt group the effect is larger: on the 8 tool answers per engine, one answer moves it by 12.5 points.
- **One topic.** Every prompt is about AI search, GEO and AI visibility. The sources engines pick for other topics will differ.
- **Engine versions are not fixed.** The engines change their models and retrieval without notice. A difference between two months can come from the engine, not the web.
- **Some prompts were misread.** These are real engine behaviours, kept in the data:
  - ChatGPT and Copilot answered "Profound alternatives" with synonyms of the word "profound", so dictionary and thesaurus sites appear among the tool sources.
  - Copilot read "best GEO agency" as geospatial intelligence.
  - On "GEO agency for local businesses", engines localised the answer to a US city.
- **No user history.** Answers came from fresh sessions with no account history or memory. People who are signed in may see different answers.

## Releases

Each study we run is published as a new folder under `data/`. The 2026-10 questions were planned as a monthly re-run; November brought new studies instead, so releases now follow the studies rather than the calendar.

- If the 2026-10 questions are asked again, their prompt ids stay fixed: `p01` will always mean "best AI visibility tools". New prompts get new ids, and retired ids are never reused.
- If a setting changes (an engine added, a country added, a different model), the release notes in the [README](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/README.md) will say so.

## The 2026-11 release

Three studies, one folder each under `data/2026-11/`. Every question named a place or a category and asked for the best.

| Study | Question | Where | Engines and runs | Asked |
|---|---|---|---|---|
| `local-businesses` | "Who's the best {personal injury lawyer, roofing company, med spa} in {city}, {state}?" | 20 US cities | ChatGPT and Google AI Mode twice, Gemini once | 2026-11-19 |
| `dental-implants` | "Who's the best dentist for dental implants in {city}, {state}?" | 50 US cities | ChatGPT and Google AI Mode twice, Gemini once (2026-11-19); Claude and Google AI Overviews once (2026-11-21) | 2026-11-19, 2026-11-21 |
| `b2b-software` | "What's the best {category} for a mid-size B2B company?" | the US, 20 software categories | as dental implants | 2026-11-19, 2026-11-21 |

**How the engines were asked.**
- **ChatGPT, Google AI Mode and Gemini:** through a commercial AI search monitoring service that asks each engine logged out and returns the answer with its sources. ChatGPT searched the web for every answer. ChatGPT and Gemini were asked from the city's state, Google AI Mode from the city itself, and B2B questions from the US.
- **Claude:** through Anthropic's Messages API (`claude-sonnet-5-5`) with web search on (up to 5 searches), located in the city or the US.
- **Google AI Overviews:** the question searched on Google from the city (or the US), desktop, first page.
- **What that means:** these are answers as a tracker gets them, not as a person sees them in the apps. The ChatGPT and Claude apps decide for themselves when to search, so a person asking can see a different list.

**Google Maps.** For each local question we read Google Maps' top 20 for the trade's own search in the same city ("personal injury lawyer", "dental implants", "plumber" and so on), with each listing's rating and review count, through a commercial search data service.
- Maps was read in 20 of 20 cities for each local study, and in 49 of 50 cities for dental implants.

**Named, cited and listed.**
- **Named:** the businesses or products an answer recommends, read from its text by fixed rules: names in bold, a list line's leading name, a place card's name, a table's first column, with headings and directories skipped. Each engine's first pick was checked by eye.
- **Cited:** a page the engine quoted inline in its answer.
- **Listed:** a page that only appeared among its sources.
- **Matching to Maps:** a named business is matched to a Maps listing when their names, with common words such as "law", "group" or "dental" removed, are the same or one contains the other, or when they are at least 85% alike.

**Cleaning.** As in 2026-10: ChatGPT's `utm_source=chatgpt.com` tag removed from URLs, nothing else changed, exact duplicates within one answer dropped.

**What is left out of 2026-11.**
- **Answer text,** as in 2026-10.
- **Google Maps' listings themselves** (names, ratings and review counts), which are Google's to publish. The studies' review medians are published as computed (`figures.json`).
- **A fourth study, eight more local trades,** which joins on 15 December 2026 with its write-up on cited.io.
- **A fintech study** run beside `local-businesses`, because we no longer publish it.
- **Perplexity and Microsoft Copilot,** which weren't asked: the monitoring service couldn't reach them logged out.

**Limits of 2026-11.**
- **One or two runs on one or two days.** Answers change about half their list between runs, so one city's list is a snapshot, not a ranking.
- **Two dates in two studies.** Claude and Google AI Overviews were asked two days after the other engines.
- **Names from free text.** The rules miss a few names and keep a few that aren't businesses, Gemini's most.
- **A link, not a cause.** Named businesses had more Google reviews; that doesn't prove the reviews got them named.
- **Ranking sites unchecked.** We didn't check who runs the directories and ranking sites the engines cited, or whether their listings are paid.

For what these numbers can and cannot tell you about your own brand, see Cited's guide on [how to measure AI visibility](https://cited.io/guides/how-to-measure-ai-visibility).

---

Maintained by [Cited](https://cited.io), an AI search and SEO agency.
