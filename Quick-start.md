The easiest way to get SPLASH is to use [precompiled release](https://github.com/refresh-bio/SPLASH/releases).
For innstance, to get 2.6.1 revision, please run the following command:
```
curl -L https://github.com/refresh-bio/SPLASH/releases/download/v2.6.1/splash-2.6.1.linux.x64.tar.gz
```

Then, one can perform an example analysis on the data from viral RNA sequencing. The data consists of four Illumina NovaSeq 6000 runs from the study entitled "Detection and prevalence of SARS-CoV-2 co-infections during the Omicron variant circulation": 
```
gunzip splash-2.6.1.linux.x64.tar.gz
tar -xf splash-2.6.1.linux.x64.tar
cd splash-2.6.1.linux.x64
cd example
./download.py #download test data
splash input.txt #run the pipeline with default parameters on test data
```

If pipeline finishes successfully, you should see the following two TSV files in your run directory:
 1. `result.after_correction.all_anchors.tsv`  #output file containing all anchors found by the pipeline 
 2. `result.after_correction.scores.tsv`  #output file containing significant anchors
