# opensearch-models

Workbench for fine-tuning and evaluating the models used by OpenSearch.
This project is never deployed. It produces one output — fine-tuned distilbert
weights — that opensearch/model_service consumes.

---

## What This Project Produces

```
models/classifier/          fine-tuned distilbert weights via SetFit
                            copy this directory to opensearch/models/classifier/
```

The cross-encoder (cross-encoder/ms-marco-MiniLM-L6-v2) needs no fine-tuning.
Notebook 04 loads and validates it against sample pairs only.

---

## Project Structure

```
opensearch-models/
    notebooks/
        01_explore_dataset.ipynb      explore and validate training data
        02_fine_tune_classifier.ipynb fine-tune distilbert via SetFit
        03_evaluate_classifier.ipynb  evaluate accuracy on held-out test set
        04_evaluate_relevance.ipynb   validate cross-encoder on sample pairs
    data/
        raw/
            queries.csv               labelled query examples
        processed/                    train/val/test splits written here by 01
    models/
        classifier/                   exported weights written here by 02
    docs/
        DATASET.md                    labelling guidelines
        TRAINING.md                   fine-tuning procedure
        EXPORT.md                     how to export and transfer weights
    requirements.txt
    README.md
```

---

## Setup

```bash
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

Open the project in VS Code. Select the `.venv` interpreter from the kernel
picker in the top-right of any notebook. No separate Jupyter server needed.

Required VS Code extensions:
- ms-python.python
- ms-toolsai.jupyter
- ms-python.pylance

---

## Workflow

```
1. Build and label the dataset       data/raw/queries.csv (done — see docs/DATASET.md)
2. Explore the dataset               run 01_explore_dataset.ipynb
3. Fine-tune the classifier          run 02_fine_tune_classifier.ipynb
4. Evaluate accuracy                 run 03_evaluate_classifier.ipynb
5. Validate cross-encoder            run 04_evaluate_relevance.ipynb
6. Export weights                    see docs/EXPORT.md
7. Copy to opensearch                cp -r models/classifier/ ../opensearch/models/classifier/
```

---

## Accuracy Thresholds

The classifier is not ready to export until it clears both of these on the
held-out test set:

```
overall accuracy        > 90%
per-class F1            > 0.85 on every class
```

If any class falls below, add more examples for that class in queries.csv,
rerun notebook 01 to regenerate splits, and retrain.

---

## Intent Classes

```
news          recent events, breaking news, scores, announcements
factual       single-fact lookups, definitions, dates, conversions
code          programming, debugging, syntax, documentation, tools
research      summaries, comparisons, deep reads, academic topics
commercial    product comparisons, prices, reviews, recommendations
general       everything that does not fit a specific class
```
