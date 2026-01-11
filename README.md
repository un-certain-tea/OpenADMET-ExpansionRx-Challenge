# OpenADMET-ExpansionRx-Challenge
Entry for the OpenADMET ExpansionRx Challenge



*   **Generated Feature sets:**
    *   **RDKit:** 2D descriptors, ECFP4 fingerprints (binary & count), 3D descriptors (WHIM, GETAWAY, RDF, MORSE, AUTOCORR3D) with `nvmolkit` for conformer generation, Avalon substructure fingerprints, Extended Reduced Graph fingerprints.
    *   **Jazzy:** Interaction energies (delta interaction, delta polar/apolar). https://github.com/AstraZeneca/jazzy
    *   **ChemAxon:** pKa, logP, logD, H/L/B. using https://docs.chemaxon.com/display/docs/python-api_index.md
    *   **CheMeleon:** 2048-bit embeddings. https://github.com/JacksonBurns/chemeleon
    *   **Minimol:** 512-bit embeddings https://github.com/graphcore-research/minimol/

 *   **Modeling Strategy:**
     *   **Algorithm:** `TabPFNRegressor` (TabPFN 2.5) https://github.com/PriorLabs/tabPFN
     *   **Feature set selection:**
         * For each endpoint, several feature set combinations were tested with a 5-fold cross validation.
         * TabPFN 2.5 has a 2000 feature limitation, so 2048-bit ECFP4 fingerprtints and 2048-bit CheMeleon embeddings were initially excluded.
         * Final selection was:
         *  "Caco2_Efflux": "ChemAxon+ERG+Jazzy+Minimol"
    *   "Caco2_Papp_AB": "Avalon+ChemAxon+ERG+Jazzy+Minimol+RDKit"
    *   "HLM_CLint": "Avalon+ChemAxon+ERG+Jazzy+Minimol+RDKit"
    *   "KSOL": "Avalon+ChemAxon+ERG+Jazzy+Minimol+RDKit"
    *   "LogD": "Avalon+ChemAxon+ERG+Jazzy+Minimol+RDKit"
    *   "MBPB": "Avalon+ChemAxon+ERG+Jazzy+Minimol+RDKit"
    *   "MGMB": "Avalon+ChemAxon+ERG+Minimol"
    *   "MLM_CLint": "ChemAxon+Minimol+RDKit"
    *   "MPPB": "ChemAxon+Minimol+RDKit"
     *   **Preprocessing:**
        *   Drops constant features.
        *   Removes highly correlated features (Pearson > 0.95) to reduce redundancy.
        *   Selects top 1999 features by variance if feature count is excessive.
    
