# Misinformation Verification System

This folder contains the implemented AI pipeline and Streamlit demo.

## Contents

```text
app.py
notebooks/
  01_feature_engineering_eda.ipynb
  02_roberta_finetuning.ipynb
  03_kg_reasoning.ipynb
  04_bayesian_fusion.ipynb
outputs/
  features.json
  roberta_scores.json
  kg_results.json
  final_verdicts.json
```

## Module Summary

1. `01_feature_engineering_eda.ipynb` loads the LIAR dataset, performs exploratory analysis, maps labels, computes speaker credibility features, extracts named entities, and saves `features.json`.
2. `02_roberta_finetuning.ipynb` fine-tunes RoBERTa for six-class truth-label prediction and saves per-claim probability scores.
3. `03_kg_reasoning.ipynb` constructs a metadata knowledge graph and applies symbolic reasoning rules to produce KG verdicts.
4. `04_bayesian_fusion.ipynb` combines speaker prior, RoBERTa text evidence, and KG reasoning evidence into final true/false probabilities.
5. `app.py` presents final verdicts, model signals, and confidence values in a Streamlit interface.

## Run Demo

From the repository root:

```bash
streamlit run misinformation_verification_system/app.py
```

The app reads `outputs/final_verdicts.json`.
