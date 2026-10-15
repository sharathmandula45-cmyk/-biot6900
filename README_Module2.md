# BIOT 6900 – Module 2, Part 3: Alzheimer's Disease Multi-Omics Integration

**Name:** Sharath
**Disease:** Alzheimer's disease (AD)

## Files
- `BIOT6900_Module2_Starter.ipynb` – completed notebook (Part 3, cells 3.1–3.5)
- `targets_ad.csv` – final ranked gene table exported from 3.5 (hand-off to Module 3)
- `Module2_Part3_Report.pdf` – 3.6 interpretation write-up

## Data sources
- **Transcriptomics:** GEO2R differential expression on **GSE5281** (hippocampus, AD vs. control)
- **Proteomics:** Supplementary Table 2 from Johnson et al. (2022), *Nature Neuroscience* – TMT-MS study of the ROSMAP and Banner cohorts
- **Genomics:** GWAS Catalog, filtered to Alzheimer's disease associations

## Pipeline summary
Harmonise on gene symbol → split multi-gene GWAS entries → collapse each source to one row per gene → inner-join all three → sign-agreement concordance → rank-normalised, equal-weighted multi-evidence score (`multi_evidence_score`) → rank and export.

**Result:** 1,139 genes survived the three-way join; 560 were sign-concordant between RNA and protein.
Top hits: SAMHD1, PDZD2, ZMYM2, APOE, ASAP1, CACNG8, COL11A1, MAPT, APOC1, RHOJ, NDUFA10, WNK2, CLU, FARP2, NUMA1.

## Problems I ran into and how I fixed them
1. **GEO2R group mis-assignment.** I first assigned the 13 hippocampus control samples to the AD group. I caught this by checking the group labels before running the analysis and re-assigned them correctly.
2. **Unusable proteomics file.** My first download (a PRIDE glycoproteomics file) was peptide-level data, not a gene-level AD vs. control comparison. I switched to Supplementary Table 2 from Johnson et al. (2022), which has gene symbol, fold change and adjusted p-value.
3. **Wrong working directory.** `load_ad()` raised `FileNotFoundError` because Jupyter was running from another folder. Fixed by launching Jupyter from the repo folder (no hard-coded path in the notebook).
4. **Double file extensions.** Windows renamed files to `ad_gwas.tsv.tsv` etc. Fixed by correcting the file names.
5. **Column name mismatches.** My real columns were `Gene.symbol` / `logFC` / `adj.P.Val` and `MAPPED_GENE` / `P-VALUE`, so I checked `.columns` first and renamed accordingly.
6. **Zero p-values.** Some GWAS p-values were exactly 0 (floating-point underflow), giving infinite scores. I floored them to the smallest non-zero p-value in the data before taking −log10.
7. **Duplicate rows per gene.** I collapsed each table to one row per gene (smallest p-value) before merging.
8. **GWAS dominating the score.** My first score was a raw weighted sum, but −log10 GWAS p-values go up to ~300 while fold changes are ~0–4, so the ranking was basically a GWAS ranking. Fixed by using `multi_evidence_score`, which rank-normalises each layer to 0–1 before equal weighting.
9. **Multi-gene GWAS entries lost in the join.** GWAS Catalog often lists several genes in one cell (e.g. "TOMM40, APOE"). I split these so each gene gets its own row. The join grew from 731 to 1,139 genes.

## Sanity check
APOE (the strongest known genetic risk factor for late-onset AD) is in the top 5, and MAPT (tau), APOC1 and CLU are also in the top 15.
