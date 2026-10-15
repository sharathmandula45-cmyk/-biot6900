# BIOT 6900 – Module 1: Computational Environment Setup & Tool Ecosystem

**Name:** Sharath
**Notebook:** `module1_setup.ipynb`

## What I did
- **Part A – Environment:** set up the `biot6900` conda environment (Python 3.11) with BioPython, pandas, numpy, requests and Jupyter, plus Git/GitHub for version control.
- **Part B – BioPython quickstart:** translated a short DNA sequence into protein with `Seq.translate()`.
- **Part C – Database APIs:**
  - **PubMed** (via `Bio.Entrez`): searched "AlphaFold protein structure" and retrieved 5 PMIDs.
  - **UniProt** (REST API): retrieved P04637, the human tumour suppressor p53.
  - **PDB** (RCSB REST API): retrieved 1TUP, a p53 core domain structure bound to DNA.
  - **GWAS Catalog** (EBI REST API): looked up rs7412, an APOE missense variant that defines the ε2 allele.
- **Part D – Multi-omics datasets:** deferred to Week 2, as confirmed by Prof. Prabakaran by email (Sep 14).

## Problems and fixes
- Part D's loading cell raises `FileNotFoundError` because no dataset was assigned for Week 1; this was expected (see above).
