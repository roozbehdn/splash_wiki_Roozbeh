There are a lot of parameters allowing to customize the pipeline. They can be grouped into several categories.
The parameters will be displayed when running splash without parameters (or with `--help`).
 ### Input
 * `input_file` — positional argument, path to the file where input samples are defined, the  format is: per each line
 `{sample_name} {path}`
 path is a fastq[.gz] file in case of non-10X and txt
file for 10X/Visium where the content of text file is
`{first_file.fastq[.gz]},{second_file.fastq[.gz]}`
per line
## Base configuration
 * `--outname_prefix` — prefix of output file names (default: result)
 * `--anchor_len` — anchor length (default: 31)
 * `--gap_len` — gap length, if 'auto' it will be inferred from the data, in the opposite case it must be an int (default: 0)
 * `--target_len` — target length (default: 31)
 * `--anchor_list` — list of accepted anchors, this is path to plain text file with one anchor per line without any header (default accept all anchors)
 * `--pvals_correction_col_name` — for which column correction should be applied (default: pval_opt)
 * `--technology` — Technology used to generate the input data, must be one of 'base', '10x', 'visium' (default: `base`)
 * `--without_compactors` — if used compactors will not be run (default: False)
 * `--compactors_config` — optional json file with compactors configuration, example file content: { "num_threads": 4, "epsilon": 0.001 } (default: )
 * `--lookup_table_config` — optional json file with configuration of lookup_table, if not specified lookup_tables are not used  (default: )
 ## Filters and thresholds
 * `--poly_ACGT_len` — filter out all anchors containing poly(ACGT) of length at least <poly_ACGT_len> (0 means no filtering) (default: 8)
 * `--artifacts` — path to artifacts, each anchor containing artifact will be filtered out (default: )
 * `--dont_filter_illumina_adapters` — if used anchors containing Illumina adapters will not be filtered out (default: False)
 * `--anchor_unique_targets_threshold` — filter out all anchors for which the number of unique targets is <= anchor_unique_targets_threshold (default: 1)
 * `--anchor_count_threshold` — filter out all anchors for which the total count <= anchor_count_threshold (default: 50)
 * `--anchor_samples_threshold` — filter out all anchors for which the number of unique samples is <= anchor_samples_threshold (default: 1)
 * `--anchor_sample_counts_threshold` — filter out anchor from sample if its count in this sample is <= anchor_sample_counts_threshold (default: 5)
 * `--n_most_freq_targets_for_stats` — use at most n_most_freq_targets_for_stats for each contingency table, 0 means use all (default: 0)
 * `--n_most_freq_targets_for_dump` — use when dumping satc (txt or binary), resulting file will only contain data for n_most_freq_targets_for_dump targets in each anchor, 0 means use all (default: 0)
 * `--fdr_threshold` — keep anchors having corrected p-val below this value (default: 0.05)
 * `--min_hamming_threshold` — keep only anchors with a pair of targets that differ by >= min_hamming_threshold (default: 0)
 * `--keep_top_n_target_entropy` — select keep_top_n_target_entropy records with highest target entropy (0 means don't select) (default: 10000)
 * `--keep_top_n_effect_size_bin` — select keep_top_n_effect_size_bin records with highest effect size bin (0 means don't select) (default: 20000)
 * `--keep_significant_anchors_satc` — if set there will be additional output file in SATC format with all significant anchors (default: False)
 * `--keep_top_target_entropy_anchors_satc` — if set there will be additional output file in SATC format with top target entropy significant anchors (default: False)
 * `--keep_top_effect_size_bin_anchors_satc` — if set there will be additional output file in SATC format with top effect size bin anchors (default: False)
## Additional output configuration
 * `--dump_Cjs` — output Cjs (default: False)
 * `--max_pval_opt_for_Cjs` — dump only Cjs for anchors that have pval_opt <= max_pval_opt_for_Cjs (default: 0.1)
 * `--n_most_freq_targets` — number of most frequent targets printed per each anchor (default: 10)
 * `--with_effect_size_cts` — if set effect_size_cts will be computed (default: False)
 * `--with_pval_asymp_opt` — if set pval_asymp_opt will be computed (default: False)
 * `--without_seqence_entropy` — if set sequence entropy for anchor and most freq targets will not be computed (default: False)
 * `--sample_name_to_id` — file name with mapping sample name <-> sample id (default: sample_name_to_id.mapping.txt)
 * `--dump_sample_anchor_target_count_txt` — if set contingency tables will be generated in text format (default: False)
 * `--dump_sample_anchor_target_count_binary` — if set contingency tables will be generated in binary (SATC) format, to convert to text format later `satc_dump` program may be used, it may take optionally mapping from id to sample_name (--sample_names param) (default: False)
 * `--supervised_test_samplesheet` — if used script for finding/visualizing anchors with metadata-dependent variation will be run (forces `--dump_sample_anchor_target_count_binary`) (default: )
 * `--supervised_test_anchor_sample_fraction_cutoff` — the cutoff for the minimum fraction of samples for each anchor (default: 0.4)
 * `--supervised_test_num_anchors` — maximum number of anchors to be tested example (default: 20000)
## Tuning statistics computation
 * `--opt_num_inits` — the number of altMaximize random initializations (default: 10)
 * `--opt_num_iters` — the maximum number of iterations in altMaximize (default: 50)
 * `--num_rand_cf` — the number of random c and f used for pval_base (default: 50)
 * `--num_splits` — the number of contingency table splits (default: 1)
 * `--opt_train_fraction` — use this fraction to create training data from contingency table (default: 0.25)
 * `--without_alt_max` — if set int alt max and related stats will not be computed (default: False)
 * `--without_sample_spectral_sum` — if set sample spectral sum will not be computed (default: False)
 * `--Cjs_samplesheet` — path to file with predefined Cjs for non-10X supervised mode (default: )
## Technical and performance-related
 * `--bin_path` — path to a directory where satc, satc_dump, satc_merge, sig_anch, kmc, kmc_tools, and other binaries are (if any not found there splash will check if installed and use installed) (default: bin)
 * `--tmp_dir` — path to a directory where temporary files will be stored (default: let splash decide)
<!--
 * `--n_input_generators` — number of input generators running in parallel, used only when input generators are defined (0 means auto adjustment)  (default: 0)
-->
 * `--n_threads_stage_1` — number of threads for the first stage, too large value is not recomended because of intensive disk access here, but may be profitable if there is a lot of small size samples in the input (0 means auto adjustment) (default: 0)
 * `--n_threads_stage_1_internal` — number of threads per each stage 1 thread (0 means auto adjustment) (default: 0)
 * `--n_threads_stage_1_internal_boost` — multiply the value of n_threads_stage_1_internal by this (may increase performance but the total number of running threads may be high) (default: 1)
 * `--n_threads_stage_2` — number of threads for the second stage, high value is recommended if possible, single thread will process single bin  (0 means auto adjustment) (default: 0)
 * `--n_bins` — the data will be split in a number of bins that will be merged later (default: 128)
 * `--kmc_use_RAM_only_mode` — True here may increase performance but also RAM-usage (default: False)
 * `--kmc_max_mem_GB` — maximal amount of memory (in GB) KMC will try to not extend (default: 12)
 * `--dont_clean_up` — if set then intermediate files will not be removed (default: False)
 * `--logs_dir` — director where run logs of each thread will be stored (default: logs)

## 10x or visium processing
 * `--cbc_len` — call barcode length (in case of 10X/Visium data) (default: 16)
 * `--umi_len` — UMI length (in case of 10X/Visium data) (default: 12)
 * `--soft_cbc_umi_len_limit` — allow additional symbols (beyond cbc_len + umi_len in _1.fastq 10X file UMI (default: 0)
 * `--cbc_filtering_thr` — how to filter cbcs, if 0 do the same as umi tools, in the opposite case keep cbcs with freq >= <cbc_filtering_thr> (default: 0)
 * `--cell_type_samplesheet` — path for mapping barcode to cell type, is used Helmert-based supervised mode is turned on (default: )
 * `--export_cbc_logs` — use if need cbc log files (default: False)
 * `--predefined_cbc` — path to file with predefined CBCs (default: )
 * `--export_filtered_input` — use if need filtered FASTQ files  (default: False)
 * `--allow_strange_cbc_umi_reads` — use to prevent the application from crashing when the CBC+UMI read length is outside the acceptable range (either shorter than CBC+UMI or longer than CBC+UMI+soft_cbc_umi_len_limit) (default: False)

<!--
## Postprocessing
 * `--postprocessing_item` — path to JSON defining postprocessing, may be defined multiple times, will be executed in the order of provided arguments (default: [])
 * `--exclude_postprocessing_item` — Path to JSON defining postprocessing to exclude from the default or provided postprocessing items (default: None)
-->