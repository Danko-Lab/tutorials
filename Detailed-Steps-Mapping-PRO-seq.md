BioMG 7810 practical day 1: Mapping PRO-seq, GRO-seq, and ChRO-seq data.
========================================================================

Global Run-On and Sequencing (GRO-seq), Precision Run-On and Sequencing (PRO-seq), and Chromatin Run-On and Sequencing are technologies for mapping the location and orientation of actively transcribing RNA polymerase I, II, and III (Pol) across the genome.  All Run-on technologies provide a genome-wide readout of gene and lincRNA transcription, as well as the location and relative activities of active enhancers and promoters that regulate gene expression.

Learning goals
--------------

* Get comfortable in a LINUX command line environement.
* Practice mapping ChRO-seq data to a reference genome.
* Understand *WHY* we preform each step.

Finalize your BioHPC reservation
--------------------------------

Four of you had accounts. For those who did not, find the e-mail invitation from BioHPC (should have received this yesterday, Nov. 26th).
<img align="left" src="etc\BioHPCpwd.png" width="900">

Fig. 1: E-mail from BioHPC. Select the link and set your password.

User LabID is your netID. Please choose a password.

Log into the high performance compute server
--------------------------------------------

Secure shell (a.k.a. SSH) is a protocol that lets you log into a computer and control it remotely. We are logging into high performance computers (HPC) hosted by the BioHPC service, as part of the BioHPC cloud: https://biohpc.cornell.edu/Default.aspx 

An SSH clinet called "PuTTY" is installed on computers in B30B. 

* Select the Windows start menu and type "putty".

* Under Hostname, enter the host name assinged to you (see the Power Point slide). For example: 

```
cbsumm22.biohpc.cornell.edu
```

* Select Open and enter your username (you netID) and your password.

Hint: You can download PuTTY for your own computer from this URL: 
``` https://www.putty.org/ ```

Look at the raw data ChRO-seq data in fastq format
--------------------------------------------------

The next step is to use LINUX commands to navigate to the raw ChRO-seq data. I have added this to /workdir/data. To get there and view the data, please enter the following commands: 

``` 
[dankoc@cbsumm22 ~]$ cd /workdir/data/fastq
[dankoc@cbsumm22 fastq]$ ls -lha
total 1.9G
drwxrwxr-x 2 dankoc dankoc   35 Nov 26 19:05 .
drwxrwxr-x 4 dankoc dankoc   41 Nov 26 18:32 ..
-rw-rw-r-- 1 dankoc dankoc 1.9G Nov 26 19:06 LZ_R4.fastq.gz
```

New look into the fastq file using the "zless" LINUX command: 

```
[dankoc@cbsumm22 fastq]$ zless LZ_R4.fastq.gz
```

This opens a window which shows you the raw data!

```
@NS500503:579:HTMFNBGX3:1:11101:7482:1050 1:N:0:GATCAG
CGGGANGGTGACTGCAATGACATGCTGTTGGAATTCTCGGGTGCCAAGGAACTCCAGTCACGATCAGATCTCGTAT
+
AAAAA#EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEAEEEEEEEEEEAEEEEEEE6EEEEEE
@NS500503:579:HTMFNBGX3:1:11101:7717:1050 1:N:0:GATCAG
GTTAGNAAAGCAGGAGGATTATTTTTGGTAGCCTACTTAAATTCATGTTTTGCTTAGGTAACCATACAGTTGAGTG
+
AAAAA#EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEAEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEA
@NS500503:579:HTMFNBGX3:1:11101:17223:1050 1:N:0:GATCAG
CGAGANACATATGTGCTATGGCATGGAATTCTCGGGTGCCAAGGAACTCCAGTCACGATCAGATCTCGTATGCCGT
+
AAAAA#EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEAEEEEEEEE/EEEEEEEEEEEEEEAEEE<EEEEEEEEE6E
@NS500503:579:HTMFNBGX3:1:11101:18540:1052 1:N:0:GATCAG
CATAGNATAACCTATGCGTGACTCTCAGCACAGTGAATTTTGTTTGGAATTCTCGGGTGCCAAGGAACTCCAGTCA
+
AAAAA#EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEAAAE
@NS500503:579:HTMFNBGX3:1:11101:20114:1052 1:N:0:GATCAG
GGCTTNAGGTGCTCTGGTCCTTCCTCCAGTGTGTATGCTGGAATTCTCGGGTGCCAAGGAACTCCAGTCACGATCA
+
AAAAA#EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEAEEEEEEEEEEEAEEEEEEEEEEA
@NS500503:579:HTMFNBGX3:1:11101:5639:1052 1:N:0:GATCAG
CCCTGNCGAGCAAGCCGGGACATAAGCCAGGGACGGGGGAATTGGAATTCTCGGGTGCCAAGGAACTCCAGTCACG
+
AAAAA#EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE<
...
```

Note that you can press the space bar to see another window full of data. It would take you hours to browser the entire file.

Those DNA sequences should be similar to the mouse genome. To check this out, align them using BLAT: 
https://genome.ucsc.edu/cgi-bin/hgBlat

Be sure to change the genome to "mouse".

The second read is a good example: 
<img align="left" src="etc\BLAT1.png" width="900">
<img align="left" src="etc\BLAT2.png" width="900">

Notice that while some of the reads map, many don't map as-is. Moreover, manual inspection shows you two aspects of read mapping: 
* First, most reads start mapping at position 7 in the read.
* Second, many reads map only the 5' portion of the read.

What is your explanation?

Quality control reads
---------------------

To check the QC on these fastq files using the software program fastqc.

Note that this program will write a summary of the fastq qualities. You need to put this summary in a directory that we can write to. Permissions in LINUX frequently do not allow writing to directories with shared information (nor should they!). Create a working directory: 

```
[dankoc@cbsumm27 workdir]$ mkdir /workdir/dankoc
[dankoc@cbsumm27 workdir]$ cd /workdir/dankoc/
```

Then run fastqc:

```
fastqc $INPUT.fastq.gz
```

Download those files using filezilla, and open the window. It will looks something like this: 

Notice that many of the reads are contaminated with Illumina adapter! This indicates a short insert size in the input data. This is typical of ChRO-seq data, and many other types of short read data as well. We will need remove these before proceeding.

Demultiplex PCR duplicates
--------------------------

Something...


Trim adapters
-------------

We will trim adapters using the software program "cutadapt". To see how to use this program, call the program without any options: 

```
[dankoc@cbsumm22 data]$ cutadapt
cutadapt version 1.16
Copyright (C) 2010-2017 Marcel Martin <marcel.martin@scilifelab.se>

cutadapt removes adapter sequences from high-throughput sequencing reads.

Usage:
    cutadapt -a ADAPTER [options] [-o output.fastq] input.fastq

For paired-end reads:
    cutadapt -a ADAPT1 -A ADAPT2 [options] -o out1.fastq -p out2.fastq in1.fastq in2.fastq

Replace "ADAPTER" with the actual sequence of your 3' adapter. IUPAC wildcard
characters are supported. The reverse complement is *not* automatically
searched. All reads from input.fastq will be written to output.fastq with the
adapter sequence removed. Adapter matching is error-tolerant. Multiple adapter
sequences can be given (use further -a options), but only the best-matching
adapter will be removed.

Input may also be in FASTA format. Compressed input and output is supported and
auto-detected from the file name (.gz, .xz, .bz2). Use the file name '-' for
standard input/output. Without the -o option, output is sent to standard output.

Citation:

Marcel Martin. Cutadapt removes adapter sequences from high-throughput
sequencing reads. EMBnet.Journal, 17(1):10-12, May 2011.
http://dx.doi.org/10.14806/ej.17.1.200

Use "cutadapt --help" to see all command-line options.
See http://cutadapt.readthedocs.io/ for full documentation.

cutadapt: error: At least one parameter needed: name of a FASTA or FASTQ file.
```

To make cutadapt work, we need to specify the: 
* Input fastq file (input.fastq)
* Adapter sequence (-a ADAPTER)
* Output file (-o output.fastq)


Run this as follows: 

```
[dankoc@cbsumm22 data]$ cutadapt

```

Map short reads to the mouse genome
-----------------------------------

The next step is to map raw ChRO-seq data to the mouse genome. This is done using a software program that aligns short reads to a reference genome. 

We will use the software program BWA for this. To see the options type bwa: 

```
[dankoc@cbsumm22 data]$ bwa

Program: bwa (alignment via Burrows-Wheeler transformation)
Version: 0.7.13-r1126
Contact: Heng Li <lh3@sanger.ac.uk>

Usage:   bwa <command> [options]

Command: index         index sequences in the FASTA format
         mem           BWA-MEM algorithm
         fastmap       identify super-maximal exact matches
         pemerge       merge overlapping paired ends (EXPERIMENTAL)
         aln           gapped/ungapped alignment
         samse         generate alignment (single ended)
         sampe         generate alignment (paired ended)
         bwasw         BWA-SW for long queries

         shm           manage indices in shared memory
         fa2pac        convert FASTA to PAC format
         pac2bwt       generate BWT from PAC
         pac2bwtgen    alternative algorithm for generating BWT
         bwtupdate     update .bwt to the new format
         bwt2sa        generate SA from BWT and Occ

Note: To use BWA, you need to first index the genome with `bwa index'.
      There are three alignment algorithms in BWA: `mem', `bwasw', and
      `aln/samse/sampe'. If you are not sure which to use, try `bwa mem'
      first. Please `man ./bwa.1' for the manual.

```

Note that BWA works in two steps. First, we need to generate a compressed file that represents the mouse genome using very efficient machine language. I have placed a text copy of the mouse reference genome (version mm10) here: /workdir/mm10/mm10.fa

To do this, go to a directory that you can write to and create the index using bwa index: 

```
[dankoc@cbsumm27 mm10]$ cd /workdir/dankoc
[dankoc@cbsumm27 dankoc]$ bwa index /workdir/data/mm10/mm10.rRNA.fa.gz
[bwa_index] Pack FASTA... 32.40 sec
[bwa_index] Construct BWT for the packed sequence...
[BWTIncCreate] textLength=5461829546, availableWord=396314452
[BWTIncConstructFromPacked] 10 iterations done. 99999994 characters processed.
[BWTIncConstructFromPacked] 20 iterations done. 199999994 characters processed.
[BWTIncConstructFromPacked] 30 iterations done. 299999994 characters processed.
[BWTIncConstructFromPacked] 40 iterations done. 399999994 characters processed.
[BWTIncConstructFromPacked] 50 iterations done. 499999994 characters processed.
[BWTIncConstructFromPacked] 60 iterations done. 599999994 characters processed.
[BWTIncConstructFromPacked] 70 iterations done. 699999994 characters processed.
[BWTIncConstructFromPacked] 80 iterations done. 799999994 characters processed.
[BWTIncConstructFromPacked] 90 iterations done. 899999994 characters processed.
[BWTIncConstructFromPacked] 100 iterations done. 999999994 characters processed.
[BWTIncConstructFromPacked] 110 iterations done. 1099999994 characters processed.
[BWTIncConstructFromPacked] 120 iterations done. 1199999994 characters processed.
[BWTIncConstructFromPacked] 130 iterations done. 1299999994 characters processed.
[BWTIncConstructFromPacked] 140 iterations done. 1399999994 characters processed.
[BWTIncConstructFromPacked] 150 iterations done. 1499999994 characters processed.
[BWTIncConstructFromPacked] 160 iterations done. 1599999994 characters processed.
[BWTIncConstructFromPacked] 170 iterations done. 1699999994 characters processed.
[BWTIncConstructFromPacked] 180 iterations done. 1799999994 characters processed.
[BWTIncConstructFromPacked] 190 iterations done. 1899999994 characters processed.
[BWTIncConstructFromPacked] 200 iterations done. 1999999994 characters processed.
[BWTIncConstructFromPacked] 210 iterations done. 2099999994 characters processed.
[BWTIncConstructFromPacked] 220 iterations done. 2199999994 characters processed.
[BWTIncConstructFromPacked] 230 iterations done. 2299999994 characters processed.
[BWTIncConstructFromPacked] 240 iterations done. 2399999994 characters processed.
[BWTIncConstructFromPacked] 250 iterations done. 2499999994 characters processed.
[BWTIncConstructFromPacked] 260 iterations done. 2599999994 characters processed.
[BWTIncConstructFromPacked] 270 iterations done. 2699999994 characters processed.
[BWTIncConstructFromPacked] 280 iterations done. 2799999994 characters processed.
[BWTIncConstructFromPacked] 290 iterations done. 2899999994 characters processed.
[BWTIncConstructFromPacked] 300 iterations done. 2999999994 characters processed.
[BWTIncConstructFromPacked] 310 iterations done. 3099999994 characters processed.
[BWTIncConstructFromPacked] 320 iterations done. 3199999994 characters processed.
[BWTIncConstructFromPacked] 330 iterations done. 3299999994 characters processed.
[BWTIncConstructFromPacked] 340 iterations done. 3399999994 characters processed.
[BWTIncConstructFromPacked] 350 iterations done. 3499999994 characters processed.
[BWTIncConstructFromPacked] 360 iterations done. 3599999994 characters processed.
[BWTIncConstructFromPacked] 370 iterations done. 3699999994 characters processed.
[BWTIncConstructFromPacked] 380 iterations done. 3799999994 characters processed.
[BWTIncConstructFromPacked] 390 iterations done. 3899999994 characters processed.
[BWTIncConstructFromPacked] 400 iterations done. 3999999994 characters processed.
[BWTIncConstructFromPacked] 410 iterations done. 4099999994 characters processed.
[BWTIncConstructFromPacked] 420 iterations done. 4199999994 characters processed.
[BWTIncConstructFromPacked] 430 iterations done. 4299999994 characters processed.
[BWTIncConstructFromPacked] 440 iterations done. 4399999994 characters processed.
[BWTIncConstructFromPacked] 450 iterations done. 4499999994 characters processed.
[BWTIncConstructFromPacked] 460 iterations done. 4599999994 characters processed.
[BWTIncConstructFromPacked] 470 iterations done. 4699999994 characters processed.
[BWTIncConstructFromPacked] 480 iterations done. 4799899178 characters processed.
[BWTIncConstructFromPacked] 490 iterations done. 4892727610 characters processed.
[BWTIncConstructFromPacked] 500 iterations done. 4975229770 characters processed.
[BWTIncConstructFromPacked] 510 iterations done. 5048553898 characters processed.
[BWTIncConstructFromPacked] 520 iterations done. 5113720554 characters processed.
[BWTIncConstructFromPacked] 530 iterations done. 5171636842 characters processed.
[BWTIncConstructFromPacked] 540 iterations done. 5223108954 characters processed.
[BWTIncConstructFromPacked] 550 iterations done. 5268853482 characters processed.
[BWTIncConstructFromPacked] 560 iterations done. 5309507322 characters processed.
[BWTIncConstructFromPacked] 570 iterations done. 5345636538 characters processed.
[BWTIncConstructFromPacked] 580 iterations done. 5377744234 characters processed.
[BWTIncConstructFromPacked] 590 iterations done. 5406277626 characters processed.
[BWTIncConstructFromPacked] 600 iterations done. 5431634122 characters processed.
[BWTIncConstructFromPacked] 610 iterations done. 5454167018 characters processed.
[bwt_gen] Finished constructing BWT in 614 iterations.
[bwa_index] 2339.11 seconds elapse.
[bwa_index] Update BWT... 15.82 sec
[bwa_index] Pack forward-only FASTA... 24.65 sec
[bwa_index] Construct SA from BWT and Occ... 774.60 sec
[main] Version: 0.7.13-r1126
[main] CMD: bwa index /workdir/data/mm10/mm10.rRNA.fa.gz
[main] Real time: 3192.431 sec; CPU: 3186.585 sec
```

Next, align reads in the trimmed fastq.gz file to this mm10 reference genome: 

```
bwa aln
```

And convert the resulting alignments (.bai format) into a more standard BAM format: 

```
bwa samse
```

The BAM file represents the location of reads that map to the reference genome. That's it - you've done it!

Note: See the reference for BWA here: 
https://academic.oup.com/bioinformatics/article/25/14/1754/225615

Convert BAM files into bigWig format
------------------------------------

Much of what we will do tomorrow uses a more compact format, known as bigWig. BigWig is a binary format that represents each position in the genome with >0 counts, and the number of counts at that position.

To convert into a bigWig file, ...


Look at the mapped data using a genome browser
----------------------------------------------

Preference for IGV web start.


Notes/ thoughts
---------------

Note that for tasks that you do all the time, it is much *much* better to have a pipeline set up that automates each of these steps. The PRO-seq, GRO-seq, and ChRO-seq pipeline that we use in my lab can be found here: 
https://github.com/Danko-Lab/proseq2.0

This program is essentially just a "shell script" which automates the commands that you just put in manually. To use it, you just execut a single command which takes in all of the information and provides the location of the BAM and bigWig files at the end. 

