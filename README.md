# opensearch-models

Workbench for fine-tuning and evaluating the models used by OpenSearch.
This project is never deployed. It produces one output — fine-tuned distilbert
weights — that opensearch/model_service consumes.

---

## What This Project Produces

```
models/classifier/          fine-tuned distilbert weights
                            copy this directory to opensearch/models/classifier/
```

The cross-encoder (ms-marco-MiniLM-L6-v2) needs no fine-tuning. It downloads
from HuggingFace when the opensearch model service container starts.

---

## Project Structure

```
opensearch-models/
├── notebooks/
│   ├── 01_explore_dataset.ipynb      explore and validate training data
│   ├── 02_fine_tune_classifier.ipynb fine-tune distilbert via SetFit
│   ├── 03_evaluate_classifier.ipynb  evaluate accuracy on held-out test set
│   └── 04_evaluate_relevance.ipynb   validate cross-encoder on sample pairs
├── data/
│   ├── raw/                          raw labelled query examples
│   └── processed/                    cleaned and split datasets
├── models/
│   └── classifier/                   exported weights after fine-tuning
├── docs/
│   ├── DATASET.md                    labelling guidelines
│   ├── TRAINING.md                   fine-tuning procedure
│   └── EXPORT.md                     how to export and transfer weights
├── requirements.txt
└── README.md
```

---

## Setup

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
jupyter notebook
```

---

## Workflow

```
1. Build and label the dataset       see docs/DATASET.md
2. Explore the dataset               run 01_explore_dataset.ipynb
3. Fine-tune the classifier          run 02_fine_tune_classifier.ipynb
4. Evaluate accuracy                 run 03_evaluate_classifier.ipynb
5. Validate cross-encoder            run 04_evaluate_relevance.ipynb
6. Export weights                    see docs/EXPORT.md
7. Copy to opensearch                cp -r models/classifier/ ../opensearch/models/classifier/
```

---

## Requirements

```
torch
transformers
setfit
sentence-transformers
datasets
scikit-learn
jupyter
pandas
```
