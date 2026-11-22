# Who AI names for B2B software in 20 categories (2026-11-19 and 2026-11-21)

We asked “What’s the best {category} for a mid-size B2B company?” for 20 software categories, from the US: ChatGPT and Google AI Mode twice and Gemini once on 2026-11-19, then Claude and Google AI Overviews once on 2026-11-21.
Written up on cited.io: [Who AI Cites: B2B software](https://cited.io/who-ai-cites/b2b-software).

Every number below is printed by the Python above it, run from this folder (Python 3, standard library only). Each code block starts from this setup:

```python
import csv, json, statistics as st
from collections import Counter
rows = lambda f: list(csv.DictReader(open(f, encoding="utf-8")))
top = rows("top_domains.csv")
def leaders(study, engine, kind="cited", n=5):
    return [f"{r['domain']} ({r['answers_or_pages']})" for r in top if r["study"] == study and r["engine"] == engine and r["kind"] == kind][:n]
```

## Categories and searches

```python
import csv, json, statistics as st
from collections import Counter
rows = lambda f: list(csv.DictReader(open(f, encoding="utf-8")))
top = rows("top_domains.csv")
def leaders(study, engine, kind="cited", n=5):
    return [f"{r['domain']} ({r['answers_or_pages']})" for r in top if r["study"] == study and r["engine"] == engine and r["kind"] == kind][:n]

r = rows("b2b_by_category.csv")
print("categories:", len(r))
print("searches a month for 'best {category}', all categories:", sum(int(x["searches_a_month_best_category"] or 0) for x in r))
```

```text
categories: 20
searches a month for 'best {category}', all categories: 24610
```

## Products named per answer, and agreement

```python
import csv, json, statistics as st
from collections import Counter
rows = lambda f: list(csv.DictReader(open(f, encoding="utf-8")))
top = rows("top_domains.csv")
def leaders(study, engine, kind="cited", n=5):
    return [f"{r['domain']} ({r['answers_or_pages']})" for r in top if r["study"] == study and r["engine"] == engine and r["kind"] == kind][:n]

f = json.load(open("figures.json"))
print("named per answer (median):", f["five_apps"]["names_per_answer_median"])
print("same first pick, ChatGPT, AI Mode and Gemini:", f["three_apps"]["top_pick_same_on_all_three"])
print("same first pick, all five:", f["five_apps"]["top_pick_same_on_every_app"])
```

```text
named per answer (median): {'chatgpt': 4.0, 'aimode': 3.5, 'gemini': 5.0, 'aioverview': 4.0, 'claude': 4.0}
same first pick, ChatGPT, AI Mode and Gemini: 6 of 14
same first pick, all five: 5 of 13
```

## What kinds of pages each engine cited

Each cited page sorted by kind by the study's reading.

```python
import csv, json, statistics as st
from collections import Counter
rows = lambda f: list(csv.DictReader(open(f, encoding="utf-8")))
top = rows("top_domains.csv")
def leaders(study, engine, kind="cited", n=5):
    return [f"{r['domain']} ({r['answers_or_pages']})" for r in top if r["study"] == study and r["engine"] == engine and r["kind"] == kind][:n]

f = json.load(open("figures.json"))["five_apps"]["cited_kinds"]
for engine, kinds in f.items():
    total = sum(v for k, v in kinds.items() if not k.startswith("_"))
    print(engine, total, "pages:", {k: f"{v} ({round(100 * v / total)}%)" for k, v in kinds.items() if not k.startswith("_")})
```

```text
chatgpt 262 pages: {'third-party comparison / listicle': '150 (57%)', "vendor's own site": '103 (39%)', 'review site / analyst': '8 (3%)', 'google (maps, listing)': '1 (0%)'}
aimode 711 pages: {'third-party comparison / listicle': '450 (63%)', "vendor's own site": '91 (13%)', 'youtube': '79 (11%)', 'reddit': '52 (7%)', 'review site / analyst': '39 (5%)'}
gemini 120 pages: {'third-party comparison / listicle': '82 (68%)', "vendor's own site": '30 (25%)', 'review site / analyst': '6 (5%)', 'reddit': '1 (1%)', 'youtube': '1 (1%)'}
aioverview 162 pages: {'third-party comparison / listicle': '107 (66%)', "vendor's own site": '28 (17%)', 'review site / analyst': '11 (7%)', 'reddit': '7 (4%)', 'youtube': '6 (4%)', 'listing / list site': '2 (1%)', 'local press / city magazine': '1 (1%)'}
claude 137 pages: {'third-party comparison / listicle': '119 (87%)', "vendor's own site": '12 (9%)', 'review site / analyst': '3 (2%)', 'local press / city magazine': '2 (1%)', 'google (maps, listing)': '1 (1%)'}
```

## The sites each engine cited most

```python
import csv, json, statistics as st
from collections import Counter
rows = lambda f: list(csv.DictReader(open(f, encoding="utf-8")))
top = rows("top_domains.csv")
def leaders(study, engine, kind="cited", n=5):
    return [f"{r['domain']} ({r['answers_or_pages']})" for r in top if r["study"] == study and r["engine"] == engine and r["kind"] == kind][:n]

for engine in ("chatgpt", "aimode", "gemini", "claude", "aioverview"):
    print(engine, leaders("b2b-software", engine))
```

```text
chatgpt ['airbyte.com (11)', 'dupple.com (10)', 'itechguides.com (8)', 'procurify.com (7)', 'bindlegal.com (7)']
aimode ['youtube.com (79)', 'reddit.com (52)', 'g2.com (15)', 'startwithidentity.com (15)', 'trustradius.com (14)']
gemini ['resources.rework.com (10)', 'bitwarden.com (8)', 'ytt-ai.com (6)', 'intercom.com (5)', 'thegrowthelements.com (5)']
claude ['tech-insider.org (4)', 'resources.rework.com (3)', 'guideflow.com (3)', 'checkthat.ai (3)', 'technologyadvice.com (2)']
aioverview ['reddit.com (7)', 'youtube.com (6)', 'g2.com (4)', 'gartner.com (3)', 'microsoft.com (3)']
```
