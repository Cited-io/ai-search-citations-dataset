# Who AI names for dental implants in 50 US cities (2026-11-19 and 2026-11-21)

We asked “Who’s the best dentist for dental implants in {city}, {state}?” in 50 US cities: ChatGPT and Google AI Mode twice and Gemini once on 2026-11-19, then Claude and Google AI Overviews once on 2026-11-21.
Written up on cited.io: [Who AI Cites: dental implants](https://cited.io/who-ai-cites/dental-implants).

`figures.json` holds the study's figures for the first three engines (`three_apps`) and for all five (`five_apps`). The per-listing Google Maps results behind the review medians were not kept in a publishable form, so those medians are given as computed; the rest is recomputed here.

Every number below is printed by the Python above it, run from this folder (Python 3, standard library only). Each code block starts from this setup:

```python
import csv, json, statistics as st
from collections import Counter
rows = lambda f: list(csv.DictReader(open(f, encoding="utf-8")))
top = rows("top_domains.csv")
def leaders(study, engine, kind="cited", n=5):
    return [f"{r['domain']} ({r['answers_or_pages']})" for r in top if r["study"] == study and r["engine"] == engine and r["kind"] == kind][:n]
```

## Cities, searches and Google

```python
import csv, json, statistics as st
from collections import Counter
rows = lambda f: list(csv.DictReader(open(f, encoding="utf-8")))
top = rows("top_domains.csv")
def leaders(study, engine, kind="cited", n=5):
    return [f"{r['domain']} ({r['answers_or_pages']})" for r in top if r["study"] == study and r["engine"] == engine and r["kind"] == kind][:n]

r = rows("practices_by_city.csv")
print("cities:", len(r))
print("searches a month for 'dental implants {city}', all cities:", sum(int(x["searches_a_month_dental_implants_city"] or 0) for x in r))
print("cities with a Google AI Overview:", sum(x["google_ai_overview"] == "yes" for x in r))
print("cities with Maps results:", sum(x["maps_data"] == "yes" for x in r))
```

```text
cities: 50
searches a month for 'dental implants {city}', all cities: 27950
cities with a Google AI Overview: 25
cities with Maps results: 49
```

**Searches:** the file sums to 27,950 a month across its 50 cities, but Portland, Maine and Portland, Oregon share one search ("dental implants portland", 480), so the 49 distinct searches total 27,470, the figure cited.io quotes.

## Practices named per answer, and agreement

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
named per answer (median): {'chatgpt': 4.0, 'aimode': 7.0, 'gemini': 3.0, 'aioverview': 3.0, 'claude': 5.0}
same first pick, ChatGPT, AI Mode and Gemini: 2 of 37
same first pick, all five: 0 of 34
```

## Named practices in Google Maps' top 20 and top 3

```python
import csv, json, statistics as st
from collections import Counter
rows = lambda f: list(csv.DictReader(open(f, encoding="utf-8")))
top = rows("top_domains.csv")
def leaders(study, engine, kind="cited", n=5):
    return [f"{r['domain']} ({r['answers_or_pages']})" for r in top if r["study"] == study and r["engine"] == engine and r["kind"] == kind][:n]

f = json.load(open("figures.json"))["five_apps"]
share = lambda s: f"{s} ({round(100 * int(s.split(' of ')[0]) / int(s.split(' of ')[1]))}%)"
for e in f["named_in_maps_top20"]:
    print(e, "top 20:", share(f["named_in_maps_top20"][e]), "| top 3:", share(f["named_in_maps_top3"][e]))
```

```text
chatgpt top 20: 125 of 196 (64%) | top 3: 33 of 196 (17%)
aimode top 20: 245 of 328 (75%) | top 3: 67 of 328 (20%)
gemini top 20: 64 of 153 (42%) | top 3: 21 of 153 (14%)
aioverview top 20: 107 of 159 (67%) | top 3: 39 of 159 (25%)
claude top 20: 126 of 267 (47%) | top 3: 34 of 267 (13%)
```

## Google reviews: named against passed over

```python
import csv, json, statistics as st
from collections import Counter
rows = lambda f: list(csv.DictReader(open(f, encoding="utf-8")))
top = rows("top_domains.csv")
def leaders(study, engine, kind="cited", n=5):
    return [f"{r['domain']} ({r['answers_or_pages']})" for r in top if r["study"] == study and r["engine"] == engine and r["kind"] == kind][:n]

f = json.load(open("figures.json"))
for k in ("three_apps", "five_apps"):
    named, passed, n_named, n_passed = f[k]["reviews_median_named_vs_not"]
    print(f"{k}: {named} vs {passed} reviews ({n_named} named listings, {n_passed} passed over)")
```

```text
three_apps: 357 vs 243.5 reviews (381 named listings, 582 passed over)
five_apps: 361 vs 228.5 reviews (419 named listings, 544 passed over)
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
    print(engine, leaders("dental-implants", engine))
```

```text
chatgpt ['dentistsranked.com (36)', 'yelp.com (25)', 'google.com (25)', 'healthgrades.com (20)', 'dentalimplantdirectory.com (19)']
aimode ['google.com (1393)', 'clearchoice.com (12)', 'book.modento.io (11)', 'app.nexhealth.com (11)', 'api.leadconnectorhq.com (4)']
gemini ['toprateddentist.com (6)', 'southerndentalimplant.com (5)', 'tnoralsurgery.com (4)', 'umomsa.com (4)', 'westhartfordoralsurgeon.com (4)']
claude ['yelp.com (65)', 'clearchoice.com (12)', 'nuviasmiles.com (5)', 'dental.me (3)', 'realself.com (3)']
aioverview ['clearchoice.com (10)', 'm.yelp.com (3)', 'nuviasmiles.com (3)', 'gherardiandmoore.com (2)', 'dentistsranked.com (2)']
```
