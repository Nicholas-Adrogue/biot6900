# BIOT 6900 coursework
## Nicholas Adrogue

### Environment setup
All notebooks run in one conda environment, `biot6900` (Python 3.11).

```bash
conda create -n biot6900 python=3.11
conda activate biot6900

# Core + Jupyter (all modules)
pip install jupyter notebook ipykernel numpy pandas scipy scikit-learn matplotlib requests

# Module 1-5: sequences, structures, variant scoring
pip install biopython alphagenome
conda install -c conda-forge -c bioconda fpocket   # Module 4 pocket detection

# Module 7: knowledge graph
pip install neo4j networkx anthropic

# Module 8: single-cell and spatial
pip install anndata scanpy squidpy

# Module 9: clinical NLP
pip install spacy scispacy negspacy
pip install "numpy>=2"   # scispacy pins numpy<2, which breaks Module 8; it works fine on numpy 2
python -m spacy download en_core_web_sm
pip install --no-deps https://s3-us-west-2.amazonaws.com/ai2-s2-scispacy/releases/v0.5.4/en_core_sci_sm-0.5.4.tar.gz
pip install --no-deps "https://huggingface.co/kormilitzin/en_core_med7_lg/resolve/main/en_core_med7_lg-any-py3-none-any.whl"   # optional
```

| Package | Version tested | Used in |
|---|---|---|
| numpy / pandas / scipy | 2.4.6 / 2.3.3 / 1.17.1 | all modules |
| scikit-learn / matplotlib | 1.9.1 / 3.11.2 | Modules 3, 5, 6, 9 / all modules |
| requests | 2.34.2 | Modules 1, 5, 7 |
| biopython | 1.88 | Module 1 |
| fpocket (conda) | | Module 4 |
| alphagenome | 0.10.0 | Module 5 |
| neo4j / networkx / anthropic | 6.4.1 / 3.6.1 / 2.1.0 | Module 7 |
| anndata / scanpy / squidpy | 0.12.19 / 1.11.5 / 1.8.2 | Module 8 |
| spacy / scispacy / negspacy | 3.8.18 / 0.6.2 / 1.1.0 | Module 9 |
| en_core_web_sm / en_core_sci_sm | 3.8.0 / 0.5.4 | Module 9 (spaCy models) |
| en_core_med7_lg | not installed | Module 9 (optional Med7 preview) |

Setup notes:
- `en_core_sci_sm` 0.5.4 is the newest scispaCy model and was built for spaCy 3.7. To load it on spaCy 3.8, open its `config.cfg` (in `site-packages/en_core_sci_sm/en_core_sci_sm-0.5.4/`) and change the three quoted booleans `= "False"` to `= false`.
- Module 7 reads Neo4j credentials from a git-ignored `.env` file (`NEO4J_URI`, `NEO4J_USERNAME`, `NEO4J_PASSWORD`).

### Module 1
My only issue was with deleting old Conda environments from my computer since 
they had been corrupted. After that, everything worked as expected.

### Module 2
#### I chose to do Alzheimers.  
#### Proteomic:  
    Global quantitative analysis of the human brain proteome in AD and PD for Nature
    Scientific Data  
    https://www.synapse.org/Synapse:syn12269876  
    Application  
    Renamed to: ad_proteomics.tsv    

#### Transcriptomic:  
    GE104704  
    https://www.ncbi.nlm.nih.gov/geo/download/?acc=GSE104704  
    Open  
    Renamed to: ad_transcriptomics.tsv    

#### Genomic:  
    MONDO_0004975  
    https://www.ebi.ac.uk/gwas/efotraits/MONDO_0004975  
    Open  
    Renamed to: ad_gwas.tsv

### Module 3
Everything worked well

### Module 4
Doing AD, with targets APOE and HCN1

The provided global_align forces end-to-end pairing. That paired APOE's last 3 experimental residues with AlphaFold 302–306 instead of 181–183. One pair deviated by 84 Å and inflated the RMSD to 7.36 Å. For HCN1, AlphaFold 584–631 were paired even though 8Y60 resolves nothing past 579. I fixed this by keeping only pairs on the dominant numbering offset (18 for APOE, 0 for HCN1). That dropped 1 APOE pair and 5 HCN1 pairs and gave 0.77 Å and 1.35 Å. The fix assumes the PDB numbering is a constant shift of UniProt numbering.

### Module 5
I used CYP2C19, and I did run Atlas.

Note on the Atlas cell (Step 7). The top_feature tally in Step 7 sums to 169 although the cell reports scoring 70 variants, which indicates rows were duplicated when the Atlas scores were merged onto the variant table. The Step 8 flag counts are computed over that enlarged table and are therefore overstated. Every variant discussed in my report has an rsID and a distinct score, and pgx_classified_CYP2C19.csv is written before this cell runs.

### Module 6
Graded notebook: `BIOT6900_Module6_Part3_Graded_Starter.ipynb`. I ran it on real data (`USE_DEMO = False`): 1000 Genomes genotypes (2,336 people; 500 AFR, 500 EAS, 500 EUR, 489 SAS, 347 AMR) with the Alzheimer's score PGS000334. The `data/` folder is gitignored; the file checksums printed in Section 2 are the proof of real data.

Pipeline: harmonized the score alleles against REF/ALT (22 score variants, 0 not found in the panel; 22 used, 13 flipped, 0 dropped for allele mismatch), computed the PRS as `G @ w` and standardized it, then evaluated the distribution shift, discrimination, and calibration by ancestry, and whether the PRS adds to age alone.

Key results:
- One whole-cohort top-10% cutoff puts 28.0% of AFR above it versus 7.8% of EUR and 2.2% of EAS. AFR also has the highest mean PRS (0.74).
- AUC is highest in EUR (0.672) and near chance in AFR (0.493). Overall AUC is 0.599.
- Cross-validated AUC goes from 0.562 (age only) to 0.618 (age + PRS).

Figures and key result tables (CSV) from the graded notebooks are saved in `images/`, with files named by module, part, and content (for Module 6, `module6_part3_*`).

Caveat: the phenotype is simulated (1000 Genomes has no disease labels), so AUC and calibration are illustrative. The weak AFR performance partly reflects the simulation's missing LD proxy for APOE ε4 in that group, not real biology.

### Module 7
Disease: Alzheimer's disease. Notebook: `BIOT6900_Module7_Starter.ipynb`. Outputs are saved in `images/` (like my earlier modules' figures): `images/my_knowledge_graph.png`, `images/nodes.csv`, `images/edges.csv`.

The graph has 82 nodes and 85 relationships. Genes are my Module 3 top 15, carrying my Module 2 GWAS, RNA and protein evidence and my Module 3 druggability scores. Patients are 20 people from my Module 6 cohort. Phenotypes come from HPO, pathways from Reactome, and drugs from Open Targets. Sources are cited in comments in section 1 of the notebook. Neo4j credentials are read from the git-ignored `.env`.

What didn't work or needed changing:
- My original AuraDB instance had been paused, so I resumed it.
- The provided node-count query in section 2 listed every node under the shared `Entity` label, because `labels(n)[0]` returns that label. I changed it to skip `Entity`, so it now reports counts per node type, and added a per-relationship count.
- My Module 5 gene was CYP2C19, which isn't an Alzheimer's gene, so the variant layer uses lead SNPs from my Module 2 GWAS file plus the APOE ε4/ε2 SNPs instead of Module 5 calls.
- Open Targets lists no drug for HCN1. I added ivabradine → HCN1 from the primary literature (cited in the notebook).
- I changed the starter's build code in five commented places:
  - it allows more than one related disease
  - related-disease edges cite their real evidence instead of reusing the Alzheimer's GWAS p-value
  - patients store their ancestry
  - patients link to their APOE variants
  - every node and edge carries a `source` citation, plus an `xref` ID (HPO, Reactome, ChEMBL, OMIM/Orphanet/Mondo, rsID) where one exists
- Patient age and case status are simulated, as in Module 6.

### Module 8


### Module 9
Disease: Alzheimer's disease (hospitalized patients, outcome = in-hospital death). Notebook: `BIOT6900_Module9_Notebook3_Graded.ipynb`. It needs only pandas and scikit-learn, and all data is synthetic, generated inside the notebook (`SEED = 1386377`).

Signals (seizure, gait disturbance, APOE ε4) and consequences (anti-amyloid therapy stopped, feeding tube, comfort care) come from my Module 2, 6 and 7 findings. Sources and DOIs are in the comments of the Part A cell.

Results: AUROC 0.912 with everything, 0.780 with my declared leakage removed, and 0.680 with all leakage removed (`renal_replacement` caught as the hidden leak).

What didn't fit:
- The provided engine is built for sepsis, so every note includes ICU lactate and creatinine values and the two injected features (`lactate_not_clearing`, `renal_replacement`) are sepsis concepts. I left the engine unedited, as instructed.
- My three AD signals add little to the model on top of age. With 400 patients, their effects are too small to show clearly.