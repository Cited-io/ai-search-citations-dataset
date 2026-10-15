# Contributing to the AI search citations dataset

Issues and pull requests are welcome. Every correction or claim needs a source we can check.

## Report a problem in the data

Open an issue with:

- the release, `engine` and `prompt_id` of the answer (for example `2026-10`, `gemini`, `p03`);
- the row or field that looks wrong;
- why you think so, with a link or evidence.

We do not edit published rows to match what an engine shows today. Answers change from run to run, so a different answer now does not make the recorded one wrong. We fix processing errors: a malformed URL, a wrong domain, a row in the wrong answer.

## Suggest a prompt

Open an issue with the prompt text and the group it belongs to (`tools`, `agency` or `how-to`). Tell us why buyers ask it, ideally with a source such as search volume, a forum thread or a sales call pattern.
Accepted prompts are added in the next monthly release with a new `prompt_id`. Existing ids never change.

## Improve the docs or the summary

Pull requests that fix the docs, the SQL in a summary, or the [vendor list](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/data/2026-10/summary.md#vendor-sites-on-tool-questions) are welcome. A vendor added to the list needs a link to its product page showing it sells AI visibility tracking.

## Share what you built

If you publish an analysis using this data, open an issue with the link. We may list it in the README.

By contributing, you agree that your contribution is licensed under [CC BY 4.0](https://github.com/Cited-io/ai-search-citations-dataset/blob/main/LICENSE).
