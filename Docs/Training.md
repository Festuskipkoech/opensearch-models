# Training

How to fine-tune the intent classifier using SetFit.

---

## Why SetFit

SetFit is optimised for few-shot text classification. It fine-tunes a sentence
transformer using contrastive learning on pairs of examples, then trains a
lightweight classification head on top. It reaches high accuracy with far fewer
labelled examples than standard fine-tuning and trains in minutes on CPU.

---

## Base Model

`distilbert-base-uncased`
66MB, fast CPU inference, strong English text classification baseline.

---

## Procedure

Run notebook `02_fine_tune_classifier.ipynb` which executes these steps:

**1. Load the dataset**
Load the CSV from data/processed/train.csv. Verify class distribution is
reasonably balanced — no class should have fewer than 100 examples.

**2. Initialise the SetFit trainer**
```
model:          sentence-transformers/paraphrase-mpnet-base-v2
classifier:     LogisticRegression
num_epochs:     1 (contrastive learning phase)
num_iterations: 20 (pairs per example)
```

**3. Train**
SetFit trains in two phases automatically:
- Contrastive fine-tuning of the sentence transformer
- Fitting the classification head on the labelled examples

Total training time on CPU: 5-15 minutes depending on dataset size.

**4. Evaluate on validation set**
After training evaluate on data/processed/val.csv.
Check per-class precision, recall, and F1. All classes must exceed 0.85 F1.
If any class falls below, inspect misclassified examples, add clearer examples
for that class, and retrain.

**5. Final evaluation on test set**
Run once only after all decisions are finalised.
Overall accuracy must exceed 90%. Per-class F1 must exceed 0.85.
If the model fails these thresholds do not export. Fix the dataset and retrain.

---

## Hyperparameters to Tune if Accuracy is Low

```
num_iterations    increase to 40 for more contrastive pairs
num_epochs        increase to 3 for longer fine-tuning
classifier        try SGDClassifier if LogisticRegression underperforms
```

Do not change the base model unless accuracy remains below 85% after dataset
improvements and hyperparameter tuning. If switching models, try
`sentence-transformers/all-mpnet-base-v2` next.

---

## Retraining from Real Traffic

Once OpenSearch accumulates real query traffic, export queries with confirmed
correct intent labels and add them to the training dataset. Retrain periodically
— monthly or when accuracy monitoring shows degradation on a specific class.

The retraining procedure is identical to the initial training procedure.
Export new weights and copy them to opensearch/models/classifier/.
Restart the model service container to load the new weights.
