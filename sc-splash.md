sc-SPLASH is an ultra-efficient easy-to-use pipeline for analyzing transcriptomic complexity in barcoded scRNA-seq (and also barcoded spatial data such as Visium), as an alternative to reference-based, gene expression-centric approaches. 


![image](https://github.com/user-attachments/assets/30bb826a-7f4c-4327-b0fe-07163027be20)


## Installation
sc-SPLASH is integrated into the SPLASH2 pipeline and can be invoked by setting the input parameter `technology = 10x` for 10x scRNA-Seq analysis or `technology = visium` for Visium spatial analysis.

## Input format
The input file for sc-SPLASH needs to define the set of fastq files for each 10x library (as each 10x library might be sequenced across multiple sequencing lanes). UMI deduplication and barcode filtering is performed within the fastq files assigned to each 10x library. Each line in the input file defines a 10x library, where the first column determines the name of the library and the second column gives the path to a text file containing the paths to fastq files for the library. We provide an example input file [input-10X.txt](https://github.com/refresh-bio/SPLASH/blob/master/example/input-10X.txt), which defines four 10x libraries: `S10`, `S11`, `S12`, and `S13`. Each line in the text file for each library (second column of input file) gives the paths to one pair of R1 (for UMI and barcode) and R2 (for cDNA sequence) fastq files separated by a comma. For example, file [S10.txt](https://github.com/refresh-bio/SPLASH/blob/master/example/S10.txt) has four lines and each line gives the path to the R1 and R2 fastq files from one sequencing lane.

## Quick start
We provide example 10x analysis with sc-SPLASH on the test data from Tabula Sapiens Project. The data consists of four 10x libraries (as defined in files `S10.txt`, `S11.txt`, `S12.txt`, and `S13.txt`.
```
cd splash-2.6.1.linux.x64
cd example
./download_10X.py #download test data
splash --technology 10x --cbc_len 16 --umi_len 12 --soft_cbc_umi_len_limit 0 input-10X.txt
```
## Parameters
The parameters that are specific for analysis by sc-SPLASH are described in [Configuration section](./Configuration#10x-or-visium-processing)

## Barcoded-reads K-mer Counter (BKC)
BKC is a new optimized tool developed for sc-SPLASH for preprocessing and k-mer counting in barcoded data. Like other 10x preprocessing tools such as UMI-tools (Smith, Heger, and Sudbery 2017), BKC first extracts “trusted” cell barcodes and performs UMI deduplication. Additionally, BKC can separately parse reads assigned to each extracted cell to obtain counts for either k-mers or paired k-mers (e.g., anchor-target pairs in the context of SPLASH). BKC also includes a rapid step to filter sequencing artifacts due to Illumina adapters or other user-provided contaminants utilizing custom implementation of hash-tables and Bloom filter. We also provide BKC as a standalone tool, as we anticipate it could be valuable for developing other pipelines: https://github.com/refresh-bio/bkc 
