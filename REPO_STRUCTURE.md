# Repository Structure — HFW-NPO

> **HFW-NPO: A Dual Paradigm Hybrid Filter–Wrapper Nomadic People Optimizer Framework for High-Dimensional Alzheimer's Gene Expression Classification**
> Published in **MDPI Electronics**, Vol. 15, No. 13, Article 2970 (July 2026)
> DOI: [10.3390/electronics15132970](https://doi.org/10.3390/electronics15132970)

---

## Top-Level Layout

```
hfw-npo/
│
├── Core Pipeline
│   ├── main.py                        # Entry point: GSE33000, GSE132903 datasets
│   ├── main_data4.py                  # Entry point: GSE122063 (iPSC neurons)
│   ├── npo_optimizer.py               # Binary NPO wrapper + local search
│   ├── hybrid_filter.py               # Hybrid filter: Welch t-test + MI + Fisher
│   ├── fitness_evaluator.py           # Ensemble fitness function (5-member voting)
│   ├── data_manager.py                # Dataset loading, z-score normalization
│   └── data_loader_new.py             # Alternative GEO loader
│
├── Analysis & Figures
│   ├── gene_analysis.py               # Gene stability & biomarker frequency
│   ├── ablation_study.py              # Ablation experiments (filter components)
│   ├── metrics_extended.py            # MCC, Kappa, sensitivity, specificity
│   ├── statistical_tests.py           # Wilcoxon signed-rank & Friedman tests + figures
│   ├── npo_convergence_comparison.py  # NPO convergence vs. baseline optimizers
│   ├── make_enrichment_figures.py     # GO/KEGG/Reactome enrichment bubble plots
│   └── run_all_analysis.py            # Orchestrator: runs all analyses sequentially
│
├── Supplementary & Tables
│   ├── generate_tables.py             # Generates all manuscript tables (CSV)
│   ├── generate_supplementary_batch.py
│   ├── generate_supplementary_omega.py
│   └── generate_weight_sensitivity.py # Omega-weight sensitivity analysis
│
├── Utilities
│   └── ilmn_to_genesymbol.py          # Illumina probe ID → HGNC gene symbol
│
├── results/                           # Auto-generated experiment outputs
│   ├── Data1_GSE33000/
│   │   ├── fold_results/
│   │   └── selected_genes/
│   ├── Data2_GSE132903/
│   │   ├── fold_results/
│   │   └── selected_genes/
│   ├── gene_symbols_enrichment/       # GO/KEGG enrichment results
│   ├── statistical_report.json
│   ├── gene_analysis.json
│   └── unified_project_results.json   # Master results file (all datasets)
│
├── results_data_4/                    # GSE122063 experiment outputs
│   ├── fold_results/
│   └── selected_genes/
│
├── figures/                           # Publication-ready figures (PNG, 300 dpi)
│   ├── fig_confusion_matrices.png     # Figure 3: Confusion matrices
│   ├── fig_extended_metrics.png       # Figure 4: Extended metrics (MCC, Kappa)
│   ├── fig_ablation.png               # Figure 5: Ablation study
│   ├── fig_gene_stability.png         # Figure 6: Gene stability heatmap
│   ├── fig_statistical_tests.png      # Figure 7: Wilcoxon & Friedman validation
│   ├── fig_convergence_comparison.png # Figure 12: NPO convergence comparison
│   └── fig_venn_genes.png             # Figure 11: Venn diagram (gene overlap)
│
├── table/                             # Summary tables (CSV format)
│
├── supplementary/                     # Supplementary figures and tables
│
├── README.md                          # Project overview (this file's companion)
├── REPO_STRUCTURE.md                  # This file
├── .gitignore
└── requirements.txt
```

---

## Data Flow

```
NCBI GEO (raw .soft / .gz files)
         │
         ▼
data_manager.py / data_loader_new.py
  ├─ z-score normalization
  ├─ probe → gene symbol mapping (ilmn_to_genesymbol.py)
  └─ peripheral blood: batch harmonisation
         │
         ▼
main.py  /  main_data4.py
  └─ 5-fold stratified cross-validation loop
       │
       ├── [Per fold — train split only]
       │     hybrid_filter.py  →  top-k genes (ω-score)
       │     SMOTE             →  balanced train set
       │     npo_optimizer.py  →  binary NPO + local search
       │     fitness_evaluator.py → 5-member soft-voting ensemble
       │
       └── [Test split] → BalAcc, AUC, MCC, Kappa, confusion matrix
                │
                ▼
         results/unified_project_results.json
```

---

## Key Output Files

| File | Description |
|------|-------------|
| `results/unified_project_results.json` | All performance metrics across datasets and folds |
| `results/statistical_report.json` | Wilcoxon and Friedman test p-values vs. baselines |
| `results/gene_analysis.json` | Per-gene selection frequency (all folds, all datasets) |
| `results/gene_symbols_enrichment/` | g:Profiler GO/KEGG/Reactome enrichment outputs |

---

## Datasets

Data files are **NOT included** in the repository (GEO redistribution policy).

| Dataset | GEO Accession | Tissue | Samples | Usage |
|---------|--------------|--------|---------|-------|
| D1 | [GSE33000](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE33000) | Prefrontal cortex | 74 | Main results |
| D2 | [GSE132903](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE132903) | Middle temporal gyrus | 125 | Main results |
| D3 | [GSE63060](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE63060) + [GSE63061](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE63061) | Peripheral blood | 478 | Transferability |
| D4 | [GSE122063](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE122063) | iPSC-derived neurons | 52 | Sensitivity analysis |

Download raw data from NCBI GEO and place under a `data/` directory (see `data_manager.py` for expected paths).

---

## Environment

```
Python  3.13
numpy   2.3.1
pandas  2.3.1
scikit-learn      1.7.0
imbalanced-learn  0.14.1
scipy, matplotlib, seaborn, statsmodels, gprofiler-official
```

Install with: `pip install -r requirements.txt`
