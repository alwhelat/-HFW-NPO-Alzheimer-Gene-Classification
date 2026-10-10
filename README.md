<div align="center">

# HFW-NPO
### A Dual Paradigm Hybrid Filter–Wrapper Nomadic People Optimizer<br>for High-Dimensional Alzheimer's Gene Expression Classification

[![Python](https://img.shields.io/badge/Python-3.13-3776AB?style=flat-square&logo=python&logoColor=white)](https://www.python.org/)
[![License: CC-BY-3.0](https://img.shields.io/badge/License-CC--BY--3.0-22c55e?style=flat-square)](https://creativecommons.org/licenses/by/3.0/)
[![scikit-learn](https://img.shields.io/badge/scikit--learn-1.7.0-F7931E?style=flat-square&logo=scikitlearn&logoColor=white)](https://scikit-learn.org/)
[![Journal](https://img.shields.io/badge/MDPI-Electronics-2979FF?style=flat-square)](https://www.mdpi.com/2079-9292/15/13/2970)
[![DOI](https://img.shields.io/badge/DOI-10.3390%2Felectronics15132970-blue?style=flat-square)](https://doi.org/10.3390/electronics15132970)
[![Published](https://img.shields.io/badge/Status-Published%20July%202026-22c55e?style=flat-square)]()

<br>

> **Published in MDPI Electronics, Vol. 15, No. 13, Article 2970 (July 7, 2026)**  
> DOI: [10.3390/electronics15132970](https://doi.org/10.3390/electronics15132970)

<br>

> Feature selection for Alzheimer's disease gene expression data  
> combining a tri-criterion hybrid filter, SMOTE balancing, NPO wrapper optimization  
> with adaptive Lévy-scale anti-stagnation, and a five-member soft-voting ensemble —  
> evaluated on four GEO datasets under a dual-paradigm protocol.

</div>

---

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Datasets](#datasets)
- [Results](#results)
- [Installation](#installation)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [Citation](#citation)
- [Authors](#authors)
- [License](#license)

---

## Overview

Diagnosing Alzheimer's disease (AD) from gene expression microarray data is a high-dimensional classification problem — tens of thousands of probes, hundreds of samples. This project presents **HFW-NPO**, a three-stage pipeline:

| Phase | Module | Purpose |
|-------|--------|---------|
| **1 — Hybrid Filter** | `data_manager.py` | Score probes with Fisher Discriminant Ratio + Mutual Information + Welch *t*-test; aggregate via ω-score and retain top-*k* |
| **2 — SMOTE** | `main.py` | Intra-fold oversampling to handle class imbalance (leakage-free) |
| **3 — NPO Wrapper** | `npo_optimizer.py` | Binary NPO with adaptive Lévy-scale anti-stagnation and hill-climbing local search to select the minimal discriminative gene subset |
| **4 — Ensemble** | `main.py` | Soft-voting (5 members): ExtraTrees + SVM-RBF + Logistic Regression + Random Forest + Gradient Boosting |

The **ω-score** aggregates three complementary filter criteria:

```
ω(i) = 0.40 × Fisher(i)  +  0.30 × MutualInfo(i)  +  0.30 × Welch(i)
```

The evaluation follows a **dual-paradigm protocol**:
- **Scenario A** — balanced brain tissue datasets (GSE33000, GSE132903, GSE122063)
- **Scenario B** — imbalanced peripheral blood cohort (GSE63060 + GSE63061, 478 samples, ratio 1.48:1)

---

## Architecture

```
Raw GEO Data (Illumina HumanHT-12 microarray)
           │
           ▼
┌─────────────────────────────────────────────────────────┐
│  Stage 1 · Hybrid Filter  (fitted on TRAIN only)        │
│                                                         │
│  ω = 0.40 × Fisher + 0.30 × Mutual-Info + 0.30 × Welch │
│  → retain top-k probes (k = 80 brain; k = 15 blood)     │
└──────────────────────────┬──────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│  Stage 2 · SMOTE  (train split only, leakage-free)      │
│  k_neighbors = min(5, minority_count − 1)               │
└──────────────────────────┬──────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│  Stage 3 · Binary NPO Wrapper + Local Search            │
│                                                         │
│  Fitness: f = α·(1 − BalAcc_inner) + (1−α)·|S|/K       │
│  Adaptive Lévy-scale anti-stagnation mechanism          │
│  Hill-climbing post-processing (flip-one-bit)           │
└──────────────────────────┬──────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│  Stage 4 · 5-Member Soft-Voting Ensemble (TEST split)   │
│                                                         │
│  ExtraTrees + SVM-RBF + LR + RandomForest + GradBoost  │
│  → BalAcc · Accuracy · F1 · MCC · AUC-ROC              │
└─────────────────────────────────────────────────────────┘
    Scenario A: 5-fold Stratified CV
    Scenario B: RSKF (5 × 10) + z-score batch harmonization
```

---

## Datasets

All datasets are publicly available on [NCBI GEO](https://www.ncbi.nlm.nih.gov/geo/).

| ID | GEO Accession | Tissue | Condition | Platform | Scenario |
|----|--------------|--------|-----------|----------|---------|
| D1 | [GSE33000](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE33000) | Prefrontal cortex | AD vs Control | Illumina HumanHT-12 | A |
| D2 | [GSE132903](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE132903) | Middle temporal gyrus | AD vs Control | Illumina HumanHT-12 | A |
| D3 | [GSE63060](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE63060) + [GSE63061](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE63061) | Peripheral blood | AD+MCI vs Control (478 samples) | Illumina HumanHT-12 | B |
| D4 | [GSE122063](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE122063) | iPSC-derived neurons | AD vs Control | Illumina HumanHT-12 | A |

> **Note:** Raw data files are not included in this repository due to size and redistribution restrictions.  
> Download each dataset directly from the GEO links above and place them in `dataset/`.

---

## Results

### Scenario A — Balanced Brain Tissue (5-fold Stratified CV)

| Dataset | Balanced Acc | AUC-ROC | Probes Selected |
|---------|:-----------:|:-------:|:--------------:|
| GSE33000 (D1) | **85.28 ± 4.21%** | **0.93** | 29–32 (range: 24–38) |
| GSE132903 (D2) | **87.16 ± 3.68%** | **0.95** | 29–32 (range: 24–38) |
| GSE122063 (D4) | **96.67 ± 4.56%** | **0.99** | — |

HFW-NPO outperformed **13 published methods** on Scenario A datasets.

### Scenario B — Imbalanced Peripheral Blood (RSKF 5×10)

| Dataset | Balanced Acc | MCI Recall |
|---------|:-----------:|:----------:|
| GSE63060 + GSE63061 (478 samples, 1.48:1) | **59.53%** | **63.50 ± 14.02%** |

> Scenario B establishes the first reproducible baseline for transcriptomic-based AD classification in peripheral blood. The 59.53% balanced accuracy does not yet reach clinically actionable levels, and cross-platform validation on RNA-seq cohorts is identified as a priority future extension.

---

## Installation

```bash
# 1. Clone the repository
git clone https://github.com/alwhelat/HFW-EBNPO-Alzheimer-Gene-Classification.git
cd HFW-EBNPO-Alzheimer-Gene-Classification

# 2. (Recommended) Create a virtual environment
python -m venv .venv
source .venv/bin/activate        # Linux / macOS
# .venv\Scripts\activate         # Windows

# 3. Install dependencies
pip install -r requirements.txt
```

### Requirements

| Package | Version | Purpose |
|---------|---------|---------|
| `numpy` | 2.3.1 | Array operations |
| `pandas` | 2.3.1 | Data loading & manipulation |
| `scipy` | ≥ 1.11 | Statistical tests |
| `scikit-learn` | 1.7.0 | Classifiers, CV, metrics |
| `imbalanced-learn` | 0.14.1 | SMOTE oversampling |
| `matplotlib` | ≥ 3.7 | Figures & plots |
| `seaborn` | ≥ 0.12 | Statistical visualisations |
| `statsmodels` | ≥ 0.14 | Wilcoxon / Friedman tests |
| `gprofiler-official` | ≥ 1.0 | GO/KEGG enrichment |

---

## Usage

### Run main pipeline (D1 — GSE33000 & D2 — GSE132903)

```bash
python main.py
```

### Run D4 — GSE122063 (iPSC-derived neurons)

```bash
python main_data4.py
```

### Run full analysis suite (ablation, metrics, statistics, figures)

```bash
python run_all_analysis.py
```

### Individual modules

```bash
# Ablation study (filter component contribution)
python ablation_study.py

# Extended metrics (MCC, Kappa, sensitivity, specificity)
python metrics_extended.py

# Statistical significance tests (Wilcoxon signed-rank, Friedman)
python statistical_tests.py

# GO/KEGG/Reactome enrichment figures
python make_enrichment_figures.py

# Gene stability & biomarker frequency analysis
python gene_analysis.py

# Illumina probe → HGNC gene symbol mapping
python ilmn_to_genesymbol.py
```

---

## Project Structure

```
HFW-EBNPO-Alzheimer-Gene-Classification/
│
├── main.py                        ← Pipeline entry point (D1 / D2)
├── main_data4.py                  ← Pipeline entry point (D4 — GSE122063)
├── npo_optimizer.py               ← Binary NPO + local search
├── data_manager.py                ← Dataset loading & hybrid filter (ω-score)
├── data_loader_new.py             ← Alternative GEO data loader
├── gene_analysis.py               ← Gene stability & biomarker frequency
├── ablation_study.py              ← Ablation experiments
├── metrics_extended.py            ← Extended evaluation metrics (MCC, Kappa)
├── statistical_tests.py           ← Wilcoxon / Friedman significance tests
├── make_enrichment_figures.py     ← GO / KEGG / Reactome enrichment figures
├── ilmn_to_genesymbol.py          ← Illumina probe → gene symbol mapping
├── run_all_analysis.py            ← Master runner (all modules)
│
├── dataset/                       ← GEO datasets (download separately)
│   ├── Data1(GSE33000).csv
│   ├── GSE122063_series_matrix.txt.gz
│   ├── GSE63060_series_matrix.txt.gz
│   ├── GSE63061_series_matrix.txt.gz
│   └── GSE132903_RAW/
│
├── results/                       ← Experiment outputs
│   ├── Data1_GSE33000/
│   ├── Data2_GSE132903/
│   ├── gene_symbols_enrichment/   ← GO/KEGG enrichment figures & tables
│   ├── unified_project_results.json
│   ├── statistical_report.json
│   └── gene_analysis.json
│
├── figures/                       ← Publication-ready figures (300 dpi)
│
├── requirements.txt               ← Python dependencies
├── CITATION.cff                   ← Machine-readable citation
├── LICENSE                        ← CC-BY-3.0 License
└── README.md
```

---

## Citation

If you use this code or results in your research, please cite:

```bibtex
@article{abiyev2026hfwnpo,
  title     = {HFW-NPO: A Dual Paradigm Hybrid Filter--Wrapper Nomadic People Optimizer
               Framework for High-Dimensional {Alzheimer's} Gene Expression Classification},
  author    = {Abiyev, Rahib H. and Alwhelat, Almuntadher},
  journal   = {Electronics},
  publisher = {MDPI},
  volume    = {15},
  number    = {13},
  pages     = {2970},
  year      = {2026},
  month     = {7},
  doi       = {10.3390/electronics15132970},
  url       = {https://www.mdpi.com/2079-9292/15/13/2970},
  issn      = {2079-9292}
}
```

A `CITATION.cff` file is also provided for automated citation tools (Zenodo, GitHub "Cite this repository").

---

## Authors

**Prof. Rahib H. Abiyev**  
Near East University, Nicosia, North Cyprus

**Almuntadher Alwhelat**  
Near East University / Al-Farabi University College, Baghdad, Iraq  
📧 [almuntadher.mahmood@alfarabiuc.edu.iq](mailto:almuntadher.mahmood@alfarabiuc.edu.iq)  
🔗 [github.com/alwhelat](https://github.com/alwhelat)

---

## License

This project is licensed under the **Creative Commons Attribution 3.0** license — see [LICENSE](LICENSE) for details.

---

<div align="center">
<sub>MDPI Electronics 2026 · Alzheimer's Disease Gene Expression Research · DOI: 10.3390/electronics15132970</sub>
</div>
