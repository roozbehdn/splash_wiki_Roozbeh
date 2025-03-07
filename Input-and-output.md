## Input format
In the example, the `input.txt` file was used. This file defines the set of input samples for the algorithm.
Its format is one sample per line. Each line should contain the name of a sample and (after space) path to the input sample.

**Important note:** if a relative path is specified it is relative to the current working directory, not the directory of `input.txt`.
<!--
### Input generators
SPLASH handles natively fasta/fastq (gz or not) input files.
To support other formats (like SAM, BAM, CRAM, SRA) one may first prepare input data in the above format with an appropriate command.
It is also possible to use a built-in input generator mechanism.
Input format is:
```
<sample_name_i> <path_to_reads_i> [generator_i]
```
where the `generator_i` is an optional command that SPLASH will run before main processing.
**Warning:** This command will be run even if the file at `path_to_reads_i` already exists.
For example, to retrieve samples from SRA and run SPLASH one may define input as follows:
```
SRR30680504 SRR30680504_1.fastq fasterq-dump SRR30680504
SRR30680505 SRR30680505_1.fastq fasterq-dump SRR30680505
```
(of course, it may be profitable to configure fasterq-dump to generate fasta instead of fastq, or to use prefetch first, etc.).
The same rule may be applied to convert BAM/SAM/CRAM to fasta/q.
A couple of examples are in `example` directory:
 - `input-with-generators-wget.txt`
 - `input-with-generators-fasterq-dump.txt`

In the case of 10X/Visum data, the idea is similar, but instead of defining generating commands in the main input file, they are defined on a lower level (see an example in `example/input-10X-with-generators-wget.txt` and `example/S*-generators-wget.txt`).

The number of generators running in parallel may be defined with `--n_input_generators` parameter (if not set, this will be auto-adjusted).
-->
## Understanding the output
There are following columns in the resulting tsv files
|                 Column            |     Meaning                            |     Notes                                                                              |
| ----------------------------------| -------------------------------------- | -------------------------------------------------------------------------------------- |
| anchor                            | anchor                                 |                                                                                        |
| pval_opt                          | p-value from alternating maximization  | number of iterations and other parameters can be configured with switches. Optimization formulation and statistical exposition in (Baharav et al. 2023).             |
| pval_sample_spectral_sum          | p-value from spectral sum              | may be disabled with `--without_sample_spectral_sum`                                   |
| effect_size_bin                   | measure of anchor's effect size        | bounded between [0,1], indicates how well the data is divided between 2 groups of columns. 0 indicates no different between groups, 1 indicates two groups of columns with disjoint row supports (Baharav et al. 2023).                    |
| pval_asymp_opt                    | asymptotically valid p-value based on alternating maximization                  | compute optimizing c and f used for pval_opt, and evaluate approximate p-value given by asymptotic normality (Baharav et al. 2023). Only if `--with_pval_asymp_opt` was used. |
| pval_base                         | p-value based on random partitionings      | Compute base p-value (using num_rand_cf random c,f), Bonferroni correction to yield valid p-value. |
| M                                 | anchor count                           | number of anchor occurences in the input (= the sum of elements in contingency table)  |
| anch_uniqTargs                    | number of unique targets for anchor    | (=number of rows in contingency table)                                                  |
| number_nonzero_samples            | number of samples containing anchor    | (=number of columns in contingency table)                                              |
| target_entropy                    | measure of diversity of target distribution         | entropy of empirical target distribution. Compute the empirical target distribution $(p_1,...,p_T)$ over the $T$ observed targets by summing counts across all samples and normalizing, and output the entropy of this empirical target distribution $H = -\sum_i p_i \log_2 (p_i)$.            |
| avg_no_homopolymer_targets        |                                        |                                                                                            |
| avg_hamming_distance_max_target   | average hamming distance to most abundant target | for each observed target (count), compute the hamming distance between it and the most abundant target (highest counts), average this over all targets. Useful in identifying / filtering SNPs (this measure will be low). |
| avg_hamming_distance_all_pairs    | average hamming distance between all pairs of targets | For the M counts in the matrix, compute the hamming distance between all pairs of targets, output the average.                                                                                       |
| avg_edit_distance_max_target      | average Levenshtein distance to most abundant target | Same as avg_hamming_distance_max_target, but Levenshtein distance as opposed to hamming distance. If an anchor has high avg_hamming_distance_max_target but low avg_edit_distance_max_target, this is indicative that the discrepancy between the top targets is due to an insertion or deletion. If the two quantities are similar, then this discrepancy is most likely due to a SNP.                                                                                        |
| avg_edit_distance_all_pairs       | average Levenshtein distance between all pairs of targets | Same as avg_hamming_distance_all_pairs but with Levenshtein distance as opposed to hamming distance.                                                                                       |
| anchor_2mer_seq_entropy           | 2-mer sequence entropy of the anchor                      |                                                                        |
| anchor_3mer_seq_entropy           | 3-mer sequence entropy of the anchor                      |                                                                        |
| most_freq_target_<i>              | `i`-th most frequent target for current anchor            | The number of reported targets may be controlled with `--n_most_freq_targets` |
| cnt_most_freq_target_<i>            | count of the `i`-th most frequent target for current anchor | The number of reported targets may be controlled with `--n_most_freq_targets` |
| most_freq_target_<i>_2mer_seq_entropy| 2-mer sequence entropy of the `i`-th most frequent target for current anchor | The number of reported targets may be controlled with `--n_most_freq_targets` |
| most_freq_target_<i>_3mer_seq_entropy| 3-mer sequence entropy of the `i`-th most frequent target for current anchor | The number of reported targets may be controlled with `--n_most_freq_targets` |
| pval_opt_corrected  | Benjamini-Yekutieli corrected pval_opt       |   only present in `*.scores.tsv` file                                                             |

## Additional output
### Most frequent targets per each anchor
By default SPLASH will store 10 most frequent targets per each anchor in the resulting TSV files. 2 should be sufficient for splicing, but for RNA editing/mismatches 4 may be a better choice. It may be set with `--n_most_freq_targets` switch. If the number of targets for a given anchor is lower than specified value there will be a single `-` for each missing target.
### SATC format
SPLASH stores intermediate and optional output files in SATC format (**S**ample **A**nchor **T**arget **C**ount).
### Sample representation
The unique id is assigned to each sample. The ids are consecutive numbers starting with 0. The first sample from the input file gets id 0, the second one gets 1, and so on. By default SPLASH will store the mapping in `sample_name_to_id.mapping.txt` file, but this may be redefined with `--sample_name_to_id` parameter. This mapping file may be useful to access the data stored in SATC format.
### Output sample, anchor, target, count
#### Textual
When `--dump_sample_anchor_target_count_txt` switch is used there will be an additional output directory (named `result_dumps` by default, but the `results` part may be redefined with `--outname_prefix` switch).
This directory will contain a number of files (equal to the number of bins, default 128, may be redefined with `--n_bins` switch).
The extension of these files is `.satc.dump`.
Each line of these files is a tab-separated list of <sample_name> <anchor> <target> <count>.
This is the easiest way to be able to reproduce contingency tables used during computation.
Each file contains some number of anchors, but it is assured that a specific anchor is present in a single file.
Since these text files could be large, it may be proficient to use binary (SATC) files instead, especially if one wants to investigate only some of all anchors.
#### Binary
When `--dump_sample_anchor_target_count_binary` switch is used there will be an additional output directory (named `result_satc` by default, but the `results` part may be redefined with `--outname_prefix` switch).
This directory will contain a number of files (equal to the number of bins, default 128, may be redefined with `--n_bins` switch).
The extension of these files is `.satc`.
These are binary files in SATC format.
Their content may be converted to textual representation with `satc_dump` program (part of the SPLASH package).
Each file contains some number of anchors, but it is assured that a specific anchor is present in a single file.

### satc_dump
To convert SATC files into textual representation one may use `satc_dump` program.
The simples usage is
```
 satc_dump input.satc output.satc.dump
```
There are also additional parameters that may be useful, namely:
 * `--anchor_list` — path to text file containing anchors separated by whitespaces (or tsv file with header containing column named 'anchor'), only anchors from this file will be dumped
 * `--sample_names` — path for decode sample id, each line should contain <sample_name> <sample_id>
 * `--n_bins <int>` — if set to value different than 0 the input is interpreted as a list of bins (each bin in separate line, first list is bin_0, second line is bin_1, etc. (in case of ill-formed input results will be incorrect))
 * `--separately` — if set with n_bins != 0 output param will be treated as suffix name and there will be output for each bin
 * `--binary` — if set the output will be in binary SATC format instead of text (satc_dump my be used as a anchor-based filter)

 If `--sample_names` is not used in the output there will be sample ids instead of its names. SPLASH by default write mapping to `sample_name_to_id.mapping.txt` file (may be redefined with `--sample_name_to_id` switch of SPLASH.

 `satc_dump` may be also used to determine in which bin given anchor may be present (to which bin it is mapped basing on hash value).
 Below is the usage example for such a case.
 ```
 satc_dump --which-bin --n_bins <int> <anchor>
 ```
 ### Interpreting results
 `plotGeneration.py` provides basic functionality to visualize contingency tables. To use, run SPLASH with the --dump_sample_anchor_target_count_binary flag. Then, `plotGeneration.py` can be run with inputs:
- `--dsName` — dataset name to be used in title of plots
- `outFolder` — path to save files to
- `metadataPath` — path to .tsv file containing two columns titled `sampleName` and `metadata`
- `satcFolder` — path to folder containing .satc files
- `pvDfPath` — path to p-value .tsv file (result.after_correction.scores.tsv)
- `sampleMappingTxt` — path to sample_name_to_id.mapping.txt file
- `anchorFile` — file of anchors to be plotted, one anchor per line
- `satc_dump_file` — path to satc_dump utility file, within SPLASH /bin folder
- `skipSATC` — optional flag, if .satc files have already been dumped for this set of anchors into the output folder, and the desire is to regenerate plots.

The generated output is 1 file per anchor, each file containing 4 subplots. At bottom left is the raw I x J contingency table, where each column is a sample, and each row represents a target (low abundance targets not shown). Each sample’s target counts are normalized by $n_j$ and plotted. At bottom right, the targets for the contingency table are displayed in a I x k table (corresponding to the rows of the contingency table, visually aligned). Each target of length k is shown, with base pairs colored as in the below colorbar. The target sequence for each row is displayed on the right. At top left,  the sample metadata is shown in a 1 x J table. Each entry corresponds to the column in the contingency table directly below it. The sample metadata is shown in a color bar below. At top right, the column counts ($n_j$) are shown in a 1 x J table, where the colorbar below provides the scale. Again, columns are sorted as the contingency table.


The script `c_analysis.ipynb` shows how the saved c vectors can be loaded in for further analysis. `--dump_Cjs` must be enabled for this.
