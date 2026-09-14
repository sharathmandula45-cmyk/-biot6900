# BIOT 6900 — Module 1: Computational Environment Setup

**Name:** Sharath Mandula

## What's in this repo
- `module1_setup.ipynb` — completed notebook: environment verification, BioPython quickstart, four database queries (PubMed, UniProt, PDB, GWAS Catalog), and the assigned sample dataset download.

## Environment
- Python 3.11, conda env `biot6900`
- Key packages: biopython, requests, pandas

## What didn't work / notes for the grader
- (Fill this in after you run the notebook top to bottom — e.g. "Everything ran cleanly" or "Stuck on X, here's what I tried.")

## How to reproduce
```
conda create -n biot6900 python=3.11
conda activate biot6900
pip install biopython requests pandas jupyter
jupyter notebook module1_setup.ipynb
```
Run **Kernel → Restart & Run All** before committing to confirm it executes clean top to bottom.
