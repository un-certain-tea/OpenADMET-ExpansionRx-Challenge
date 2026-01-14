# OpenADMET-ExpansionRx-Challenge

Final predictions were generated using the winsorized mean over multiple modeling strategies:

- A **Chemprop multitask** neural network
- **TabPFN** models trained on a single shared feature set
- **TabPFN** models trained with endpoint-specific feature sets

---

## Features

Multiple molecular representations were used:
- **RDKit:** 2D descriptors, ECFP4 (binary & count), 3D descriptors, Avalon, ERG fingerprints
- **ChemAxon:** pKa, logP, logD, H/L/B descriptors
- **Jazzy:** Interaction energy descriptors
- **CheMeleon:** 2048-bit molecular embeddings
- **Minimol:** 512-bit molecular embeddings
- **AZ-pred:** Predictions from Chemprop MT model published by AZ (https://pubs.acs.org/doi/10.1021/acsomega.5c04861)

---

## Models

### Chemprop
- Multitask training across all endpoints
- Included directly in the final ensemble

### TabPFN (Single Feature Set)
- **Algorithm:** `TabPFNRegressor` (v2.5)
- Feature set: RDKit + CheMeleon + ChemAxon + AZ-pred
- Feature reduction:
  - Drop constant features
  - Remove correlated features (Pearson > 0.85). Cutoff was chosen to stay <2000 features (TabPFN 2.5 limit)

### TabPFN (Endpoint-Specific)
- **Algorithm:** `TabPFNRegressor` (v2.5)
- Feature sets selected per endpoint via 5-fold CV
- 2048-bit ECFP4 and CheMeleon excluded due to feature limits in TabPFN 2.5

**Final feature sets:**
- Caco2_Efflux: ChemAxon + ERG + Jazzy + Minimol
- Caco2_Papp_AB: Avalon + ChemAxon + ERG + Jazzy + Minimol + RDKit
- HLM_CLint: Avalon + ChemAxon + ERG + Jazzy + Minimol + RDKit
- KSOL: Avalon + ChemAxon + ERG + Jazzy + Minimol + RDKit
- LogD: Avalon + ChemAxon + ERG + Jazzy + Minimol + RDKit
- MBPB: Avalon + ChemAxon + ERG + Jazzy + Minimol + RDKit
- MGMB: Avalon + ChemAxon + ERG + Minimol
- MLM_CLint: ChemAxon + Minimol + RDKit
- MPPB: ChemAxon + Minimol + RDKit

---

## Notes

The following approaches were explored but did not improve performance:
- Using predictions from public ADMET models as features
- Delta learning with public ADMET data
- Fine-tuning TabPFN
- AutoGluon ensemble models

