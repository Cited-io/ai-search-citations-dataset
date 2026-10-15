# Schema: columns and fields in the AI search citations dataset

Each monthly release is a folder `data/YYYY-MM/` with three data files: `citations.csv` (one row per listed source), `answers.jsonl` (one object per answer) and `prompts.csv` (one row per prompt).
All files are UTF-8. The CSV files have a header row and use standard RFC 4180 quoting, because some URLs contain commas.
An answer is identified by `date`, `country`, `engine` and `prompt_id` together.

## Contents

- [citations.csv](#citationscsv)
- [answers.jsonl](#answersjsonl)
- [prompts.csv](#promptscsv)
- [Engine ids](#engine-ids)
- [Prompt groups](#prompt-groups)

## citations.csv

One row per (answer, listed URL). Answers that listed no sources have no rows here; use `answers.jsonl` to count them.

| Column | Type | Description | Example |
|---|---|---|---|
| `date` | date (`YYYY-MM-DD`) | Day the answer was collected, in UTC. | `2026-10-14` |
| `country` | text (ISO 3166-1 alpha-2) | Country the engine was asked from. | `US` |
| `engine` | text | The AI engine. See [Engine ids](#engine-ids). | `chatgpt` |
| `prompt_id` | text | Stable id of the prompt. The same id means the same prompt in every release. | `p01` |
| `prompt` | text | The prompt exactly as it was asked. | `best AI visibility tools` |
| `prompt_group` | text | The prompt's group. See [Prompt groups](#prompt-groups). | `tools` |
| `rank` | integer, from 1 | Position of this URL in the answer's source list, in the order the engine returned it, after removing duplicates. | `1` |
| `url` | text | The source URL as the engine returned it, with ChatGPT's `utm_source=chatgpt.com` parameter removed. | `https://zapier.com/blog/best-ai-visibility-tool/` |
| `domain` | text | The URL's hostname, lowercased, without a leading `www.`. Other subdomains are kept. | `zapier.com` |

A domain can appear on several rows of one answer when the engine listed several pages from it. To count answers that listed a domain, count distinct (`engine`, `prompt_id`) pairs, not rows.

## answers.jsonl

One JSON object per line, one line per answer, including answers with no sources.

| Field | Type | Description |
|---|---|---|
| `date` | string | As in `citations.csv`. |
| `country` | string | As in `citations.csv`. |
| `engine` | string | As in `citations.csv`. |
| `prompt_id` | string | As in `citations.csv`. |
| `prompt` | string | As in `citations.csv`. |
| `prompt_group` | string | As in `citations.csv`. |
| `domains` | array of strings | Distinct domains the answer listed, in order of first appearance. Empty if the answer listed no sources. |
| `urls` | array of strings | Distinct URLs the answer listed, in rank order. Same values and order as the answer's rows in `citations.csv`. |
| `fan_out_queries` | array of strings, or `null` | Web searches the engine showed it ran to build the answer, as returned. `null` means not collected for this engine. An empty array means collected, and the engine showed none. |

Example (lists shortened):

```json
{"date":"2026-10-14","country":"US","engine":"chatgpt","prompt_id":"p01","prompt":"best AI visibility tools","prompt_group":"tools","domains":["zapier.com","layer3labs.io","..."],"urls":["https://zapier.com/blog/best-ai-visibility-tool/","..."],"fan_out_queries":["best AI visibility tools generative engine optimization platforms 2026"]}
```

## prompts.csv

| Column | Type | Description |
|---|---|---|
| `prompt_id` | text | Stable id, `p` plus a two-digit number. Never reused for a different prompt. |
| `prompt` | text | The prompt exactly as it was asked. |
| `prompt_group` | text | See [Prompt groups](#prompt-groups). |

## Engine ids

| `engine` | Engine | Fan-out queries collected (2026-10) |
|---|---|---|
| `chatgpt` | ChatGPT | yes |
| `google_ai_overviews` | Google AI Overviews (in Google Search) | no (`null`) |
| `google_ai_mode` | Google AI Mode | no (`null`) |
| `gemini` | Google Gemini | no (`null`) |
| `copilot` | Microsoft Copilot | yes |
| `claude` | Claude (Anthropic) | yes |

New engines get new ids. Existing ids will not change.

## Prompt groups

| `prompt_group` | Meaning | Prompts in 2026-10 |
|---|---|---|
| `tools` | Choosing or comparing AI visibility and GEO software. | 8 |
| `agency` | Choosing an agency for GEO, AEO or AI SEO. | 6 |
| `how-to` | How AI search works and how to show up in it. | 9 |
| `brand` | Questions about one named company. Reserved; no prompts in this group are published in 2026-10. | 0 |

How the data was collected and cleaned: [METHODOLOGY.md](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/METHODOLOGY.md).

---

Maintained by [Cited](https://cited.io): AI SEO and GEO agency with its own AI visibility platform.
