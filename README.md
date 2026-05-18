# AIaP Assessment 3: Misinformation Verification System

This repository contains a real-world AI application for political misinformation verification using the LIAR dataset. The system estimates claim credibility by combining neural text classification, structural knowledge representation, symbolic reasoning, and Bayesian probabilistic fusion.

## Group Members

| Student name | Student ID |
|---|---|
| Shanu Sharma   | 25979360     |
| [Student name] | [Student ID] |
| [Student name] | [Student ID] |
| [Student name] | [Student ID] |
| [Student name] | [Student ID] |
| [Student name] | [Student ID] |

## AI Methods Used

- Feature engineering and exploratory data analysis over the LIAR benchmark dataset.
- RoBERTa transformer fine-tuning for claim text classification.
- Metadata-based knowledge graph representation using speakers, parties, topics, and historical verdicts.
- Symbolic reasoning rules for interpretable credibility signals.
- Bayesian fusion to combine RoBERTa, KG, and speaker-prior evidence.
- Responsible AI reflection covering bias, uncertainty, false positives, false negatives, scalability, and GenAI/LLM alternatives.

## Project Structure

```text
misinformation_verification_system/
  app.py                         Streamlit demo application
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

LIAR_dataset/                    Source train/validation/test TSV files
docs/                            Report, rubric, presentation, and contribution drafts
report_assets/                   Figures and tables for the final report
requirements.txt                 Python dependencies
```

## Workflow

```text
LIAR_dataset
  -> Feature engineering and EDA
  -> RoBERTa fine-tuning
  -> Knowledge graph reasoning
  -> Bayesian fusion
  -> Streamlit demo
```

## Current Results

The corrected pipeline processes 12,791 LIAR claims. KG history is built from train/validation records only, then evaluated on the test split to avoid test-label leakage.

| Component | Result |
|---|---:|
| RoBERTa binary signal accuracy | 0.6737 |
| KG reasoning accuracy on decidable test claims | 0.6387 |
| Bayesian fusion accuracy on binary test claims | 0.7176 |
| Bayesian fusion ROC-AUC | 0.8154 |

## Setup

Install dependencies:

```bash
pip install -r requirements.txt
```

Install the spaCy model if running the feature engineering notebook:

```bash
python -m spacy download en_core_web_sm
```

Run the demo:

```bash
streamlit run misinformation_verification_system/app.py
```

## Main Files

| File | Purpose |
|---|---|
| `misinformation_verification_system/notebooks/01_feature_engineering_eda.ipynb` | Dataset loading, EDA, label mapping, feature engineering |
| `misinformation_verification_system/notebooks/02_roberta_finetuning.ipynb` | RoBERTa training and probability scoring |
| `misinformation_verification_system/notebooks/03_kg_reasoning.ipynb` | Knowledge graph construction and symbolic reasoning |
| `misinformation_verification_system/notebooks/04_bayesian_fusion.ipynb` | Final probabilistic fusion and evaluation |
| `misinformation_verification_system/app.py` | Streamlit demo |
| `docs/AT3_Report_Draft.docx` | Report draft |

