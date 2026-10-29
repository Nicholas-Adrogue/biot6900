# BIOT 6900 coursework
## Nicholas Adrogue

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