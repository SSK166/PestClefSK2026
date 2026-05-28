# PestClefSK2026 — Sree Krishna S at PestCLEF 2026

Code and notebooks for the Sree Krishna S submission to the [PestCLEF 2026](https://kaggle.com/competitions/pest-clef-2026) shared task at LifeCLEF, CLEF 2026.

**Final result:** Macro-averaged F1 of **0.1456** on the private test set — **10th place** on the final leaderboard.

---

## Repository Contents

| File | Description |
|------|-------------|
| `PestClefSK2026-work-f1.ipynb` | Version 1/2 — silver-label pipeline, txt-only loader (historical reference) |
| `PestClefSK2026_latest.ipynb` | Version 3 transition — corrected EPOP JSON loader and gold annotation integration |
| `PestClefSubmissionSKVSH.ipynb` | **Final submission notebook** — the exact code used to produce the submitted run |

---

## System Overview

A two-stage pipeline for Knowledge Graph extraction from plant pest documents:

1. **NER** — BioBERT (`dmis-lab/biobert-base-cased-v1.2`) fine-tuned for token classification across 7 entity types (Pest, Plant, Disease, Vector, Dissemination_pathway, Location, Date) using BIO tagging and weighted cross-entropy loss.

2. **Relation Classification** — Marker-based BioBERT classifier. Special boundary tokens (`[SUBJ_START]`, `[SUBJ_END]`, `[OBJ_START]`, `[OBJ_END]`) are inserted around candidate entity spans. Schema-based filtering constrains candidate pairs to biologically valid entity-type combinations before any model computation.

3. **KG Assembly** — Confidence thresholding, surface-form normalisation, and deduplication to produce the final JSON triples.

---

## Results

| Component | Metric | Value |
|-----------|--------|-------|
| NER | Token-level F1 (dev, epoch 8) | 0.622 |
| Relation classifier | Macro F1 (validation split) | 0.4145 |
| Full pipeline | Macro F1 (dev set) | 0.1403 |
| Full pipeline | Macro F1 (private test set) | **0.1456** |

---

## Reproducing the Final Submission

### 1. Environment

Run on Google Colab with a T4 GPU. Install dependencies:

```bash
pip install seqeval transformers datasets accelerate scikit-learn tqdm rapidfuzz
```

### 2. Data

Download the EPOP corpus from the [Kaggle competition page](https://kaggle.com/competitions/pest-clef-2026) (licence agreement required). Organise files as:

```
EPOP_documents/
  goldKG/
    train.json
    dev.json
    test.json
  train/    # .txt files for training documents
  dev/      # .txt files for development documents
  test/     # .txt files for test documents
```

Update `BASE_DIR` in `PestClefSubmissionSKVSH.ipynb` to point to this folder

### 3. Run

Execute all cells in order. The script will:
- Load gold annotations from the JSON splits
- Train the NER model (≈ 60 min on T4)
- Train the relation classifier (≈ 35 min on T4)
- Generate the submission CSV

All random seeds are fixed to 42. Minor numerical variation (±0.005 F1) is expected across runs due to stochastic training.

---

## Citation

If you use this code, please cite our working note:

```
Sree Krishna S, Sujith M, Varghese K James and Prabavathy Balasundaram.
Knowledge Graph Extraction from Plant Pest Documents Using BioBERT-based NER
and Marker-based Relation Classification. Sree Krishna S at PestCLEF 2026.
In Proceedings of the CLEF 2026 Working Notes, Jena, Germany, 2026.
```

---

## Acknowledgements

We thank the PestCLEF 2026 organisers at INRAE Paris-Saclay for providing the EPOP corpus and the evaluation infrastructure.
