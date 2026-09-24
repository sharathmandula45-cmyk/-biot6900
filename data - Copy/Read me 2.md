# BIOT 6900 — Module 2, Part 3: Alzheimer's Disease Multi-Omics Integration

**Author:** Sharath
**Course:** BIOT 6900, AI-Powered Biotech Workflows: From Discovery to Clinic (Fall 2026)
**Assignment:** Required Module 2 assignment — gene-level integration of Alzheimer's transcriptomic, proteomic, and genomic evidence

## What's in this submission

- `BIOT6900_Module2_Starter.ipynb` — completed notebook (Part 3, cells 3.1–3.5)
- `targets_ad.csv` — final ranked gene table exported from 3.5
- `Module2_Part3_Report.md` — 3.6 interpretation write-up (weighting, top targets, discordant gene, limitation)
- `README.md` — this file

## Data sources

Since this assignment required self-sourced data rather than a pre-matched cohort, I pulled each layer from a different place:

- **Transcriptomics:** GEO2R differential expression on **GSE5281** (hippocampus, AD vs. control, Alzheimer's Disease and the Normal Aged Brain)
- **Proteomics:** Supplementary Table 2 from Johnson et al. 2022, *Nature Neuroscience* ("Large-scale deep multi-layer analysis of Alzheimer's disease brain..."), a TMT-MS study of the ROSMAP + Banner cohorts
- **Genomics:** GWAS Catalog, filtered to Alzheimer's disease associations

## Pipeline summary

Harmonize on gene symbol → collapse each source to one row per gene → inner-join all three → sign-agreement concordance → equal-weighted multi-evidence score → rank and export top hits. Final joined table: 731 genes. Full results and interpretation are in `Module2_Part3_Report.md`.

## Problems I ran into and how I fixed them

**1. GEO2R group mis-assignment.** When first defining sample groups in GEO2R, I accidentally assigned the 13 `HIP control` hippocampus samples to the **AD** group instead of **Control**. Caught this by checking the left-hand group labels in the sample table before running the analysis, un-assigned them, and re-assigned them correctly to Control. Lesson: always visually confirm the group label column before clicking "Top 250."

**2. Sourcing usable proteomics data.** My first attempt at getting proteomics data was downloading a raw glycoproteomics results file (`Ngly_human.xlsx`) from a PRIDE dataset (PXD075438). This turned out to be peptide-level data (individual peptide sequences, glycan compositions, per-sample "Peak Found" calls) rather than a clean gene-level AD-vs-control comparison — not usable without a lot of additional processing outside the scope of this assignment. I switched to using **Supplementary Table 2** from a published TMT proteomics paper (Johnson et al. 2022), which already had gene symbol, fold-change, and adjusted p-value in the same shape as my GEO2R transcriptomics output.

**3. File path / working directory mismatch.** After downloading and renaming my three data files, `load_ad()` kept raising `FileNotFoundError` even though the files existed. It turned out Jupyter's working directory was `C:\Users\BHARATH\Downloads`, not the `biot6900` project folder where my `data/` folder with the correct files actually lived. Fixed by checking `os.getcwd()` and using `os.chdir()` to point to the correct project folder.

**4. Double file extensions.** Related to the above — when renaming downloaded files to `ad_gwas.tsv`, `ad_proteomics.tsv`, and `ad_transcriptomics.tsv`, Windows silently appended `.tsv` to files that already had a visible `.tsv` extension in the rename box, producing `ad_gwas.tsv.tsv` etc. `load_ad()` couldn't find files with the exact names it expected until I corrected the double extensions.

**5. Column name mismatches with the notebook's hint code.** The starter notebook's hint comments used generic placeholder column names (`log2FC`, `pval`) that didn't match any of my actual files. My real column names were `Gene.symbol` / `logFC` / `adj.P.Val` (transcriptomics and proteomics) and `MAPPED_GENE` / `P-VALUE` (GWAS Catalog export). I had to inspect each table's actual `.columns` output first and adjust the rename step accordingly.

**6. Divide-by-zero in the genomic score.** A handful of GWAS Catalog p-values were recorded as exactly `0` (floating-point underflow rather than a true zero), which made `-log10(0)` return `inf` and broke the ranking (APOE's score showed as `inf`). Fixed by flooring all zero p-values to the smallest nonzero p-value observed in the dataset before taking the log — a standard, defensible way to handle underflowed p-values. This is also noted in the interpretation report.

**7. Duplicate rows per gene skewing the top-15.** My first successful join produced a top-15 list that was almost entirely repeated `APOE`/`APOC1` rows, because the proteomics table has multiple isoform/peptide rows per gene and the GWAS table has multiple SNP rows per gene. Fixed by collapsing each source table to a single row per gene (keeping the row with the smallest p-value) *before* merging, using `groupby("gene")[p_col].idxmin()`. This dropped the joined table from 7,506 rows to 731 genuinely one-row-per-gene entries and produced a properly diverse top-15.

## Sanity check

APOE ranked #1 by score, with APOC1 and TOMM40 close behind — expected, since these three genes sit in tight linkage disequilibrium on chromosome 19 near the APOE locus, the strongest known genetic risk factor for late-onset Alzheimer's disease. ABCA7 and CLU (both well-established AD risk genes) also appear in the top 10.
