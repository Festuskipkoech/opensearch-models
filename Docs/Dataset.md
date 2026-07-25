# Dataset

The intent classifier needs labelled query examples — queries paired with their
correct intent class. This document covers how to build that dataset.

---

## Intent Classes

```
news          recent events, breaking developments, scores, results, announcements
factual       single-fact lookups, definitions, dates, distances, conversions
code          programming questions, debugging, documentation, syntax, tools
research      summaries, deep reads, comparisons, academic topics, explanations
commercial    product comparisons, prices, recommendations, reviews, purchases
general       everything that does not fit a specific class
```

---

## Minimum Dataset Size

200 examples per class minimum for the first model. 1000 per class is the
target for production accuracy. Clean labels matter more than volume — 200
carefully labelled examples outperform 1000 poorly labelled ones.

---

## Labelling Guidelines

**Label the intent of the person asking, not the topic.**

A query about Redis can be factual ("when was Redis released"), code
("how to use Redis pub/sub in Go"), research ("explain Redis persistence
tradeoffs"), or commercial ("best Redis hosting provider"). The topic is
the same. The intent is different. Label intent.

**When in doubt between two classes, pick the more specific one.**

"goroutine scheduling" is ambiguous between code and research. If it reads
like someone debugging or building something, label code. If it reads like
someone trying to understand a concept deeply, label research.

**Label what a reasonable person would want, not what the words literally say.**

"what is the best way to handle errors in Go" is code, not commercial —
even though it contains "best." The person wants a programming answer.

**Do not label edge cases in the starting set.**

The starting dataset should contain clear unambiguous examples. Edge cases
and boundary queries are handled by the confidence threshold at inference
time. Adding ambiguous examples to training data confuses the model.

---

## Dataset Format

CSV file with two columns — query and intent.

```
query,intent
"kenya election results today",news
"how to handle goroutine panic in go",code
"capital of france",factual
"summary of DDIA chapter 3",research
"best laptop under 1000 dollars",commercial
"how to make ugali",general
```

---

## Starting Examples Per Class

Use these as the seed. Expand to 200+ per class before fine-tuning.

**news**
```
kenya election results today
breaking news nairobi
premier league scores this weekend
latest developments in the region
what happened in parliament yesterday
stock market close today
new product launch announcement
government budget news
africa cup of nations results
weather forecast tomorrow
latest technology headlines
political developments this week
sports results today
economic news this morning
current events
```

**factual**
```
capital of france
when was redis created
speed of light
population of nairobi
who invented the telephone
height of mount kenya
what year did golang release
distance nairobi to mombasa
boiling point of water
bytes in a megabyte
official language of brazil
age of the universe
who is the president of kenya
when did world war two end
capital of japan
```

**code**
```
how to handle goroutine panic in go
python list comprehension syntax
rust lifetime error fix
javascript async await example
sql join types explained
docker compose volumes tutorial
git rebase vs merge
regex match email address
http status codes reference
postgres index types
implement binary search tree
golang channels tutorial
react useEffect hook example
kubernetes deployment yaml
unit tests in go
```

**research**
```
summary of designing data intensive applications
overview of transformer architecture
comparison of raft vs paxos
linux kernel scheduler internals
cap theorem distributed systems
history of internet protocols
tradeoffs of event sourcing
go garbage collector deep dive
bitcoin proof of work mechanism
vector databases overview
microservices vs monolith analysis
tls handshake in detail
large language model training research
consistency models distributed systems
database indexing strategies
```

**commercial**
```
best laptop under 1000
iphone versus samsung
cheapest flight nairobi london
mechanical keyboard for programming
macbook pro review
cheapest cloud provider for kubernetes
noise cancelling headphones
standing desk recommendations
gaming monitor under 300
best vpn service
wireless earbuds comparison
ergonomic office chair budget
smartphone camera comparison
project management software
antivirus software comparison
```

**general**
```
how to make ugali
tips for better sleep
things to do in nairobi
how to negotiate salary
good books to read
home workout no equipment
learn a new language fast
productivity tips remote work
how to start a podcast
car maintenance basics
healthy meal prep
improve public speaking
personal finance tips
grow tomatoes at home
time management tips
```

---

## Validation Split

Split the dataset 80% train, 10% validation, 10% test before fine-tuning.
Never train on the test set. Evaluate final model accuracy on the test set
only after all hyperparameter decisions are made.

Minimum acceptable accuracy on the test set: 90% across all classes.
If accuracy falls below 90% on any single class, add more examples for that
class and retrain.
