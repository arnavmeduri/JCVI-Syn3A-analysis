## Steps

#### Download JCVI-syn3A in GenBank format  
https://www.ncbi.nlm.nih.gov/nuccore/CP016816.2  
* Extract all annotated proteins to a FASTA file using Biopython – saved as “proteome”

#### Identify the subset of protein families to investigate for multiple sequence alignment  
* Search the proteome with the Pfam-A library of HMMs using HMMER (http://hmmer.org/)  
* Download the Pfam-A.hmm file from https://www.ebi.ac.uk/interpro/download/pfam/  
* Run `hmmsearch` using the following command:  
```
hmmsearch --domtblout results Pfam-A.hmm JCVI-Syn3A.fasta
```

#### Import the `--domtblout` results into a spreadsheet or analysis tool for manual review 
* Sort the data by the 'E-value' column and mark all accessions with E-values greater than 1e-60  
* Graph: histogram of Pfam scores  
* Graph: histogram of number of Pfam hits per protein  
* Graph: histogram of number of proteins per Pfam model with hits  
* The final result is a list of 110 protein families, listed in the file `JCVI-Syn3A-pfam-accessions`

#### Generate multiple sequence alignment files  
* Download the sequence alignment databases:  
  - https://ftp.uniprot.org/pub/databases/uniprot/current_release/knowledgebase/complete/uniprot_sprot.fasta.gz (0.6 GB)  
  - https://ftp.uniprot.org/pub/databases/uniprot/current_release/knowledgebase/complete/uniprot_trembl.fasta.gz (53 GB)  
  - https://ftp.ebi.ac.uk/pub/databases/Pfam/current_release/Pfam-A.full.uniprot.gz (32 GB)  

* From `Pfam-A.full.uniprot.gz`, extract Pfam alignments for the 110 families identified in the previous step (one file per family)  
* Each Pfam file contains a number of alignments, each with an alignment ID and alignment sequence. The alignment ID consists of a protein ID and a sequence range.  
  From the alignment IDs, parse the list of protein IDs and create a unique list of proteins across all 110 families in `unique_proteins.csv`  
* Both `uniprot_trembl.fasta.gz` and `uniprot_sprot.fasta.gz` contain protein data, including species (via the "OX=" attribute) and sequence data.  
  Using `unique_proteins.csv` as a guide, parse these files to extract `{Protein, Species}` mappings as a CSV file  
* Create a list of proteins that belong to the *Mycoplasma* genus  
* Taking two Pfam entries at a time, create a glued sequence file by concatenating sequences

#### Run EVCouplings analysis using `plmc`  
* On the glued sequence file, use the *Mycoplasma* sequence as the focus sequence; run the `plmc` tool to generate coupling scores across amino acid pairs  
* Ignore scores for intra-protein residues; subset the couplings to those with a z-score > 4  
* Normalize the coupling files to map indexes in the glued sequence back to indexes in the original protein sequences  
* This results in a total of 33 possible interactions across protein pairs

#### Generate interaction surfaces using PyMOL  
* Download AlphaFold PDB files for each protein in the interaction  
* Create a PyMOL automation script to draw residue positions between the two interacting proteins  
* Orient the proteins to determine whether the interaction surface can be clearly identified  
* Save the images and project files in PyMOL  
* For interactions with a clearly visible surface, create a heatmap of hydrophilic and hydrophobic interactions
