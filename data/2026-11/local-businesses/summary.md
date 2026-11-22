# Who AI names: personal injury lawyers, roofers and med spas (2026-11-19)

We asked ChatGPT, Google AI Mode and Gemini who the best personal injury lawyer, roofing company and med spa is in each of 20 US cities, and read Google Maps' top 20 for the same search in the same city.
The businesses the engines named had several times the Google reviews of the Maps listings they passed over.
Written up on cited.io: [Who AI Cites: local businesses](https://cited.io/who-ai-cites/local-businesses).

The per-listing Google Maps results behind the review medians were not kept in a publishable form, so those medians are given as the study computed them (`figures.json`), not recomputed here. Every other number is recomputed from the files in this folder.

Every number below is printed by the Python above it, run from this folder (Python 3, standard library only). Each code block starts from this setup:

```python
import csv, json, statistics as st
from collections import Counter
rows = lambda f: list(csv.DictReader(open(f, encoding="utf-8")))
top = rows("top_domains.csv")
def leaders(study, engine, kind="cited", n=5):
    return [f"{r['domain']} ({r['answers_or_pages']})" for r in top if r["study"] == study and r["engine"] == engine and r["kind"] == kind][:n]
```

## Cities and Google Maps

```python
import csv, json, statistics as st
from collections import Counter
rows = lambda f: list(csv.DictReader(open(f, encoding="utf-8")))
top = rows("top_domains.csv")
def leaders(study, engine, kind="cited", n=5):
    return [f"{r['domain']} ({r['answers_or_pages']})" for r in top if r["study"] == study and r["engine"] == engine and r["kind"] == kind][:n]

for study in ("law", "roofing", "medspa"):
    r = rows(f"by_city_{study}.csv")
    print(study, "cities:", len(r), "| Maps read:", sum(x["maps_data"] == "yes" for x in r))
```

```text
law cities: 20 | Maps read: 20
roofing cities: 20 | Maps read: 20
medspa cities: 20 | Maps read: 20
```

## Businesses named per answer, and how often the engines agreed

Medians across the 20 cities, from the study's reading of every answer (`figures.json`). `top_pick_same_on_all_three` counts the cities where all three engines named a business and named the same one first.

```python
import csv, json, statistics as st
from collections import Counter
rows = lambda f: list(csv.DictReader(open(f, encoding="utf-8")))
top = rows("top_domains.csv")
def leaders(study, engine, kind="cited", n=5):
    return [f"{r['domain']} ({r['answers_or_pages']})" for r in top if r["study"] == study and r["engine"] == engine and r["kind"] == kind][:n]

fig = json.load(open("figures.json"))
for study, f in fig.items():
    print(study, "named per answer:", f["names_per_answer_median"], "| same first pick:", f["top_pick_same_on_all_three"])
```

```text
law named per answer: {'chatgpt': 5.0, 'aimode': 9.0, 'gemini': 1.0} | same first pick: 0 of 12
roofing named per answer: {'chatgpt': 5.0, 'aimode': 7.0, 'gemini': 5.5} | same first pick: 2 of 19
medspa named per answer: {'chatgpt': 5.0, 'aimode': 7.5, 'gemini': 2.5} | same first pick: 1 of 13
```

## Named businesses in Google Maps' top 20 and top 3

```python
import csv, json, statistics as st
from collections import Counter
rows = lambda f: list(csv.DictReader(open(f, encoding="utf-8")))
top = rows("top_domains.csv")
def leaders(study, engine, kind="cited", n=5):
    return [f"{r['domain']} ({r['answers_or_pages']})" for r in top if r["study"] == study and r["engine"] == engine and r["kind"] == kind][:n]

fig = json.load(open("figures.json"))
share = lambda s: f"{s} ({round(100 * int(s.split(' of ')[0]) / int(s.split(' of ')[1]))}%)"
for study, f in fig.items():
    print(study, "top 20:", {e: share(v) for e, v in f["named_in_maps_top20"].items()})
    print(study, "top 3: ", {e: share(v) for e, v in f["named_in_maps_top3"].items()})
```

```text
law top 20: {'chatgpt': '36 of 87 (41%)', 'aimode': '76 of 167 (46%)', 'gemini': '9 of 41 (22%)'}
law top 3:  {'chatgpt': '7 of 87 (8%)', 'aimode': '13 of 167 (8%)', 'gemini': '2 of 41 (5%)'}
roofing top 20: {'chatgpt': '37 of 92 (40%)', 'aimode': '74 of 145 (51%)', 'gemini': '31 of 112 (28%)'}
roofing top 3:  {'chatgpt': '7 of 92 (8%)', 'aimode': '17 of 145 (12%)', 'gemini': '6 of 112 (5%)'}
medspa top 20: {'chatgpt': '50 of 95 (53%)', 'aimode': '103 of 151 (68%)', 'gemini': '20 of 53 (38%)'}
medspa top 3:  {'chatgpt': '10 of 95 (11%)', 'aimode': '24 of 151 (16%)', 'gemini': '7 of 53 (13%)'}
```

## Google reviews: named against passed over

The median Google review count of the Maps top-20 listings any engine named, against those none named, with how many listings each side counts (named, passed over).

```python
import csv, json, statistics as st
from collections import Counter
rows = lambda f: list(csv.DictReader(open(f, encoding="utf-8")))
top = rows("top_domains.csv")
def leaders(study, engine, kind="cited", n=5):
    return [f"{r['domain']} ({r['answers_or_pages']})" for r in top if r["study"] == study and r["engine"] == engine and r["kind"] == kind][:n]

fig = json.load(open("figures.json"))
for study, f in fig.items():
    named, passed, n_named, n_passed = f["reviews_median_named_vs_not"]
    print(f"{study}: {named} vs {passed} reviews ({n_named} named listings, {n_passed} passed over)")
```

```text
law: 368 vs 68.5 reviews (103 named listings, 296 passed over)
roofing: 225 vs 52 reviews (99 named listings, 299 passed over)
medspa: 298.0 vs 90 reviews (134 named listings, 263 passed over)
```

## The sites each engine cited most

Cited means the engine quoted the page in its answer; listed means it only appeared among the sources.

```python
import csv, json, statistics as st
from collections import Counter
rows = lambda f: list(csv.DictReader(open(f, encoding="utf-8")))
top = rows("top_domains.csv")
def leaders(study, engine, kind="cited", n=5):
    return [f"{r['domain']} ({r['answers_or_pages']})" for r in top if r["study"] == study and r["engine"] == engine and r["kind"] == kind][:n]

for study in ("law", "roofing", "medspa"):
    for engine in ("chatgpt", "aimode", "gemini"):
        print(study, engine, leaders(study, engine))
```

```text
law chatgpt ['avvo.com (51)', 'expertise.com (20)', 'yelp.com (11)', 'rosefall.ai (11)', 'lawleaderboard.com (8)']
law aimode ['google.com (623)', 'attorneys.superlawyers.com (17)', 'ckolaw.com (9)', 'justia.com (7)', 'bestlawfirms.com (6)']
law gemini ['attorneys.superlawyers.com (32)', 'bestlawfirms.com (17)', 'firstindenver.com (9)', 'justia.com (7)', 'dirjournal.com (3)']
roofing chatgpt ['theroofingbrief.com (29)', 'avocolab.com (14)', 'omniestimate.com (14)', 'contractorsranked.com (8)', 'roofersranked.com (7)']
roofing aimode ['google.com (510)', 'reddit.com (17)', 'gaf.com (9)', 'm.yelp.com (8)', 'roofpitch.net (5)']
roofing gemini ['gaf.com (8)', 'allstateroofingaz.com (4)', 'roofpitch.net (4)', 'bestpickreports.com (4)', 'reddit.com (4)']
medspa chatgpt ['nobsmedspareviews.com (24)', 'medicalspalocator.com (17)', 'discovermedspa.com (9)', 'medicalspaproviders.com (4)', 'medsparankings.com (3)']
medspa aimode ['google.com (516)', 'reddit.com (8)', 'dashboard.boulevard.io (8)', 'ayaskincare.zenoti.com (4)', 'secretmedspa.zenoti.com (2)']
medspa gemini ['thecliniccompass.com (35)', 'discovermedspa.com (10)', 'medicalspaproviders.com (4)', 'trustanalytica.org (3)', 'reddit.com (2)']
```
