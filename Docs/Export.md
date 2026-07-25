# Export

How to export the fine-tuned classifier weights and transfer them to opensearch.

---

## Exporting After Fine-Tuning

At the end of notebook `02_fine_tune_classifier.ipynb` save the model:

```python
model.save_pretrained("models/classifier/")
```

This saves the full model directory including:
```
models/classifier/
    config.json
    tokenizer_config.json
    tokenizer.json
    vocab.txt
    special_tokens_map.json
    pytorch_model.bin        (or model.safetensors)
    model_head.pkl           (SetFit classification head)
```

---

## Transferring to opensearch

Copy the entire directory:

```bash
cp -r models/classifier/ ../opensearch/models/classifier/
```

Or if the projects are not in adjacent directories adjust the path accordingly.

---

## Verifying the Transfer

After copying, verify the model service loads the weights correctly:

```bash
cd opensearch
docker compose up model_service
```

Check the logs for:
```
INFO  classifier loaded from models/classifier/
INFO  cross-encoder loaded from HuggingFace
INFO  model service ready on :50052
```

Send a test classification request via grpcurl or the integration test:

```bash
go test ./integration/... -run TestModelServiceClassify -tags integration
```

---

## When to Re-export

Re-export and transfer whenever the classifier is retrained on new data.
The model service loads weights at startup — restart the container after
copying new weights to pick them up.

```bash
docker compose restart model_service
```
