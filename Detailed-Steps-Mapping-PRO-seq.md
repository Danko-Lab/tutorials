Detailed protocol for mapping PRO-seq, GRO-seq, and ChRO-seq data.
==================================================================

Global Run-On and Sequencing (GRO-seq), Precision Run-On and Sequencing (PRO-seq), and Chromatin Run-On and Sequencing are technologies for mapping the location and orientation of actively transcribing RNA polymerase I, II, and III (Pol) across the genome.  All Run-on technologies provide a genome-wide readout of gene and lincRNA transcription, as well as the location and relative activities of active enhancers and promoters that regulate gene expression.

<BR><BR>

Finalize your BioHPC reservation
--------------------------------

Four of you had accounts. For those who did not, find the e-mail invitation from BioHPC (should have received this yesterday, Nov. 26th).
<img align="left" src="etc\BioHPCpwd.png" width="900">

Fig. 1: E-mail from BioHPC. Select the link and set your password.

User LabID is your netID. Please choose a password.

<BR><BR>

Log into the high performance compute server
--------------------------------------------

Secure shell (a.k.a. SSH) is a protocol that lets you log into a computer and control it remotely. We are logging into high performance computers (HPC) hosted by the BioHPC service, as part of the BioHPC cloud: https://biohpc.cornell.edu/Default.aspx 

An SSH clinet called "PuTTY" is installed on computers in B30B. 

* Select the Windows start menu and type "putty".

* Under Hostname, enter the host name assinged to you (see the Power Point slide). For example: 

``` cbsumm22.biohpc.cornell.edu ```

* Select Open and enter your username (you netID) and your password.

Hint: You can download PuTTY for your own computer from this URL: 
``` https://www.putty.org/ ```

<BR><BR>

Look at the raw data ChRO-seq data in fastq format
--------------------------------------------------

The next step is to use LINUX commands to navigate to the raw ChRO-seq data. I have added this to /workdir/data. To get there and view the data, please enter the following commands: 

``` 
[dankoc@cbsumm22 data]$ cd /workdir/data/fastq
[dankoc@cbsumm22 data]$ ls -lha
total 12K
drwxrwxr-x  2 dankoc dankoc    6 Nov 26 15:45 .
drwxrwxrwx 12 root   root   8.0K Nov 26 15:45 ..
```

New look into the fastq file using the "zless" LINUX command: 

```
zless 
```

That shows you the raw data! Those DNA sequences should be similar to the mouse genome. To check this out, align them using BLAT: 
https://genome.ucsc.edu/cgi-bin/hgBlat

Be sure to change the genome to "mouse".

Notice that while some of the reads map, many don't map as-is. Why do you think this might be?

To check the QC on these fastq files using the software program fastqc.

```
fastqc $INPUT.fastq.gz
```

Download those files using filezilla, and open the window. It will looks something like this: 

Notice that many of the reads are contaminated with Illumina adapter! This indicates a short insert size in the input data. This is typical of ChRO-seq data, and many other types of short read data as well. We will need remove these before proceeding.

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

Map the data to the mouse genome using BWA
------------------------------------------

The next step is to map raw ChRO-seq data to the mouse genome. This is done using a software program that aligns short reads to the reference genome 

We will use the software program BWA for this. BWA 

```
bwa aln
```

Look at the raw data ChRO-seq data in fastq format
--------------------------------------------------

