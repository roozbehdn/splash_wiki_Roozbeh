# SPLASH

SPLASH is an unsupervised and reference-free unifying framework to discover **sample**-dependent **sequence** variation through statistical analysis of k-mer composition in both DNA and RNA sequence. 
**Sample** is general (it could be a barcode, a bulk RNA-seq sample or DNA-seq sample)
**Sequence** is general (it could be RNA, DNA or protein)


SPLASH leverages our observation that detecting sample-regulated sequence variation, such as alternative splicing, RNA editing, gene fusions, V(D)J, transposable element mobilization, allele-specific splicing, genetic variation in a population, and* many other regulated processes on DNA and RNA* can be characterized in terms of a criteria of kmers, without requiring a reference (Chaung et al. 2023, Cell). 

See "how to use SPLASH" in the right bar. 

SPLASH finds constant sequences (anchors) that are followed by a set of sequences (targets) with sample-specific target variation and provides valid p-values. SPLASH is reference-free, sidestepping the computational challenges associated with alignment and making it significantly faster and more efficient than alignment, and enabling discovery and statistical precision not currently available, even from pseudo-alignment.

The first version of [SPLASH](https://github.com/salzman-lab/nomad/) pipeline proved its usefulness. It was implemented mainly in Python with the use of NextFlow. Here we provide a new and improved implementation based in C++ and Python (Kokot et al. 2024). This new version is much more efficient and allows for the analysis of datasets >1TB size in hours on a workstation or even a laptop.

A key concept of SPLASH is the analysis of composition of pairs of substrings *anchor*&ndash;*target* across many samples.
The substrings can be adjacent in reads or can be separated by a *gap*.

The image below presents the SPLASH pipeline on a high-level.
![image](https://github.com/refresh-bio/SPLASH/assets/9378882/8210fee0-c877-4374-9938-e3c01ea69e76)

## References
[Scalable and unsupervised discovery from raw sequencing reads using SPLASH2](https://www.nature.com/articles/s41587-024-02381-2), Nature Biotechnology (2024), https://doi.org/10.1038/s41587-024-02381-2
 
Kaitlin Chaung, Tavor Baharav,  Ivan Zheludev, Julia Salzman. [A statistical, reference-free algorithm subsumes myriad problems in genome science and enables novel discovery](https://doi.org/10.1101/2022.06.24.497555), bioRxiv (2022)
 
Tavor Baharav, David Tse, and Julia Salzman. 
[An Interpretable, Finite Sample Valid Alternative to Pearson’s X2 for Scientific Discovery](https://www.biorxiv.org/content/10.1101/2023.03.16.533008), bioRxiv (2023)