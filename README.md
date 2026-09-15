# BIOT 6900 — Module 1: Computational Environment Setup

**Name:** Sharath Mandula
Instructor: Prof. Sudhakaran Prabakaran
Course: BIOT 6900 — AI-Powered Biotech Workflows: From Discovery to Clinic


## What's in this repo
- `module1_setup.ipynb` 
— completed notebook: environment verification, BioPython quickstart, four database queries (PubMed, UniProt, PDB, GWAS Catalog), and the assigned sample dataset download.

## Environment
- Python 3.11, conda env `biot6900`
- Key packages: biopython, requests, pandas, Jupyter

## What didn't work / notes for the grader
- Parts A–C ran clean top to bottom, no errors.
- Part D (sample dataset download/load) is next week's material — confirmed with Prof. Prabakaran that it's not expected for this submission.

## How to reproduce
```
conda create -n biot6900 python=3.11
conda activate biot6900
pip install biopython requests pandas jupyter
jupyter notebook module1_setup.ipynb
```
Run **Kernel → Restart & Run All** before committing to confirm it executes clean top to bottom.
