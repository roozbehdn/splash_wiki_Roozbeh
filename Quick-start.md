The easiest way to get SPLASH is to use [precompiled release](https://github.com/refresh-bio/SPLASH/releases).
For innstance, to get 2.6.1 revision, please run the following command:
```
curl -L https://github.com/refresh-bio/SPLASH/releases/download/v2.6.1/splash-2.6.1.linux.x64.tar.gz
```

Then, one can perform an example analysis on the data from viral RNA sequencing. The data consists of four Illumina NovaSeq 6000 runs from the study entitled "Detection and prevalence of SARS-CoV-2 co-infections during the Omicron variant circulation": 
```
cd example
./download.py #download examplary data
splash input.txt #run the pipeline with default parameters
```

The result consists of two TSV files, namely,
 1. `result.after_correction.all_anchors.tsv` 
 2. `result.after_correction.scores.tsv`
The first file contain all unfiltered anchors found by the pipeline.
The second file contains only anchors whose corrected p-value is below 0.05.
