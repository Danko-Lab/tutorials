Analyzing PRO-seq data.
=======================

Global Run-On and Sequencing (GRO-seq) and Precision Run-On and Sequencing (PRO-seq) are technologies for mapping 
the location and orientation of actively transcribing RNA polymerase I, II, and III (Pol) across the genome.  Both
technologies are particular powerful because they provide a genome-wide readout of gene and lincRNA transcription, 
as well as the location and relative activities of active enhancers and promoters that regulate transcription.

The Danko lab generally uses PRO-seq.  This tutorial describes the basic analysis pipeline used in the <a href="http://www.dankolab.org">Danko lab</a>.

Basic experimental design
-------------------------

<img align="left" src="etc/proseq.png" width="200">

GRO-seq and PRO-seq were developed by Leighton Core and Hojoong Kwak while working in John Lis' lab at Cornell.  A schematic of the PRO-seq protocol is shown in Fig. 1 (right).  PRO-seq begins using cell nuclei isolated from a cell population of interest (step #1).  RNA polymerases are halted by depleting rNTP monomers from the nuclei prep.  Engaged RNA polymerases incorporate a single biotin-labeled rNTP (step #2).   The run-on reaction is conducted in the presence of detergents which remove impediments to Pol II transcription and prevent new initiation. Incorporation of a biotin labeled nucleotide is thought to stall RNA polymerase after a single nucleotide is incorporated, resulting in single-nucleotide resolution of RNA polymerases across the genome.  Nascent RNAs are purified using streptavidin beads, amplified by PCR, and deep sequenced using Illumina technology (step #3).

<BR><BR>

Read mapping and data visualization
-----------------------------------

The Danko lab pipeline for aligning PRO-seq data can be found here: https://github.com/Danko-Lab/utils/tree/master/proseq.  The process for GRO-seq data is very similar in most respects, but does not require switching the strand of reads after mapping.  A pipeline version for this GRO-seq will be committed to GitHub soon.

To run our pipeline users must first download the script files and install dependencies indicated in the README.md.  Users are currently required to edit each .bsh script and prove paths to the chromInfo of the genome build, swap directory, and BWA index (this interface will become more user friendly soon).

Afterword, users enter a directory containing PRO-seq fastq files and type: 

``` > make ```

The script will operate three pre-processing options, including: 

1. Preprocessing reads to filter those which do not pass Illumina QC filters and trimming the sequencing adapter using cutadapt.

2. Mapping trimmed reads using BWA.

3. BAM files are converted into bedGraph and BigWig formats for downstream analysis and visualization. 

Identifying regulatory elements using dREG
------------------------------------------

Coming soon.

Working with data using the bigWig package for R
------------------------------------------------

Coming soon.

Transcription Unit Identification
---------------------------------

Coming soon.

Testing for changes between conditions
--------------------------------------

Coming soon.

Useful references
-----------------

* GRO-seq: http://www.sciencemag.org/content/322/5909/1845.long
* PRO-seq: http://www.ncbi.nlm.nih.gov/pmc/articles/PMC3974810/
* dREG: http://www.nature.com/nmeth/journal/v12/n5/full/nmeth.3329.html
