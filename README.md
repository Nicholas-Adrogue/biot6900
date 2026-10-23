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