###############
# TMSEG
###############
Stable version: `2.2.0`
See Wiki for more info

Muriel Keribin & Cyril Duchon-Doris for Protein Prediction II WinterSemester 2015-2016

TMSEG predicts transmembrane proteins (TMP) and transmembrane helices (TMH) using position-specific scoring matrices (PSSM) generated by PSI-BLAST and the physico-chemical properties of the amino acids.

## HOWTO Install

### This repo uses git-lfs

Some files are very large (model files for ML algorithms), and have been committed using git-lfs*. It is mandatory to install the git-lfs when cloning/pulling the repo. 
Download and install [git-lfs](https://git-lfs.github.com/)
Then run `git lfs install` on each machine once. Then you don't need to worry about it (only if you wish to add/rename some files that you want to track with git-lfs).

*Git Large File Storage (LFS) replaces large files such as audio samples, videos, datasets, and graphics with text pointers inside Git, while storing the file contents on a remote server like GitHub.com or GitHub Enterprise.

### Installation

Optional : you can choose to skip this step and use the pre-packed tmseg.jar in the first `/src/` folder

An ANT script (build.xml) lets you compile the latest version of the program with
`ant`


## HOWTO RUN, Basics

Once pulled with git-lfs, you will find tmseg.jar in the first /src/ folder
This jar can easily be executed with java with some examples provided in the /example folder:

```shell
cd src/ 
java -jar tmseg.jar -i examples/query.fasta -p examples/query.pssm -o tests/query.out
cat tests/query.out
```

The project can be loaded from eclipse (a .project and .classpath were added by us)

Because of JAVA portability, the program works under any OS that can run a JVM. Java 1.7 and 1.8 are reported to work fine.

## Method Description

### Authors

Michael Bernhofer (1)
Edda Kloppmann (1,2)
Jonas Reeb (1)
Burkhard Rost (1,2,3,4)

1. Department of Informatics & Center for Bioinformatics & Computational Biology – i12, Technische Universität München (TUM), Boltzmannstr. 3, 85748 Garching/Munich, Germany
2. New York Consortium on Membrane Protein Structure, New York Structural Biology Center, 89 Convent Avenue, New York, NY 10027
3. Institute of Advanced Study (TUM-IAS), Lichtenbergstr. 2a, 85748 Garching/Munich, Germany
4. Institute for Food and Plant Sciences WZW – Weihenstephan, Alte Akademie 8, Freising, Germany

First version of the program : work started in 2014, first commit on 09/02/2014, last one on 01/03/2014
A second version (TMSEG 2) was released by Michael in november 2015 on https://github.com/BernhoferM/TMSEG2. The repo was then forked to Rostlab/ namespace and work should continue from there

Michael Bernhofer has implemented the method in this java program TMSEG

### Publications

The paper is not yet published (currently in progress).

### Training/Test Data

Will be detailed once the article is published

### Programming Language

The program itself is coded in java and exported in a .jar file. Source files available under src/

### Machine learning methods

The prediction is divided into three steps performed by three different classifiers. 

 *  Random Forest (RF) decision trees
   *  predicts the probability of each residues to be in one of three states: transmembrane, soluble, and signal peptide. The RF uses a sliding window of 19 residues for the PSSM scores and 9 residues for the physico-chemical properties (charge, hydrophobicity, polarity). The protein sequence is then divided into transmembrane and soluble segments (and signal peptide, if applicable) based on the probabilities. 
   * predicts the inside/outside topology of the N-terminus. The prediction is based on the amino acid composition and positive charge of the residues on the two sides of the membrane (separated by the TMHs). 
 *  Neural Network (NN) refines the prediction by adjusting the position of the TMHs or potentially splitting very long TMHs (>36 residues). This NN is specifically trained on the length, amino acid composition, and physico-chemical properties of TMHs. 

## EVALUATION

### Performance

TMSEG was compared to three established methods: PolyPhobius [1], MEMSAT3 [2], MEMSAT-SVM [3], and PHDhtm [4]. Its performance was at least comparable to and often better than the other three methods (unpublished data). The evaluation was performed on a dataset with 41 transmembrane proteins and 285 soluble proteins. The PSSM profiles were generated by running PSI-BLAST against the UniProt [5] Reference Cluster with 90% sequence identity (UniRef90).

TMSEG correctly identified 98±2% of the transmembrane proteins (40 out of 41 TMPs) and had a false positive rate of only 3±1% (8 out of 285 soluble proteins). Transmembrane helices were predicted with a precision of 87±4% and recall of 85±4%, and 66±7% of all transmembrane proteins were predicted with all their helices at the correct positions (i.e. no false positives/negatives).

A predicted helix was considered to be correct if its end-points did not deviate by more than five residues from the observed helix, and if the overlap between the predicted and observed helix was at least half of the length of the longer helix.

### Limitations

TMSEG uses only the PSI-BLAST PSSM scores and features derived from those scores. Therefore, the quality of the prediction strongly depends on the quality of the PSSM. In order to estimate the effect of the database size on the prediction accuracy, PSSMs from a PSI-BLAST run against the UniRef50 Cluster and Swiss-Prot were used.

These PSSMs mainly affected the recall of transmembrane proteins and helices. The protein recall dropped to 95% (UniRef50) and 90% (Swiss-Prot), and the helix recall to 79% (UniRef50) and 77% (Swiss-Prot). The precision of the transmembrane helices dropped to 83% (UniRef50) and 82% (Swiss-Prot), and the percentage of transmembrane proteins with all helices at their correct positions was only 59% (UniRef50) and 49% (Swiss-Prot). However, the false positive rate (i.e. soluble proteins predicted as transmembrane proteins) was mostly unaffected and remained at 3% (UniRef50) and 2% (Swiss-Prot).

## HOW-TO Extended
 
### Inputs/Output arguments and flags

* IN -i : FASTA file (only amino acids sequences)
* IN -p : PSSM Matrix file generated by PSI-BLAST

* OUT -o : Human readable file
* OUT -r : Raw prediction scores

* FLAG -m : Multi-job (process whole folder of PSSM/FASTA)
* FLAG -x : Process previous prediction - Adjust (requires FASTA)
* FLAG -t : Only perform topology prediction

## References :

[1] L. Käll, A. Krogh, and E. L. Sonnhammer. An HMM posterior decoder for sequence feature prediction that includes homology information. Bioinformatics, 21 Suppl 1:i251–257, Jun 2005. [DOI:10.1093/bioinformatics/bti1014] [PubMed:15961464]

[2] D. T. Jones. Improving the accuracy of transmembrane protein topology prediction using evolutionary information Bioinformatics, 23(5):538–544, Mar 2007. [DOI:10.1093/bioinformatics/btl677] [PubMed:17237066]

[3] T Nugent, D. T. Jones. Transmembrane protein topology prediction using support vector machines. BMC Bioinformatics 2009;10:159. [DOI:10.1186/1471-2105-10-159] [PubMed:19470175]

[4] B. Rost, P. Fariselli, and R. Casadio. Topology prediction for helical transmembrane proteins at 86% accuracy. Protein Sci., 5(8):1704–1718, Aug 1996. [DOI:10.1002/pro.5560050824] [PubMed:8844859] [PubMed Central:PMC2143485]

[5] UniProt C. UniProt: a hub for protein information. Nucleic Acids Res. 2015, 43:D204-212. [DOI:10.1093/nar/gku989] [PubMed:25348405] [PubMed Central:PMC4384041]

## Other notes :

Original wiki page : https://rostlab.org/owiki/index.php/Tmseg
Original repo (forked from) TMSEGv2 : https://github.com/BernhoferM/TMSEG2
