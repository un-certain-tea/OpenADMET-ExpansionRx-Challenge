# OpenADMET-ExpansionRx-Challenge

This repository contains my final submission for the **OpenADMET ExpansionRx Challenge**.  
The final predictions are an **average (ensemble)** of several modeling strategies explored throughout the challenge.

## Overview of Modeling Approaches

The ensemble consists of predictions from the following approaches:

- **Chemprop** multitask neural network
- **TabPFN** model trained on a single shared feature set across all endpoints
- **TabPFN** model trained with endpoint-specific feature sets

---

## Feature Sets

The following molecular feature representations were used across different models:

### RDKit
- 2D physicochemical descriptors  
- ECFP4 fingerprints (binary and count)  
- 3D descriptors: WHIM, GETAWAY, RDF, MORSE, AUTOCORR3D  
- Conformer generation using `nvmolkit`  
- Avalon substructure fingerprints  
- Extended Reduced Graph (ERG) fingerprints  

### Jazzy
- Interaction energy descriptors:
  - Δ interaction energy  
  - Δ polar / apolar contributions  
- Repository: https://github.com/AstraZeneca/jazzy

### ChemAxon
- pKa, logP, logD  
- Hydrogen bond donors / acceptors (H/L/B)  
- Python API: https://docs.chemaxon.com/display/docs/python-api_index.md

### CheMeleon
- 2048-bit learned molecular embeddings  
- Repository: https://github.com/JacksonBurns/chemeleon

### Minimol
- 512-bit molecular embeddings  
- Repository: https://github.com/graphcore-research/minimol

### Giammy
- Publicly available pretrained ADMET models  
- Used as additional feature generators

---

## Modeling Strategy 1: Chemprop Multitask Training

- Multitask Chemprop model trained jointly on all endpoints
- Predictions from this model were included as part of the final ensemble

---

## Modeling Strategy 2: TabPFN (Single Feature Set)

- **Algorithm:** `TabPFNRegressor` (TabPFN v2.5)
- **Feature set:**  
  - RDKit 2D descriptors  
  - CheMeleon embeddings  
  - ChemAxon descriptors  
  - Giammy features  

### Feature Reduction
- Dropped constant features  
- Removed highly correlated features (Pearson correlation > 0.85)  
- Ensured the total feature count remained below the 2000-feature limit of TabPFN v2.5  

---

## Modeling Strategy 3: TabPFN (Endpoint-Specific Feature Sets)

- **Algorithm:** `TabPFNRegressor` (TabPFN v2.5)
- **Feature selection approach:**
  - For each endpoint, multiple feature-set combinations were evaluated using 5-fold cross-validation
  - Due to the 2000-feature limitation of TabPFN v2.5, 2048-bit ECFP4 fingerprints and 2048-bit CheMeleon embeddings were initially excluded

### Final Feature Set per Endpoint

- **Caco2_Efflux:** ChemAxon + ERG + Jazzy + Minimol  
- **Caco2_Papp_AB:** Avalon + ChemAxon + ERG + Jazzy + Minimol + RDKit  
- **HLM_CLint:** Avalon + ChemAxon + ERG + Jazzy + Minimol + RDKit  
- **KSOL:** Avalon + ChemAxon + ERG + Jazzy + Minimol + RDKit  
- **LogD:** Avalon + ChemAxon + ERG + Jazzy + Minimol + RDKit  
- **MBPB:** Avalon + ChemAxon + ERG + Jazzy + Minimol + RDKit  
- **MGMB:** Avalon + ChemAxon + ERG + Minimol  
- **MLM_CLint:** ChemAxon + Minimol + RDKit  
- **MPPB:** ChemAxon + Minimol + RDKit  

### Feature Reduction
- Dropped constant features  
- Removed highly correlated features (Pearson correlation > 0.95)  
- If the feature count was still excessive, selected the top 1999 features by variance  

---

## Approaches That Did Not Work Well

The following ideas were explored but did not lead to performance improvements:

- Training models on public ADMET datasets and using their predictions as additional features  
- Delta learning using public ADMET data  
- Fine-tuning TabPFN  
- Extensive experimentation with AutoGluon ensemble models using extreme/best presets  
