# SPLASH

SPLASH is an unsupervised and reference-free unifying framework to discover **sample**-dependent **sequence** variation through statistical analysis of k-mer composition in both DNA and RNA sequence. 
**Sample** is general (it could be a barcode, a bulk RNA-seq sample, or DNA-seq sample).
**Sequence** is general (it could be RNA, DNA, or protein)


SPLASH leverages the unifying framework that detecting sample-regulated sequence variation, such as alternative splicing, RNA editing, gene fusions, V(D)J, transposable element mobilization, allele-specific splicing, genetic variation in a population, and many other regulated processes on DNA and RNA can be characterized by signature k-mers, without requiring a reference (Chaung et al. 2023, Cell). 

See "how to use SPLASH" in the right bar. 

SPLASH finds constant sequences *anchors* that are followed by a set of sequences *targets* with sample-specific target variation and provides valid p-values. The targets can be adjacent to anchors or can be separated by a *gap*. SPLASH is reference-free, sidestepping the computational challenges associated with alignment and making it significantly faster and more efficient than alignment, and enabling discovery and statistical precision not currently available, even from pseudo-alignment.

The first version of [SPLASH](https://github.com/salzman-lab/nomad/) pipeline proved its usefulness. It was implemented mainly in Python with the use of NextFlow. Here we provide a new and improved implementation based in C++ and Python (Kokot et al. 2024). This new version is much more efficient and allows for the analysis of datasets >1TB size in hours on a workstation or even a laptop.

The image below presents the SPLASH pipeline on a high level.
![image](https://github.com/refresh-bio/SPLASH/assets/9378882/8210fee0-c877-4374-9938-e3c01ea69e76)

## References
Marek Kokot*, Roozbeh Dehghannasiri*, Tavor Baharav, Julia Salzman, and Sebastian Deorowicz.
[Scalable and unsupervised discovery from raw sequencing reads using SPLASH2](https://www.nature.com/articles/s41587-024-02381-2), Nature Biotechnology (2024)

Roozbeh Dehghannasiri*, Marek Kokot*, Sebastian Deorowicz, and Julia Salzman. [sc-SPLASH provides ultra-efficient reference-free discovery in barcoded single-cell sequencing](https://doi.org/10.1101/2024.12.24.630263), bioRxiv (2024)

Kaitlin Chaung*, Tavor Baharav*, George Henderson, Ivan Zheludev, Peter Wang, and Julia Salzman. [SPLASH: A statistical, reference-free genomic algorithm unifies biological discovery](https://www.cell.com/cell/fulltext/S0092-8674(23)01179-0), Cell (2023)
 
Tavor Baharav, David Tse, and Julia Salzman. 
[OASIS: An interpretable, finite-sample valid alternative to Pearson’s X2 for scientific discovery](https://www.pnas.org/doi/10.1073/pnas.2304671121), PNAS (2024)

George Henderson, Adam Gudys, Tavor Baharav, Punit Sundaramurthy, Marek Kokot, Peter L. Wang, Sebastian Deorowicz, Allison F. Carey, and Julia Salzman.
[Ultra-efficient, unified discovery from microbial sequencing with SPLASH and precise statistical assembly](https://www.biorxiv.org/content/10.1101/2024.01.18.576133v1.full)
bioRxiv 2024.01.18.576133 (2024)
