## Example Applications

## Read replacement:
The simplest way to use SPLASH is to run it on a set of samples where you would have analyzed with a traditional pipeline (in read-replacement mode). In this mode, SPLASH will replace reads with those containing significant anchor targets and report significant anchor-targets as *read replacements*. You can then perform any of the interpretive steps that you would have otherwise performed. The advantages of this approach are speed, statistical precision and removing the filter of alignment before statistical significance. 

Given the broad applications of SPLASH, in analysis_notebooks folder, we provide the analysis downstream of SPLASH on how to interpret the results for a few major applications in which SPLASH has been applied so far. We should note that SPLASH is quite general and continues to be applied in new genomics problems.

#### Splicing analysis for RNA-Seq
The notebook (`analysis_notebooks/SPLASH_splicing_analysis_notebook.Rmd`) provides detailed step-by-step instructions on how SPLASH results can be interpreted for an alternative splicing analysis. The reference genome in this example is human but it could be replaced with any other organism with any quality of transcriptome annotation. 


## Biological interpretation and classification of anchors
To facilitate downstream analysis of anchors, we provide a postprocessing script `SPLASH_extendor_classification.R`, that can be run on the anchors file generated from the SPLASH run to classify anchors to biologically meaningful events such as alternative splicing, and base pair changes. `SPLASH_extendor_classification.R` needs the following inputs:

- `directory` — the output directory used for the SPLASH run
- `which_anchors_file` — flag to decide which anchor file (after correction or all anchors) to use, could be "after_correction" or "all"
- `effect_size_cutoff` — the effect size cutoff for significant anchors (default 0.2)
- `num_samples_cutoff` — the minimum number of samples for an anchor to be called (default 20)
- `STAR_executable` — path to STAR executable file
- `samtools_executable` — path to samtools executable file
- `bedtools_executable` — path to bedtools executable file
- `bowtie2_executable` — path to bowtie2 executable file
- `STAR_reference` — path to STAR index files for the reference genome
- `bowtie2_reference` — path to bowtie2 index for the reference genome
- `bowtie2_univec_index` — path to bowtie2 index for univec
- `annotated_splice_juncs` — path to the file containing annotated splice junctions from the reference transcriptome (can be either downloaded or generated from `SPLASH_build.R`)
- `annotated_exon_boundaries` — path to the file containing annotated exon boundaries from the reference transcriptome (can be either downloaded or generated from `SPLASH_build.R`)
- `gene_coords_file` — path to the file containing gene coordinates from the reference transcriptome (can be either downloaded or generated from `SPLASH_build.R`)
- `paralogs_file` — (optional) path to file containing list of paralogous genes from the reference genome
 
The script will generate a file `classified_anchors.tsv` in the same directory used for SPLASH run, containing significant anchors along with their biological classification and alignment information.

## Building index and annotation files needed for running classification script
For running the classification script for a given reference genome/transcriptome you first need to obtain a fasta file for the reference genome and a gtf file for the transcriptome annotation. You then need to do the following 3 steps (note that all index/annotation files from these 3 steps should be generated from the same fasta and gtf file):
- **STAR index**: you need [STAR](https://github.com/alexdobin/STAR) index for reference genome. You can use default parameters to build the index: `STAR --runThreadN 4 --runMode genomeGenerate --genomeDir STAR_index_files --genomeFastaFiles $fasta file$ --sjdbGTFfile $gtf file$`
- **Bowtie index**: you also need to [Bowtie2](https://github.com/BenLangmead/bowtie2) index that can be built using this command: `bowtie2-build $fasta file$ Bowtie_index_files/$Bowtie_index_name$`
- **Three annotation files**: three files are needed for annotated exon boundaries, annotated splice sites, and gene coordinates that should be built by running script `SPLASH_build.R`. You need three inputs for `SPLASH_build.R` script: `$gtf_file$` (absolute path to the gtf file), `$hisat2_directory$` (directory containing HISAT2 codes downloaded from [HISAT2 repository](https://github.com/DaehwanKimLab/hisat2), the script assumes that there are two python scripts at: `$hisat2_directory$/extract_exons.py` and `$hisat2_directory$/extract_splice_sites.py`), `$outfile_name$` (the name used for the annotation files that script will generate). The script can be run using the following command:
`Rscript SPLASH_build.R $gtf_file$ $hisat2_directory$ $outfile_name$`
If the script is run successfully, it will generate 3 output annotation files in the same directory as the script: `$outfile_name$_known_splice_sites.txt` (for annotated splice sites), `$outfile_name$_exon_coordinates.bed` (for annotated exon boundaries), and `$outfile_name$_genes.bed` (for annotated gene coordinates)

## Downloading pre-built annotation files for human and mouse genomes:
 The human files were built for both [T2T assembly](https://www.ncbi.nlm.nih.gov/datasets/genome/GCF_009914755.1/) and [GRCh38 assembly](https://www.ncbi.nlm.nih.gov/datasets/genome/GCF_000001405.40/). The mouse files were built based on [mm39 assembly](https://www.ncbi.nlm.nih.gov/assembly/GCF_000001635.27/). The annotation files can be downloaded using the following links:
- **Human (T2T)**:
   - `Annotated exon coordinates`: https://drive.google.com/file/d/1R-4-ICDAzmIBgQmlOF22nNrCWoSgrmHi/view?usp=share_link
   - `Annotated splice junctions`: https://drive.google.com/file/d/1owlOQyP1z4cyFvYcAAA-qQmc-K6jGbs9/view?usp=share_link
   - `Gene coordinates`: https://drive.google.com/file/d/1L0A7iGXEYiOsPQ0QiJayKPybJ79ZDi2F/view?usp=sharing
   - `Paralogous genes`: https://drive.google.com/file/d/1mqGft4tPlx8X3cRYoqQnPeXaonLfSbGa/view?usp=share_link
- **Human (GRCh38)**:
   - `Annotated exon coordinates`: https://drive.google.com/file/d/1oK6OgQnFFVvybBo0EZ5aIyeoZLAtMyZF/view?usp=sharing
   - `Annotated splice junctions`: https://drive.google.com/file/d/1izVHy1m-ddlNgJtFKfWcHdtkc_Y5bHHP/view?usp=sharing
   - `Gene coordinates`: https://drive.google.com/file/d/1REfnl9ZNYcsb-1jSurDHcsL7QFJ00JEp/view?usp=sharing
 - **Mouse (mm39)**:
   - `Annotated exon coordinates`: https://drive.google.com/file/d/1npE0rkxhsDtJk3FeMdfuZwc5Elfuk4bq/view?usp=sharing
   - `Annotated splice junctions`: https://drive.google.com/file/d/1iJhf421nMRDC0uCo_0jh7Nkns8NAieTE/view?usp=sharing
   - `Gene coordinates`: https://drive.google.com/file/d/1V8By-yq7AmgXY-XDhipgjjsamL0ghhJa/view?usp=sharing
   - `Paralogous genes`: https://drive.google.com/file/d/1Uf28bE2XF9Y2w57ARlUGfO5agiFFXx2S/view?usp=sharing
