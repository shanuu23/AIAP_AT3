# AIaP Assessment 3: Misinformation Detection and Analysis

## Project Title

**Hybrid AI-Based Misinformation Detection Using RoBERTa, Knowledge Graph Reasoning, and Bayesian Credibility Fusion**

---

## Group Details

**Subject:** 36121 Artificial Intelligence Principles and Applications  
**Group Number:** 17  

| Student Name | Student ID |
|---|---:|
| Yash Bankar | 25717428 |
| Harshini Prasad | 26025369 |
| Remith Sajin | 25502170 |
| Shameel Zeshan Khader Sheriff | 26030371 |
| Shanu Sharma | 25979360 |
| Vit Patel | 25524051 |

---

## Project Overview

This project investigates how artificial intelligence can support misinformation detection in political claims. Political misinformation is difficult to verify automatically because claims are short, context-dependent, and often require background knowledge beyond the wording of the claim itself.

The project implements a hybrid AI pipeline using the LIAR dataset, a public benchmark of 12,791 labelled political claims from PolitiFact. The system combines neural language modelling, structural knowledge representation, symbolic reasoning, and Bayesian probabilistic fusion to estimate claim credibility.

The system is designed as a **decision-support tool**, not an automatic truth authority. Its purpose is to help identify potentially misleading claims and provide confidence-aware outputs that can support human fact-checking.

---

## Real-World Problem

Manual fact-checking is valuable but slow. Political claims can spread widely before expert reviewers are able to respond. Automated AI systems can help by prioritising claims for review, identifying high-risk claims, and presenting confidence scores.

However, misinformation detection is ethically sensitive because the data includes speaker identities, political affiliation, and historical credibility information. Incorrect predictions may create reputational harm or reinforce political bias. For this reason, the system reports probabilistic confidence and should be interpreted as an assistive tool for human reviewers.

---

## Dataset

The project uses the **LIAR dataset** introduced by Wang (2017). It contains labelled political claims collected from PolitiFact.

### Dataset Size

| Split | Records |
|---|---:|
| Training | 10,240 |
| Validation | 1,284 |
| Test | 1,267 |
| Total | 12,791 |

### Original Labels

The dataset contains six veracity labels:

- `pants-fire`
- `false`
- `barely-true`
- `half-true`
- `mostly-true`
- `true`

For binary evaluation, labels were mapped as follows:

| Binary Class | LIAR Labels |
|---|---|
| False | `pants-fire`, `false`, `barely-true` |
| True | `mostly-true`, `true` |
| Excluded from binary evaluation | `half-true` |

The `half-true` class was excluded from binary evaluation because it represents an ambiguous middle category that is not naturally reducible to a hard true/false verdict.

---

## AI Methods Used

### 1. Feature Engineering and EDA

The first module loads the LIAR train, validation, and test files, performs exploratory data analysis, and extracts structured features such as:

- speaker identity
- party affiliation
- topic
- claim length
- word count
- presence of numbers, years, percentages, and money values
- named entities
- historical speaker lie rate

These features provide the foundation for both symbolic reasoning and probabilistic fusion.

### 2. RoBERTa Text Classification

A RoBERTa model is fine-tuned on LIAR claims to learn linguistic patterns associated with different veracity labels. The model produces a probability distribution over the six LIAR classes. For binary fusion, the probabilities for `mostly-true` and `true` are combined into a single `P(true | text)` score.

RoBERTa is useful because it captures contextual language patterns in short political claims. However, text alone is limited because many claims require background knowledge or speaker history.

### 3. Knowledge Graph and Symbolic Reasoning

A metadata-based knowledge graph is constructed using LIAR speaker metadata.

The graph includes:

- speaker nodes
- topic nodes
- party nodes
- verdict nodes

Edges represent relationships such as:

- speaker makes claims about a topic
- speaker belongs to a party
- speaker has historical verdict patterns

Symbolic rules are then applied to reason over this structure. For example, a speaker with a high historical false-claim rate may contribute a `likely_false` signal, while a speaker with a low lie rate may contribute a `likely_true` signal.

### 4. Bayesian Credibility Fusion

The final module combines three signals:

| Signal | Role |
|---|---|
| Speaker lie rate | Prior probability |
| RoBERTa true probability | Text-based likelihood |
| KG verdict and confidence | Symbolic reasoning likelihood |

The Bayesian fusion layer combines these signals using weighted log-probability fusion and outputs:

- final binary verdict
- `P(true)`
- `P(false)`
- confidence score

This allows the system to express uncertainty rather than only producing a hard label.

---

## Project Structure

```text
AIAP_AT3/
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── LIAR_dataset/
│   ├── train.tsv
│   ├── valid.tsv
│   ├── test.tsv
│   └── README
│
├── misinformation_verification_system/
│   ├── app.py
│   │
│   ├── notebooks/
│   │   ├── 01_feature_engineering_eda.ipynb
│   │   ├── 02_roberta_finetuning.ipynb
│   │   ├── 03_kg_reasoning.ipynb
│   │   └── 04_bayesian_fusion.ipynb
│   │
│   └── outputs/
│       ├── features.json
│       ├── roberta_scores.json
│       ├── kg_results.json
│       └── final_verdicts.json
│
└── report_assets/
    └── figures/
        ├── eda_overview.png
        ├── feature_analysis.png
        ├── bayesian_analysis.png
        └── roc_curve.png
```

## Workflow

```text
LIAR Dataset
    ↓
Feature Engineering and EDA
    ↓
RoBERTa Fine-Tuning
    ↓
Knowledge Graph Construction and Symbolic Reasoning
    ↓
Bayesian Fusion
    ↓
Final Verdict + Confidence Score
    ↓
Streamlit Demo
```

## Current Results

The final notebook evaluation reports the following results on the binary test setting.

| Component | Result |
|---|---:|
| RoBERTa text-only binary accuracy | 67.37% |
| Knowledge graph accuracy on decidable binary test claims | 76.15% |
| Knowledge graph coverage on binary test set | 80.34% |
| Bayesian fusion accuracy | 75.05% |
| Bayesian fusion ROC-AUC | 0.8416 |

## Result Interpretation

The results show that text-only misinformation detection is challenging. RoBERTa reaches 67.37% binary accuracy, which suggests that claim wording contains useful signals but is not sufficient on its own. Many political claims require speaker history, context, and background knowledge that cannot be fully inferred from the claim sentence alone.

The knowledge graph performs strongly on claims where symbolic rules can produce a decision. Its 76.15% accuracy shows that structural metadata such as speaker history, party affiliation, topic patterns, and historical false-claim rates provide useful credibility evidence. However, the KG does not decide every claim, so coverage must be reported together with accuracy.

Bayesian fusion achieves 75.05% accuracy and a ROC-AUC of 0.8416. This is important because ROC-AUC measures how well the system separates true and false claims across different probability thresholds. In a real decision-support setting, calibrated probabilities are more useful than only a single hard verdict because reviewers can prioritise high-risk or low-confidence claims.

## Evaluation Note

The binary evaluation excludes the `half-true` LIAR class because it represents an ambiguous middle category. The final binary test set contains 1,002 claims.

The knowledge graph accuracy is calculated only on 805 decidable binary test claims where the symbolic rules produced either `likely_true` or `likely_false`. Its coverage on the binary test set is 80.34%. Claims marked `unverifiable` are not counted in KG-only accuracy.

Bayesian fusion is evaluated on the full binary test set and combines RoBERTa probability, KG evidence, and speaker credibility prior. Although KG has slightly higher accuracy on decidable claims, Bayesian fusion provides calibrated probabilities across the full binary evaluation set and achieves a ROC-AUC of 0.8416.

## Responsible AI and Ethics

Misinformation detection is a sensitive AI application because the system works with political claims, speaker identities, party information, and historical credibility patterns. A wrong prediction can create reputational harm, reinforce political bias, or incorrectly flag a claim as misleading. For this reason, this project treats the model as a **decision-support system** rather than an automatic truth authority.

### Ethical Risks Considered

| Risk | Why It Matters | Mitigation in This Project |
|---|---|---|
| False positives | A true claim may be incorrectly labelled as false, which could unfairly damage a speaker's credibility. | The system reports probability and confidence scores instead of only a hard verdict. |
| False negatives | A false claim may be incorrectly labelled as true, allowing misinformation to appear credible. | Multiple AI signals are combined so the final decision does not rely on text alone. |
| Political or speaker bias | Historical speaker records may reflect media coverage patterns, political imbalance, or dataset bias. | Speaker history is used as only one weighted signal, not the full decision. |
| Over-reliance on automation | Users may treat the model output as final truth without human review. | The README, report, and demo describe the system as human-in-the-loop decision support. |
| Lack of explainability | Black-box predictions are difficult to trust in high-impact domains. | The app shows separate RoBERTa, knowledge graph, speaker prior, and Bayesian fusion signals. |
| Ambiguous truth labels | Some political claims are partly true, context-dependent, or difficult to reduce to binary labels. | The `half-true` class is excluded from binary evaluation and discussed as a limitation. |

### Responsible Use Position

The system should be used to **prioritise claims for review**, highlight uncertainty, and support fact-checkers with structured evidence. It should not be used to automatically censor, punish, rank, or publicly label individuals without independent human verification.

The Streamlit demo is designed to make the reasoning process more transparent by showing:

- the RoBERTa text-based signal
- the knowledge graph reasoning signal
- the speaker credibility prior
- the Bayesian posterior probability
- the final verdict and confidence score

### Limitations from an Ethics Perspective

The LIAR dataset is based on political claims from PolitiFact, so the system reflects the scope and judgement criteria of that source. The model may not generalise well to non-political misinformation, newer events, other countries, different languages, or claims requiring live external evidence. The knowledge graph also relies on historical metadata, so it can reproduce past dataset biases if used without careful human interpretation.

Overall, the ethical design goal is not to replace human judgement, but to make misinformation review more structured, explainable, and uncertainty-aware.


## Development Environment Setup

### Prerequisites

Before running the project, make sure you have:

- Python 3.10 or higher installed
- Git installed, if cloning from GitHub
- VS Code, Jupyter Notebook, Kaggle, or Google Colab
- A terminal or command prompt
- Recommended: a Python virtual environment

Check your Python version:

```bash
python --version
```

## Streamlit Demo

The project includes a Streamlit demo application that allows users to inspect claim-level predictions and view:

- final verdict
- confidence score
- RoBERTa text signal
- knowledge graph signal
- Bayesian fusion output

Run the demo from the root project folder:

```bash
streamlit run misinformation_verification_system/app.py
```

## Step-by-Step Setup

### 1. Clone or Download the Repository

Clone the GitHub repository:

```bash
git clone https://github.com/shanuu23/AIAP_AT3.git
cd AIAP_AT3
```

Alternatively, download the repository as a ZIP file from GitHub and extract it.

### 2. Create a Virtual Environment

Create a virtual environment named `venv`:

```bash
python -m venv venv
```

Activate it on Mac/Linux:

```bash
source venv/bin/activate
```

Activate it on Windows:

```bash
venv\Scripts\activate
```

### 3. Install Required Packages

Install all project dependencies from `requirements.txt`:

```bash
pip install -r requirements.txt
```

If you are running the feature engineering notebook locally, also install the spaCy English model:

```bash
python -m spacy download en_core_web_sm
```

The main packages installed include:

- `streamlit` — builds the interactive web application interface
- `pandas` — loads and processes LIAR dataset records and model outputs
- `numpy` — supports numerical operations in Bayesian fusion
- `matplotlib` and `seaborn` — generate result visualisations
- `scikit-learn` — provides evaluation metrics such as accuracy and ROC-AUC
- `spacy` — supports named entity recognition during feature engineering
- `transformers` — provides the RoBERTa model and tokenizer
- `torch` — supports RoBERTa fine-tuning
- `networkx` — constructs and analyses the knowledge graph
- `jupyter` — runs the project notebooks

## How to Run the Streamlit Application

The project includes a Streamlit demo called **Misinformation Verification System**. The app allows users to inspect claim-level predictions and view the RoBERTa signal, knowledge graph signal, speaker prior, Bayesian fusion probability, final verdict, and confidence score.

From the root project folder, run:

```bash
streamlit run misinformation_verification_system/app.py
```

Streamlit will usually open automatically in your browser at:

```text
http://localhost:8501
```

If it does not open automatically, copy the URL shown in the terminal and paste it into your browser.

To stop the app, return to the terminal and press:

```text
Ctrl + C
```


## How to Run the AI Pipeline

The notebooks should be run in order because each module produces outputs used by the next module.

```text
1. misinformation_verification_system/notebooks/01_feature_engineering_eda.ipynb
2. misinformation_verification_system/notebooks/02_roberta_finetuning.ipynb
3. misinformation_verification_system/notebooks/03_kg_reasoning.ipynb
4. misinformation_verification_system/notebooks/04_bayesian_fusion.ipynb
```

| Step | Notebook | Purpose | Main Output |
|---|---|---|---|
| 1 | `01_feature_engineering_eda.ipynb` | Loads LIAR data, performs EDA, maps labels, and engineers features | `features.json` |
| 2 | `02_roberta_finetuning.ipynb` | Fine-tunes RoBERTa and generates claim-level probability scores | `roberta_scores.json` |
| 3 | `03_kg_reasoning.ipynb` | Builds the knowledge graph and applies symbolic reasoning rules | `kg_results.json` |
| 4 | `04_bayesian_fusion.ipynb` | Combines RoBERTa, KG, and speaker prior signals using Bayesian fusion | `final_verdicts.json` |

## Future Enhancements

- Add live evidence retrieval from reliable external fact-checking or news sources to support claims beyond the LIAR dataset.
- Improve probability calibration so confidence values more closely match real-world correctness rates.
- Evaluate fairness across parties, speakers, and topics to identify whether some groups are systematically over-flagged.
- Extend the system to multi-class veracity prediction instead of only binary true/false classification.
- Add stronger handling for ambiguous claims such as `half-true`, where a single binary verdict may be misleading.
- Compare the hybrid system with retrieval-augmented LLM explanations while keeping human verification in the loop.
- Test scalability on larger and more recent misinformation datasets.

## References

- Wang, W. Y. (2017). LIAR, LIAR Pants on Fire: A New Benchmark Dataset for Fake News Detection. *Proceedings of the 55th Annual Meeting of the Association for Computational Linguistics*.
- Liu, Y., Ott, M., Goyal, N., Du, J., Joshi, M., Chen, D., Levy, O., Lewis, M., Zettlemoyer, L., and Stoyanov, V. (2019). RoBERTa: A Robustly Optimized BERT Pretraining Approach.
- PolitiFact. LIAR dataset source claims and veracity labels.
- Streamlit documentation: https://docs.streamlit.io/
- scikit-learn documentation: https://scikit-learn.org/
- NetworkX documentation: https://networkx.org/
- spaCy documentation: https://spacy.io/

## GenAI Acknowledgement

Generative AI tools were used to support explanation, debugging, documentation improvement, and report/readme polishing. Final decisions, implementation outputs, interpretation of results, and academic responsibility remain with the project team.
