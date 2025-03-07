
# Motivation
At its core, SPLASH detects biologically important sequences based on k-mers and statistical methodology. These sequences are usually represented as extenders (anchor+target concatenation) or compactors. Some additional steps are typically required to make biological discoveries. An example of such may be running blast on extenders. SPLASH provides a way to run various post-processing (some of which are default). Each postprocessing is defined in a dedicated JSON file. Its format is described later in this document. 

## Make SPLASH run additional post-processing
To run postprocessing that is not called by SPLASH by default, one should use parameter `--postprocessing_item <path>`, where `<path>` is the path to a JSON post-processing definition file. This switch may be used multiple times to run many post-processings. They are executed in the order of appearance in the command line. Default post-processings are always run at the beginning, before any defined with `--postprocessing_item` parameter.

## Disable default post-processing
To disable default postprocessing, one may use `--exclude_postprocessing_item <path>`, where `<path>` is the path to a JSON post-processing definition file. For example, to disable blast, one should use `--exclude_postprocessing_item postprocessing/blast.json`. This parameter may appear multiple times to disable many post-processings.

**Warning** Currently we have turned of any default post-processings but this may change in the future.

# JSON post-processing definition file format
The JSON post-processing definition file should contain a JSON object with the following fields:
 - `name` [REQUIRED] - the name of postprocessing. It is used to create a post-processing run directory. Assuming SPLASH is run with the default `--outname_prefix,` i.e., `result` and the `name` is `blast` SPLASH will create the directory `result_blast` where the postprocessing will be run,

 - `command` [REQUIRED] - this defines a command that will be run in postprocessing. In this command, one may (and probably will need to) specify placeholders (inside `{}` like in Python format), which will be used by SPLASH to put the path to the input file(s) for post-processing. For example, if the command is `wc -l {significant_extendors_fasta}` SPLASH will first transform TSV file with significant anchors to a FASTA file with extendors, and the path to this file will be passed to a command in place of a placeholder. Supported placeholders are listed [here](#subsection-supported-placeholders),

- `test_run` [OPTIONAL] - if this field is defined SPLASH will, at the initial stage (before running anything time-consuming), run this command, and if it exits with a status different than 0, SPLASH will fail with an appropriate message. This may be useful, for example, to force SPLASH to test if proper software is installed and available on PATH. It is highly recommended to define this field because it may help the user understand that he/she needs to install this software (for example, blast).


## Supported placeholders
Below is the list of supported placeholders that may be used in `command`, assuming `--outname_prefix` of SPLASH run was the default (i.e., `result`):
 - `all_extendors_tsv`- will pass path to `result.after_correction.all_anchors.tsv`,
 - `significant_extendors_tsv`- will pass path to  `result.after_correction.scores.tsv`,
 - `top_target_entropy_extendors_tsv`- will pass path to `result.after_correction.scores.top_target_entropy.tsv`,
 - `top_effect_size_extendors_tsv`- will pass path to `result.after_correction.scores.top_effect_size_bin.tsv`,
 - `compactors_top_target_entropy_tsv` - will pass path to `result_compactors/after_correction.scores.top_target_entropy.tsv`,
 - `compactors_top_effect_size_bin_tsv`- will pass path to `result_compactors/after_correction.scores.top_effect_size_bin.tsv`,

 - `all_extendors_fasta`- the same as `all_extendors_tsv`, but SPLASH will extract extendors from TSV and store them in FASTA format,
 - `significant_extendors_fasta`- the same as `significant_extendors_tsv`, but SPLASH will extract extendors from TSV and store them in FASTA format,
 - `top_target_entropy_extendors_fasta`- the same as `top_target_entropy_extendors_tsv`, but SPLASH will extract extendors from TSV and store them in FASTA format,
 - `top_effect_size_extendors_fasta`- the same as `top_effect_size_extendors_tsv`, but SPLASH will extract extendors from TSV and store them in FASTA format,
 - `compactors_top_target_entropy_fasta`- the same as `compactors_top_target_entropy_tsv`, but SPLASH will extract compactors from TSV and store them in FASTA format,
 - `compactors_top_effect_size_bin_fasta` - the same as `compactors_top_effect_size_bin_tsv`, but SPLASH will extract compactors from TSV and store them in FASTA format.

# Failing post-processing
If one of the post-processing fails, it will not cause the whole SPLASH to fail but generate a warning message.

# Locations browsed by SPLASH to find JSON post-processing definition file
With `--postprocessing_item <path>`, one may define the absolute or relative path to the JSON post-processing definition file. If it's relative, it's relative to the current working directory, but if not found there, SPLASH will also check if it exists in the location relative to the main SPLASH script.
When SPLASH is installed, the content of the `postprocessing` directory is also copied to the install location.

# Example of JSON post-processing definition file
```
{
	"name": "blast",
	"test_run": "blastn -help",	
	"command": "blastn -outfmt \"6 qseqid stitle sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore sgi sacc slen staxids stitle\" -query {all_extendors_fasta} -remote -db nt -out blast_output.txt -evalue 0.01 -task blastn -dust no -word_size 24 -reward 1 -penalty -3 -max_target_seqs 5"
}
```
