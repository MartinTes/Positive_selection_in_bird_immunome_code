# Positive selection in bird immunome - code
Code for paper Tesicky et al. *‘Immunome adaptive evolution is driven by protein function and localization’*

This is a collection of scripts performing most of the analysis reported in Tesicky et al. Immunome adaptive evolution is driven by protein function and localization. Scripts are performed in R or bash Linux. If you encounter unexpected errors when running these scripts, a possible reason is name conflicts between functions from different packages (sometimes functions have the same names but require different syntax). In such cases, please, try running the scripts in a clean environment. 

## Data preparation ###
**o Downloading TOGA gene nucleotide sequence alignments** 
`TOGA_downloading.sh` – downloading TOGA gene alignments based on the list of immune genes *(immune_gene_filelist.txt)*

**o CIAlign: Removing gappy columns from the alignment **

`CIAlign_filtering.sh` – After filtering out unnecessary species from the original TOGA alignment, many columns containing only gaps remain in the alignment. This script removes those columns. Only manual trimming of poorly aligned ends remains, after which the alignments are ready for selection analysis. CIAlign can also perform this step, but it does not preserve the open reading frame.

## Positive selection analysis ###
#### (A) Running models ####
(A)	Collection of Linux scripts for running positive selection analysis on a Linux server. While MEME and FUBAR are run in Hyphy package, CODEML is run in the DGINN pipeline (singularity workflow). These scripts follow always the same logic: “outer” scripts queue* prepare gene-specific “inner” script according to the universal template (*_PSS_Martin.sh) and launch multiple jobs in parallel. These scripts require *.fasta and *.newick files with identical names for each species. For MEME and FUBAR, results of positive selection are provided in gene-specific *.JSON files, which are further parsed by custom-based R scripts (see below).

**o	MEME** 

`queue_MEMEjobs_PSS_all.sh` – job submitting outer script

`metacentrum_MEME_PSS-Martin.sh` – running inner script

**o	FUBAR** 

`queue_FUBARjobs_PSS_all.sh` – job submitting outer script

`metacentrum_FUBAR_PSS-Martin.sh` – running inner script

**o	CODEML (DGINN pipeline)**

`queue_DGINNjobs_PSS_all.sh`  – job submitting outer script  

`metacentrum_DGINN_PSS-Martin.sh` – running inner script

The DGINN pipeline also requires the parameters_possel-Martin.txt file, which defines the active DGINN modules and CODEML models, as well as the singularity dginn_latest.sif (Singularity must be in the same folder).
`PAML_DGINN_preprocessing.sh` – preprocessing CODEML results. All codeml results should be in the same folder in different subfolders. This script simplifies folder structure and renames model results by gene name.
`get_PAML_lnL_PP.sh` – processing CODEML results from individual gene files that were preprocessed by PAML_DGINN_preprocessing.sh and merge into a single table: `PAML_summary_results_all_compiled.txt.` 

#### (B) Parsing results ####
(B)	R scripts for parsing positive selection results. MEME and FUBAR results are in the type of JSON files, with a separate file for each gene. These can also be checked individually at http://vision.hyphy.org/, which provides similar output similar to the Datamonkey server (https://www.datamonkey.org/). For CODEML, the script parses preprocessed *.txt file. Results from individual methods are always provided in both long and wide format tables, as well as RDS objects. 

**o	MEME** 

`Meme_extraction_script.R` – parses MEME results from individual gene JSON files

**o	FUBAR** 

`FUBAR_extraction_script.R` – parses MEME results from individual gene JSON files

**o DGINN CODEML**

`PAML_extraction_script.R` – parses results from combined results for all genes using the preprocessed .txt file: *PAML_summary_results_all_compiled.txt*

**o	Combining results of all three selection methods**
`
PSS_combining_all_results_script.R` – combines results of individual methods all together (**)

## Statistical and data analysis ###
**o Gene over-representation analysis (ORA)**

`clusterProfiler_gene_ORA_script.R` – performs ORA analysis using Gene Ontology and Reactome terms

**o  Statistical analysis** 

`PSS_immune_genes_statistical_analysis.R` – performs statistical analysis of positive selection results and visualizes plot

