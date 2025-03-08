sc-SPLASH is an ultra-efficient easy-to-use pipeline for analyzing transcriptomic complexity in barcoded scRNA-seq (and also barcoded spatial data such as Visium), as an alternative to reference-based, gene expression-centric approaches. 


![image](https://github.com/user-attachments/assets/30bb826a-7f4c-4327-b0fe-07163027be20)


## Installation
sc-SPLASH is integrated into the SPLASH2 pipeline and can be invoked by setting the input parameter `technology = 10x` for 10x scRNA-Seq analysis or `technology = visium` for Visium spatial analysis.

## Input format
The input file for sc-SPLASH needs to define the set of fastq files for each 10x library (as each 10x library might be sequenced across multiple sequencing lanes). UMI deduplication and barcode filtering is performed within the fastq files assigned to each 10x library. Each line in the input file defines a 10x library, where the first column determines the name of the library and the second column gives the path to a text file containing the paths to fastq files for the library. We provide an example input file [input-10X.txt](https://github.com/refresh-bio/SPLASH/blob/master/example/input-10X.txt), which defines four 10x libraries: `S10`, `S11`, `S12`, and `S13`. Each line in the text file for each library (second column of input file) gives the paths to one pair of R1 (for UMI and barcode) and R2 (for cDNA sequence) fastq files separated by a comma. For example, file [S10.txt](https://github.com/refresh-bio/SPLASH/blob/master/example/S10.txt) has four lines and each line gives the path to the R1 and R2 fastq files from one sequencing lane.

## Quick start
We provide example 10x analysis with sc-SPLASH on the test data from Tabula Sapiens Project. The data consists of four 10x libraries (as defined in files `S10.txt`, `S11.txt`, `S12.txt`, and `S13.txt`. Illumina NovaSeq 6000 runs from the study entitled "Detection and prevalence of SARS-CoV-2 co-infections during the Omicron variant circulation": 
```
cd splash-2.6.1.linux.x64
cd example
./download_10X.py #download test data
splash --technology 10x --cbc_len 16 --umi_len 12 --soft_cbc_umi_len_limit 0 input-10X.txt
```

## Barcoded-reads K-mer Counter (BKC)
BKC is a new optimized tool developed for sc-SPLASH for preprocessing and k-mer counting in barcoded data. Like other 10x preprocessing tools such as UMI-tools (Smith, Heger, and Sudbery 2017), BKC first extracts “trusted” cell barcodes and performs UMI deduplication. Additionally, BKC can separately parse reads assigned to each extracted cell to obtain counts for either k-mers or paired k-mers (e.g., anchor-target pairs in the context of SPLASH). BKC also includes a rapid step to filter sequencing artifacts due to Illumina adapters or other user-provided contaminants utilizing custom implementation of hash-tables and Bloom filter. We also provide BKC as a standalone tool, as we anticipate it could be valuable for developing other pipelines: https://github.com/refresh-bio/bkc 

### More extensions
The stringency of compactors' extension can be loosened by decreasing specificity requirement `--min_extender_specificity`. Additionally, the algorithm by default considers only the last k-mer as a potential extender. However, k-mers preceding it can be also considered by redefining `--num_extenders` and `--extenders_shift` parameters at the cost of increased computational time. The procedure operates from the end of a compactor and stops at the first extender fulfilling the requirement. In the following example, the 27-mers at shifts 0, 5, and 10 nucleotides from the current compactors' end are checked for the specificity criterion (which has been also loosened slightly).
```
compactors fastq.list anchors.tsv compactors.tsv --min_extender_specificity 0.8 --num_extenders 3 --extenders_shift 5
```

### Long reads
When analyzing long read data, the length of a compactor segment can be significantly increased. In the example below, it is set to 2700 bases (`num_kmers` \* `kmer_len`) while the maximum length of a compactor has been increased to 100 000 bases.
```
compactors fastq.list anchors.tsv compactors.tsv --num_kmers 100 --kmer_len 27 --max_length 100000
```

## Parameters

A short help is printed after running the executable without arguments. 

`compactors [options] <fastq_list> <anchors_tsv> <compactors_tsv>`

Positional parameters:
* `fastq_list`        	- input file with a list of FASTQ/FASTA files to be queried for anchors (one per line)
* `anchors_tsv`        	- input tsv file with anchors (seeds)
* `compactors_tsv`  		- output tsv with compactors
   
Options:
* `--input_format <fastq|fasta>` - input format (default: `fastq`)
* `--num_kmers <int>` - number of kmers in a compactor segment (default: 2, does not include an anchor/seed)
* `--kmer_len <int>` - length of kmers in a compactor segment (default: 27, max: 31)
* `--epsilon <real>` - sequencing error (default: 0.05)
* `--beta <real>` - beta parameter for active set generation, lower values increase sensitivity (default: 5)
* `--lower_bound <int>` - minimum kmer abundance to add it to an active set, lower values increase sensitivity (default: 10)
* `--max_mismatch <int>` - maximum mismatch count for compactor candidates (default: 4)
* `--all_anchors` - find all anchors' occurences in a read, not just the first one (default: off)

* `--no_extension` - disable recursive extension (default: enabled)
* `--max_length <int>` - maximum compactor length in bases (used only with recursion; default: 2000)
* `--min_extender_specificity <real>` - minimum extender specificity for current anchor to allow extensions (default: 0.9)
* `--num_extenders <int>` - number of extender candidates to be verified starting from the very end of the compactor (default: 1)
* `--extenders_shift <int>` - shift in bases between extender candidates to be verified (default: 1)
* `--max_anchor_compactors <int>` - maximum number of compactors that can originate from an anchor (default: 1000)
* `--max_child_compactors <int>` - maximum number of child compactors produced at each extension step (default: 20)
* `--extend_all` - if multiple compactors for a given anchor end with same extender, all are extended (default: off)
* `--out_fasta <name>` - name of the optional compactor FASTA (not generated by default)
* `--no_subcompactors` - do not include subcompactors in the output TSV (default: off)
* `--cumulated_stats` - include columns with cumulated stats in the output TSV (default: off)
* `--independent_outputs` - run compactors independently on input FASTQ files (default: off); output file names are preceded by the name of input FASTQs
	

* `--num_threads <int>` - number of threads
* `--reads_buffer_gb <int>` - size of the read buffer in GB (default: 24)
* `--keep_temp` - keep temporary files after the analysis, use previously generated temporary files (if exist) during the analysis (default: off)

The output of the program is a TSV table with compactors ordered increasingly by number of segments and then alphabetically w.r.t anchor and then decreasingly w.r.t support. The table contains the following columns:
* `anchor` - anchor sequence,
* `compactor` - compactor sequence,
* `id` - compactor numerical identifier,
* `parent_id` - identifier of compactor's parent (-1 if no parent exists),  
* `exact_support` - how many times the compactor was observed in reads exactly (if the compactor was extended, it concerns the part of the compactor added in the last extension),
* `support` - how many times the compactor was observed in reads up to `max_mismatch` errors (if the compactor was extended, it concerns the part of the compactor added in the last extension),
* `extender_specificity` - how the last investigated extender is specific for the seed of the current compactor's segment (if specificity > `min_extender_specificity`, an extension is performed),
* `extender_shift` - shift in bases of the last investigated extender from the compactor end,
* `total_length` - total compactor length in bases,
* `num_extended` - how many times the compactor was extended,
* `expected_read_count` - expected number of compactor occurences in the reads,
* `cumulated_id` - comma-separated list of identifiers of all compactor segments (only with `--cumulated_stats` flag),
* `cumulated_exact_support` - comma-separated list of `exact_support` of all compactor segments (only with `--cumulated_stats` flag),
* `cumulated_extender_specificity` - comma-separated list of `extender_specificity` of all compactor segments (only with `--cumulated_stats` flag),

The optional FASTA file can also be created by using `--out_fasta` option.
