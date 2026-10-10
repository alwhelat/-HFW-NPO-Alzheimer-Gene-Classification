# HFW-NPO: A Dual-Paradigm Hybrid Filter-Wrapper Framework for Alzheimer's Disease Classification

> **Published in MDPI Electronics — Volume 15, Issue 13, Article 2970 (July 2026)**
> DOI: [10.3390/electronics15132970](https://doi.org/10.3390/electronics15132970)

[![Python](https://img.shields.io/badge/Python-3.13-blue)](https://www.python.org/)
[![scikit-learn](https://img.shields.io/badge/scikit--learn-1.7.0-orange)](https://scikit-learn.org/)
[![License](https://img.shields.io/badge/License-CC--BY--3.0-green)](https://creativecommons.org/licenses/by/3.0/)
[![Journal](https://img.shields.io/badge/MDPI-Electronics-blue)](https://www.mdpi.com/2079-9292/15/13/2970)

---

## Overview

**HFW-NPO** (Hybrid Filter-Wrapper Nomadic People Optimizer) is a dual-paradigm feature selection framework designed for high-dimensional Alzheimer's disease gene expression classification. The framework integrates:

1. **Hybrid Filter Stage** — combines three complementary statistical filters:
   - Welch's t-test (parametric group difference)
   - Mutual Information (MI) (non-linear dependency)
   - Fisher Discriminant Ratio (class separability)
   - Scores aggregated via a weighted omega-score: `ω = 0.40·F + 0.30·MI + 0.30·W`

2. **NPO Wrapper Stage** — Binary Nomadic People Optimizer with local search refinement for optimal gene subset selection

3. **SMOTE Balancing** — Synthetic Minority Over-sampling Technique applied within each cross-validation fold (leakage-free)

4. **5-Member Soft-Voting Ensemble** — ExtraTrees + SVM-RBF + Logistic Regression + Random Forest + Gradient Boosting

---

## Results

| Dataset | Description | Balanced Accuracy | AUC-ROC |
|---------|-------------|:-----------------:|:-------:|
| GSE33000  | Brain tissue — Alzheimer's (n=74)   | **85.28 ± 4.21%** | 0.93 |
| GSE132903 | Brain tissue — Alzheimer's (n=125)  | **87.16 ± 3.68%** | 0.95 |
| GSE122063 | iPSC-derived neurons (n=52)         | **96.67 ± 4.56%** | 0.99 |
| GSE63060+GSE63061 | Peripheral blood (n=478) | **59.53 ± 4.21%** | — |

*5-fold stratified cross-validation; mean ± std reported*

---

## Datasets

| ID | GEO Accession | Platform | Description | Samples |
|----|--------------|----------|-------------|---------|
| D1 | GSE33000   | GPL6947  | Brain — prefrontal cortex | 74 (AD vs. control) |
| D2 | GSE132903  | GPL10558 | Brain — middle temporal gyrus | 125 (AD vs. control) |
| D3 | GSE63060 + GSE63061 | GPL6947 | Peripheral blood | 478 (AD+MCI vs. control) |
| D4 | GSE122063  | — | iPSC-derived neurons | 52 (AD vs. control) |

> Raw GEO data are not included in this repository due to redistribution restrictions.
> Download directly from [NCBI GEO](https://www.ncbi.nlm.nih.gov/geo/) using the accession numbers above.

---

## Project Structure

```
hfw-npo/
│
├── main.py                        # Pipeline entry point (GSE33000, GSE132903)
├── main_data4.py                  # Pipeline entry point (GSE122063)
├── npo_optimizer.py               # Core binary NPO wrapper optimizer
├── hybrid_filter.py               # Hybrid filter stage (Welch + MI + Fisher)
├── fitness_evaluator.py           # Classification fitness function
├── data_manager.py                # Dataset loading & preprocessing
├── data_loader_new.py             # Alternative GEO data loader
├── gene_analysis.py               # Gene stability & biomarker analysis
├── ablation_study.py              # Ablation experiments
├── metrics_extended.py            # Extended evaluation metrics (MCC, Kappa, etc.)
├── statistical_tests.py           # Wilcoxon signed-rank & Friedman tests
├── npo_convergence_comparison.py  # Convergence behavior plots
├── run_all_analysis.py            # Run full analysis pipeline
├── generate_tables.py             # Table generation
├── make_enrichment_figures.py     # GO/KEGG/Reactome pathway enrichment figures
├── generate_supplementary_batch.py
├── generate_supplementary_omega.py
├── generate_weight_sensitivity.py
├── ilmn_to_genesymbol.py          # Illumina probe ID → HGNC gene symbol mapping
├── statistical_tests.py           # Statistical validation figures
│
├── results/                       # Experiment outputs
│   ├── Data1_GSE33000/
│   ├── Data2_GSE132903/
│   ├── gene_symbols_enrichment/   # GO enrichment outputs
│   └── unified_project_results.json
│
├── results_data_4/                # GSE122063 outputs
│
├── figures/                       # Publication-ready figures
│   ├── fig_confusion_matrices.png
│   ├── fig_extended_metrics.png
│   ├── fig_convergence_comparison.png
│   ├── fig_ablation.png
│   ├── fig_gene_stability.png
│   ├── fig_venn_genes.png
│   └── fig_statistical_tests.png
│
├── table/                         # Summary tables (CSV)
├── supplementary/                 # Supplementary figures and tables
│
├── README.md
├── .gitignore
└── requirements.txt
```

---

## Installation

```bash
git clone https://github.com/alwhelat/-HFW-NPO-Alzheimer-Gene-Classification.git
cd -HFW-NPO-Alzheimer-Gene-Classification
pip install -r requirements.txt
```

### Dependencies

```
python==3.13
numpy==2.3.1
pandas==2.3.1
scikit-learn==1.7.0
imbalanced-learn==0.14.1
scipy
matplotlib
seaborn
statsmodels
gprofiler-official
```

---

## Usage

**Run main pipeline (GSE33000 & GSE132903):**
```bash
python main.py
```

**Run GSE122063 (iPSC-derived neurons):**
```bash
python main_data4.py
```

**Run full analysis (ablation, metrics, statistics, figures):**
```bash
python run_all_analysis.py
```

**Generate pathway enrichment figures:**
```bash
python make_enrichment_figures.py
```

**Run gene stability and biomarker analysis:**
```bash
python gene_analysis.py
```

---

## Key Outputs

| File | Description |
|------|-------------|
| `results/unified_project_results.json` | Cross-dataset accuracy, BalAcc, AUC, selected gene counts |
| `results/statistical_report.json` | Wilcoxon signed-rank & Friedman test results |
| `results/gene_analysis.json` | Gene selection frequency & stability analysis |
| `figures/fig_gene_stability.png` | Top-20 biomarkers per dataset (selection frequency) |
| `figures/fig_venn_genes.png` | Gene union set overlap across datasets |
| `figures/fig_statistical_tests.png` | Statistical validation vs. baseline methods |
| `results/gene_symbols_enrichment/` | GO/KEGG/Reactome pathway enrichment outputs |

---

## Methodology Summary

```
GEO Data
   │
   ▼
Preprocessing (z-score normalization, batch harmonisation for peripheral blood)
   │
   ▼
5-Fold Stratified Cross-Validation
   │
   ├── [Train fold]
   │       │
   │       ▼
   │   Hybrid Filter (ω-score: Welch + MI + Fisher)
   │       │  Top-k probes (k=80 brain; k=15 blood)
   │       ▼
   │   SMOTE (class balancing within fold)
   │       │
   │       ▼
   │   NPO Wrapper + Local Search
   │       │  Optimal gene subset
   │       ▼
   │   5-Member Ensemble Training
   │
   └── [Test fold] → Balanced Accuracy, AUC, MCC, Kappa
```

---

## Citation

If you use this code or data, please cite:

**APA:**
> Alwhelat, A. M., & Abiyev, R. H. (2026). HFW-NPO: A Dual a Paradigm Hybrid Filter–Wrapper Nomadic People Optimizer Framework for High-Dimensional Alzheimer's Gene Expression Classification. *Electronics*, *15*(13), 2970. https://doi.org/10.3390/electronics15132970

**BibTeX:**
```bibtex
@article{alwhelat2026hfwnpo,
  title     = {HFW-NPO: A Dual a Paradigm Hybrid Filter--Wrapper Nomadic People Optimizer
               Framework for High-Dimensional {Alzheimer's} Gene Expression Classification},
  author    = {Alwhelat, Almuntadher M. and Abiyev, Rahib H.},
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

---

## Authors

- **Almuntadher Alwhelat** — Near East University / Al-Farabi University College, Baghdad, Iraq
  ([almuntadher.mahmood@alfarabiuc.edu.iq](mailto:almuntadher.mahmood@alfarabiuc.edu.iq))
- **Rahib H. Abiyev** — Near East University, Nicosia, North Cyprus

---

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.
