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

User LabID is your netID. Please choose a password following the instructions on the web browser.

Log into the high performance compute server
--------------------------------------------

Secure shell (a.k.a. SSH) is a protocol that lets you log into a computer and control it remotely. We are logging into high performance computers (HPC) hosted by the BioHPC service, as part of the BioHPC cloud: https://biohpc.cornell.edu/Default.aspx 

An SSH clinet called "PuTTY" is installed on computers in B30B. 

* Select the Windows start menu and type "putty".

* Under Hostname, enter the host name assinged to you (see the table below), followed by .biohpc.cornell.edu 

* Select Open and enter your username (you netID) and your password.

Server assignments are:  <BR>
cbsumm22:	apb248 mc2698 agc94 ad986 <BR>
cbsumm23:	yj386 chk63 mgl77 mip25 <BR>
cbsumm26:	is372 mvs44 ldw64 mjg75 <BR>
cbsumm27:	kmw264 yx438 hz543 <BR>

If you are assigned cbsumm22, for example, type: 
```
cbsumm22.biohpc.cornell.edu
```

Hint: You can download PuTTY for your own computer from this URL: https://www.putty.org/. If you have a Mac, you can use the command line utility. Look for tutorials online.

Look at the raw ChRO-seq data in fastq format
---------------------------------------------

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

This opens a window which shows you the raw data:

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

Why do you think this is?

Quality control reads
---------------------

To check the QC on these fastq files using the software program fastqc.

Note that this program will write a summary of the fastq qualities. You need to put this summary in a directory that we can write to. Permissions in LINUX frequently do not allow writing to directories with shared information (nor should they!). Create a working directory: 

```
[dankoc@cbsumm27 workdir]$ mkdir /workdir/dankoc
[dankoc@cbsumm27 workdir]$ cd /workdir/dankoc/
```

Note: instead of using /workdir/dankoc/ you should name the directory /workdir/{your netID}/. Make sure to change the commands throughout to write to your own folder instead of dankoc!!

Then run fastqc:

```
[dankoc@cbsumm27 dankoc]$ fastqc -o /workdir/dankoc /workdir/data/fastq/LZ_R4.fastq.gz
Started analysis of LZ_R4.fastq.gz
Approx 5% complete for LZ_R4.fastq.gz
Approx 10% complete for LZ_R4.fastq.gz
Approx 15% complete for LZ_R4.fastq.gz
Approx 20% complete for LZ_R4.fastq.gz
Approx 25% complete for LZ_R4.fastq.gz
Approx 30% complete for LZ_R4.fastq.gz
Approx 35% complete for LZ_R4.fastq.gz
Approx 40% complete for LZ_R4.fastq.gz
Approx 45% complete for LZ_R4.fastq.gz
Approx 50% complete for LZ_R4.fastq.gz
Approx 55% complete for LZ_R4.fastq.gz
Approx 60% complete for LZ_R4.fastq.gz
Approx 65% complete for LZ_R4.fastq.gz
Approx 70% complete for LZ_R4.fastq.gz
Approx 75% complete for LZ_R4.fastq.gz
Approx 80% complete for LZ_R4.fastq.gz
Approx 85% complete for LZ_R4.fastq.gz
Approx 90% complete for LZ_R4.fastq.gz
Approx 95% complete for LZ_R4.fastq.gz
Analysis complete for LZ_R4.fastq.gz
```

To see the fastqc analysis, download those files using a secure file transfer protocol (SFTP) clinet. The client "filezilla" is on the computers in Mann Library B30B. Select start, type filezilla, and set filezilla to connect to your assigned server. Note that you have to use the SFTP protocol. 

Note also that I have not yet gone to Mann library to try this step.

When you view the fastqc file, it will looks something like this: 

<img align="left" src="etc\FastQC.png" width="900">

The base qualities represented in that graph look excellent (our sequencing core typically does a nice job). 

Scroll down and note the fraction of reads contaminated with adapters: 

<img align="left" src="etc\FastQC-ac.png" width="900">

A lot of PRO-seq-type data (and other short read data too) looks like this. There's a lot of relatively shoter inserts in the library, and Illumina NextSeq500 has a very strong bias for shorter fragments. Whatever you think your insert size distribution looks like, Illumina will sequence the shorter fragments first!

Therefore we will have to trim adapters. Note that if we did not already know what sequence to trim, we could use the adapter identified by fastqc!

Demultiplex PCR duplicates
--------------------------

The first step in processing ChRO-seq data is to demultiplex PCR duplicates. I demultiplex using prinseq-lite.pl.

First, you have to copy prinseq-lite.pl into your working directory.

```
cp /workdir/data/prinseq-lite.pl /workdir/dankoc/
```

Then, you can use the various prinseq-lite options to preform the demultiplexing, and trim off the first 6 bases from the read. 

Note that what follows is a complicated set of instructions. This program is a bit archaic, so I'm not going to go into much detail explaining this line. If you don't understand this in detail, don't fret! There's better examples coming that you should be better equipped to understand in their entirety. 

```
cd /workdir/dankoc
zcat /workdir/data/fastq/LZ_R4.fastq.gz | ./prinseq-lite.pl -derep 1 -fastq stdin -out_format 3 -out_good stdout -out_bad null 2> /workdir/dankoc/pcr_dups.txt | \
	./prinseq-lite.pl -trim_left 6 -fastq stdin -out_format 3 -out_good stdout -out_bad null -min_len 15 | gzip > /workdir/dankoc/LZ_R4.no-PCR-dups.fastq.gz 
Input and filter stats:
        Input sequences: 42,740,174
        Input bases: 3,248,253,224
        Input mean length: 76.00
        Good sequences: 42,740,174 (100.00%)
        Good bases: 2,991,812,180
        Good mean length: 70.00
        Bad sequences: 0 (0.00%)
        Sequences filtered by specified parameters:
        none
```

That line should create a new .fastq.gz file in your working directory (mine is: /workdir/dankoc/). It also created a text file, pcr_dups.txt, which contains information about how many PCR duplicates were identified and removed: 

```
[dankoc@cbsumm27 dankoc]$ cat pcr_dups.txt
Input and filter stats:
        Input sequences: 47,534,859
        Input bases: 3,612,649,284
        Input mean length: 76.00
        Good sequences: 42,740,174 (89.91%)
        Good bases: 3,248,253,224
        Good mean length: 76.00
        Bad sequences: 4,794,685 (10.09%)
        Bad bases: 364,396,060
        Bad mean length: 76.00
        Sequences filtered by specified parameters:
        derep: 4794685
```

The output fastq file (LZ_R4.no-PCR-dups.fastq.gz) can be used in the next step!

Trim adapters
-------------

Our next goal is to trim adapters, and leaving all non-adapter sequence untouched. We will trim adapters using the software program "cutadapt". 

To see how to use this program, call the program without any options: 

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
[dankoc@cbsumm22 data]$ cutadapt -a TGGAATTCTCGGGTGCCAAGG -z -e 0.10 --minimum-length=15 --output=LZ_R4.no-PCR-dups.no-Adapters.fastq.gz LZ_R4.no-PCR-dups.fastq.gz
This is cutadapt 1.16 with Python 3.6.1
Command line parameters: -a TGGAATTCTCGGGTGCCAAGG -z -e 0.10 --minimum-length=15 --output=LZ_R4.no-PCR-dups.no-Adapters.fastq.gz LZ_R4.no-PCR-dups.fastq.gz
Running on 1 core
Trimming 1 adapter with at most 10.0% errors in single-end mode ...
Finished in 888.86 s (21 us/read; 2.89 M reads/minute).

=== Summary ===

Total reads processed:              42,740,174
Reads with adapters:                39,544,375 (92.5%)
Reads that were too short:           1,287,179 (3.0%)
Reads written (passing filters):    41,452,995 (97.0%)

Total basepairs processed: 2,991,812,180 bp
Total written (filtered):  1,530,434,687 bp (51.2%)

=== Adapter 1 ===

Sequence: TGGAATTCTCGGGTGCCAAGG; Type: regular 3'; Length: 21; Trimmed: 39544375 times.

No. of allowed errors:
0-9 bp: 0; 10-19 bp: 1; 20-21 bp: 2

Bases preceding removed adapters:
  A: 14.5%
  C: 50.0%
  G: 8.9%
  T: 26.3%
  none/other: 0.3%

Overview of removed sequences
length  count   expect  max.err error counts
3       257830  667815.2        0       257830
4       222128  166953.8        0       222128
5       221401  41738.5 0       221401
6       225189  10434.6 0       225189
7       236872  2608.7  0       236872
8       240668  652.2   0       240668
9       265207  163.0   0       263620 1587
10      285301  40.8    1       275947 9354
11      300955  10.2    1       290697 10258
12      317065  2.5     1       306226 10839
13      336865  0.6     1       324269 12596
14      353719  0.2     1       339814 13905
15      368055  0.0     1       351799 16256
16      390184  0.0     1       373033 17151
17      409857  0.0     1       390850 19007
18      428369  0.0     1       408019 20220 130
19      450155  0.0     1       427985 21864 306
20      471537  0.0     2       441747 24955 4835
21      493105  0.0     2       463368 25061 4676
22      522386  0.0     2       492115 25756 4515
23      545160  0.0     2       514271 26394 4495
24      563192  0.0     2       531642 27057 4493
25      593036  0.0     2       559794 28482 4760
26      619266  0.0     2       585012 29415 4839
27      639210  0.0     2       604625 29922 4663
28      666983  0.0     2       630913 31177 4893
29      698123  0.0     2       660362 32627 5134
30      725655  0.0     2       686379 33901 5375
31      751377  0.0     2       712010 34325 5042
32      765386  0.0     2       725969 34396 5021
33      784165  0.0     2       744621 34554 4990
34      820110  0.0     2       780207 35181 4722
35      850916  0.0     2       809539 36314 5063
36      890172  0.0     2       847372 37944 4856
37      942634  0.0     2       897212 40301 5121
38      986344  0.0     2       936554 43934 5856
39      1028666 0.0     2       978616 44129 5921
40      1079438 0.0     2       1026400 46842 6196
41      1135827 0.0     2       1080585 48915 6327
42      1216544 0.0     2       1159792 50528 6224
43      1310655 0.0     2       1249154 54866 6635
44      1405163 0.0     2       1339787 58293 7083
45      1479306 0.0     2       1412826 59730 6750
46      1553448 0.0     2       1479859 65533 8056
47      1612051 0.0     2       1536252 67783 8016
48      1604135 0.0     2       1530348 66045 7742
49      1576522 0.0     2       1504822 64359 7341
50      1459342 0.0     2       1393907 58885 6550
51      1208041 0.0     2       1154098 48606 5337
52      877897  0.0     2       838192 35883 3822
53      546745  0.0     2       522130 22382 2233
54      310862  0.0     2       296835 12705 1322
55      213977  0.0     2       204002 8922 1053
56      186416  0.0     2       177561 7988 867
57      163987  0.0     2       156562 6673 752
58      139292  0.0     2       133083 5571 638
59      123801  0.0     2       118571 4734 496
60      108009  0.0     2       103273 4307 429
61      108722  0.0     2       104057 4210 455
62      103555  0.0     2       99258 3933 364
63      91135   0.0     2       87329 3485 321
64      71053   0.0     2       67868 2904 281
65      45819   0.0     2       43809 1816 194
66      20209   0.0     2       19387 754 68
67      10985   0.0     2       10462 485 38
68      5114    0.0     2       4841 250 23
69      7818    0.0     2       7241 533 44
70      101264  0.0     2       63611 32321 5332

```

This produces a new fastq.gz file in your working directory (LZ_R4.no-PCR-dups.no-Adapters.fastq.gz). Look at this file using zless, and you'll see that reads are all sorts of different lengths. 

```
[dankoc@cbsumm27 dankoc]$ zless LZ_R4.no-PCR-dups.no-Adapters.fastq.gz

@NS500503:579:HTMFNBGX3:1:11101:7482:1050 1:N:0:GATCAG
GGTGACTGCAATGACATGCTGT
+NS500503:579:HTMFNBGX3:1:11101:7482:1050 1:N:0:GATCAG
EEEEEEEEEEEEEEEEEEEEEE
@NS500503:579:HTMFNBGX3:1:11101:7717:1050 1:N:0:GATCAG
AAAGCAGGAGGATTATTTTTGGTAGCCTACTTAAATTCATGTTTTGCTTAGGTAACCATACAGTTGAGTG
+NS500503:579:HTMFNBGX3:1:11101:7717:1050 1:N:0:GATCAG
EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEAEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEA
@NS500503:579:HTMFNBGX3:1:11101:17223:1050 1:N:0:GATCAG
ACATATGTGCTATGGCA
+NS500503:579:HTMFNBGX3:1:11101:17223:1050 1:N:0:GATCAG
EEEEEEEEEEEEEEEEE
@NS500503:579:HTMFNBGX3:1:11101:18540:1052 1:N:0:GATCAG
ATAACCTATGCGTGACTCTCAGCACAGTGAATTTTGTT
+NS500503:579:HTMFNBGX3:1:11101:18540:1052 1:N:0:GATCAG
EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE
@NS500503:579:HTMFNBGX3:1:11101:20114:1052 1:N:0:GATCAG
AGGTGCTCTGGTCCTTCCTCCAGTGTGTATGC
+NS500503:579:HTMFNBGX3:1:11101:20114:1052 1:N:0:GATCAG
EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE
@NS500503:579:HTMFNBGX3:1:11101:5639:1052 1:N:0:GATCAG
CGAGCAAGCCGGGACATAAGCCAGGGACGGGGGAAT
+NS500503:579:HTMFNBGX3:1:11101:5639:1052 1:N:0:GATCAG
EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE
@NS500503:579:HTMFNBGX3:1:11101:16452:1052 1:N:0:GATCAG
CTCAGACCCCAGAACTGTACACTAATAACTGTGTATTAATTTC
+NS500503:579:HTMFNBGX3:1:11101:16452:1052 1:N:0:GATCAG
EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE<EEE
@NS500503:579:HTMFNBGX3:1:11101:15727:1053 1:N:0:GATCAG
TTGGCTTCAGAAAAGTCAAACTCTAAGAAAATGTCATTGAGTCATTAAGCAAAGTTGAGTGCTTTAAAAT
+NS500503:579:HTMFNBGX3:1:11101:15727:1053 1:N:0:GATCAG
EEE/AEEEEEEAEEE/EEE6AEEEEEEEEEEEEEAEEE/E/EEEEEEE/6EEA//E/E/E/A//E/EEE/
@NS500503:579:HTMFNBGX3:1:11101:11864:1053 1:N:0:GATCAG
AATGGTATTTATTTTAAGGATCTGTTTAATAGTGCAGTTATCACAGAGTGTCTTCAGAAATGGCCAATAA
+NS500503:579:HTMFNBGX3:1:11101:11864:1053 1:N:0:GATCAG
EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEAEEEEEEEEEEAEEEEEEEEAEEEEEEEEEEEE/A
@NS500503:579:HTMFNBGX3:1:11101:19410:1053 1:N:0:GATCAG
ACGCCTCATTTGCATGTTCCTCATCTGATTGGCTACTCTCTCTCTGTACCTCACAGAGCCT
+NS500503:579:HTMFNBGX3:1:11101:19410:1053 1:N:0:GATCAG
EEAEEEAEEEEEEEEEEEEEEEAEEEEE/EAEEEE/EAEEAEEEAEEEE6EAEEE<//AAE
@NS500503:579:HTMFNBGX3:1:11101:24932:1053 1:N:0:GATCAG
AGTCTTTCCCGGTAACAAGAGGC
+NS500503:579:HTMFNBGX3:1:11101:24932:1053 1:N:0:GATCAG
EEEEEEEAEEEEEEEEEEEEEEE
@NS500503:579:HTMFNBGX3:1:11101:8810:1053 1:N:0:GATCAG
ATTTCTAAGCCATTGGACCTCCA
+NS500503:579:HTMFNBGX3:1:11101:8810:1053 1:N:0:GATCAG
EEEEEEEEEEEEEEEEEEEEEEE
@NS500503:579:HTMFNBGX3:1:11101:25219:1054 1:N:0:GATCAG
CAGGTTGTGGGTAGCTCACACAGATGGCATACGATCTCTCA
+NS500503:579:HTMFNBGX3:1:11101:25219:1054 1:N:0:GATCAG
EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEAEEEEEEEE
@NS500503:579:HTMFNBGX3:1:11101:1821:1054 1:N:0:GATCAG
ACTTCATAAATGTAACTGTTGGTATTTGGTGTCT
+NS500503:579:HTMFNBGX3:1:11101:1821:1054 1:N:0:GATCAG
EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE
@NS500503:579:HTMFNBGX3:1:11101:2853:1054 1:N:0:GATCAG
CGCTGCGATCTATTGAAAGTCAGCCCTCGACACAAGGGTTTGT
+NS500503:579:HTMFNBGX3:1:11101:2853:1054 1:N:0:GATCAG
EEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE
@NS500503:579:HTMFNBGX3:1:11101:3325:1054 1:N:0:GATCAG
GATTTGGGGAAGGGCTGTTGCAAGATTAC
+NS500503:579:HTMFNBGX3:1:11101:3325:1054 1:N:0:GATCAG
EEEEEEEEEEEEEEEEEEEEEEEEEEEEE
@NS500503:579:HTMFNBGX3:1:11101:20618:1054 1:N:0:GATCAG
AGACCATATGCCAAAAACTTGAACATTAAAGAAAAAAAGATTAAGATTGTATTACATTAGGCTAGGCACG
+NS500503:579:HTMFNBGX3:1:11101:20618:1054 1:N:0:GATCAG
EEAEEEEEEEEEEEEEEEEEEEEEEEEAEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEE
```

This has trimmed off the adapter sequence, leaving only the insert. It's now ready to align! Nice!

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

BWA works in two steps. First, we need to generate a compressed file that represents the mouse genome using very efficient machine language. This compressed representation of the genome is called an "index".

I have placed a text copy of the mouse reference genome (version mm10) here: /workdir/data/mm10/mm10.rRNA.fa.gz

Note that BWA does not allow you to adjust the output directory of this file, so you have to copy it to your working directory where you have permission to write the index.

Use the following commands: 

```
[dankoc@cbsumm27 mm10]$ cd /workdir/data/mm10
[dankoc@cbsumm27 mm10]$ cp mm10.rRNA.fa.gz /workdir/dankoc
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
[dankoc@cbsumm27 dankoc]$ ls -lha mm10.rRNA.fa*
-rw-rw-r-- 1 dankoc dankoc 830M Nov 26 18:36 mm10.rRNA.fa.gz
-rw-rw-r-- 1 dankoc dankoc  12K Nov 26 19:23 mm10.rRNA.fa.gz.amb
-rw-rw-r-- 1 dankoc dankoc 2.9K Nov 26 19:23 mm10.rRNA.fa.gz.ann
-rw-rw-r-- 1 dankoc dankoc 2.6G Nov 26 19:23 mm10.rRNA.fa.gz.bwt
-rw-rw-r-- 1 dankoc dankoc 652M Nov 26 19:23 mm10.rRNA.fa.gz.pac
-rw-rw-r-- 1 dankoc dankoc 1.3G Nov 26 19:36 mm10.rRNA.fa.gz.sa
```

Note the addition of five additional files, some of which are larger than the fastq file. These files are the index, representing the mouse genome in very effecient machine langugae. 

You will refer to this genome index in subsequent commands as "mm10.rRNA.fa.gz". Note that the fastq file is not a part of the index. You can now delete your private copy if you wish.

Next, align reads in the trimmed fastq.gz file to this mm10 reference genome: 

```
[dankoc@cbsumm27 dankoc]$ bwa aln -t 10 mm10.rRNA.fa.gz LZ_R4.no-PCR-dups.no-Adapters.fastq.gz > LZ_R4.sai
[bwa_aln] 17bp reads: max_diff = 2
[bwa_aln] 38bp reads: max_diff = 3
[bwa_aln] 64bp reads: max_diff = 4
[bwa_aln] 93bp reads: max_diff = 5
[bwa_aln] 124bp reads: max_diff = 6
[bwa_aln] 157bp reads: max_diff = 7
[bwa_aln] 190bp reads: max_diff = 8
[bwa_aln] 225bp reads: max_diff = 9
[bwa_aln_core] calculate SA coordinate... 43.04 sec
[bwa_aln_core] write to the disk... 0.05 sec
[bwa_aln_core] 262144 sequences have been processed.
[bwa_aln_core] calculate SA coordinate... 44.20 sec
[bwa_aln_core] write to the disk... 0.05 sec
[bwa_aln_core] 524288 sequences have been processed.
... [MANY MORE LINES LIKE THIS]
[bwa_aln_core] calculate SA coordinate... 40.86 sec              
[bwa_aln_core] write to the disk... 0.07 sec                     
[bwa_aln_core] 41418752 sequences have been processed.           
[bwa_aln_core] calculate SA coordinate... 5.02 sec               
[bwa_aln_core] write to the disk... 0.01 sec                     
[bwa_aln_core] 41452995 sequences have been processed.           
[main] Version: 0.7.13-r1126
[main] CMD: bwa aln -t 10 mm10.rRNA.fa.gz LZ_R4.no-PCR-dups.no-Adapters.fastq.gz
[main] Real time: 824.319 sec; CPU: 6761.728 sec
```

And convert the resulting alignments (.bai format) into a more standard BAM format: 

```
[dankoc@cbsumm27 dankoc]$ bwa samse -n 1 -f LZ_R4.sam mm10.rRNA.fa.gz LZ_R4.sai LZ_R4.no-PCR-dups.no-Adapters.fastq.gz
[bwa_aln_core] convert to sequence coordinate... 4.35 sec
[bwa_aln_core] refine gapped alignments... 0.63 sec      
[bwa_aln_core] print alignments... 0.35 sec              
[bwa_aln_core] 262144 sequences have been processed.     
[bwa_aln_core] convert to sequence coordinate... 4.18 sec
[bwa_aln_core] refine gapped alignments... 0.61 sec
[bwa_aln_core] print alignments... 0.34 sec
[bwa_aln_core] 524288 sequences have been processed.
[bwa_aln_core] convert to sequence coordinate... 4.03 sec
[bwa_aln_core] refine gapped alignments... 0.60 sec
[bwa_aln_core] print alignments... 0.35 sec
[bwa_aln_core] 786432 sequences have been processed.
[bwa_aln_core] convert to sequence coordinate... 4.00 sec
...
[main] Version: 0.7.13-r1126                                                                                 
[main] CMD: bwa samse -n 1 -f LZ_R4.sam mm10.rRNA.fa.gz LZ_R4.sai LZ_R4.no-PCR-dups.no-Adapters.fastq.gz     
[main] Real time: 878.276 sec; CPU: 878.285 sec                                                              
```

The output of this is a SAM file. See here for details on the SAM specification: https://en.wikipedia.org/wiki/SAM_(file_format)

Note that SAM files are human readable text files, and are therefore very inefficient for long term storage. In the next step we will convert this SAM file into a machine-readable (but not human readable) file format called BAM. We will then sort the BAM file for downstream processing. Both of these steps are completed using a program called samtools.

```
[dankoc@cbsumm27 dankoc]$ samtools view -b -S LZ_R4.sam > LZ_R4.bam
[dankoc@cbsumm27 dankoc]$ samtools sort -@ 10 LZ_R4.bam -o LZ_R4.sort.bam
[bam_sort_core] merging from 10 files and 10 in-memory blocks...                                             |
```

Notice how much larger the SAM file is than the BAMs: 

```
[dankoc@cbsumm27 dankoc]$ ls -lha *.bam
-rw-rw-r-- 1 dankoc dankoc 1.9G Nov 27 12:34 LZ_R4.bam
-rw-rw-r-- 1 dankoc dankoc 1.6G Nov 27 12:38 LZ_R4.sort.bam
[dankoc@cbsumm27 dankoc]$ ls -lha *.sam                                                                      |
-rw-rw-r-- 1 dankoc dankoc 7.9G Nov 27 12:25 LZ_R4.sam
```

The BAM file represents the location of reads that map to the reference genome. That's it - you've done it!

Note: See the reference for BWA here: 
https://academic.oup.com/bioinformatics/article/25/14/1754/225615

Convert BAM files into bigWig format
------------------------------------

Much of what we will do tomorrow uses a more compact format, known as bigWig. BigWig is a binary format that represents each position in the genome with >0 counts, and the number of counts at that position. 

Several steps To convert into a bigWig file, ...

```
[dankoc@cbsumm27 dankoc]$ bedtools bamtobed -i LZ_R4.sort.bam | awk 'BEGIN{OFS="\t"} ($5 > 20){print $0}' | grep -v "rRNA" | \
 awk 'BEGIN{OFS="\t"} ($6 == "+") {print $1,$2,$2+1,$4,$5,$6}; ($6 == "-") {print $1,$3-1,$3,$4,$5,$6}' | gzip > LZ_R4.bed.gz
```

Next, count the number of reads starting in each position. This is done using the genomecov command in the bedtools suite.

```
[dankoc@cbsumm27 dankoc]$ bedtools genomecov -bg -i LZ_R4.bed.gz -g /workdir/data/mm10.chromInfo -strand + > LZ_R4_plus.bedGraph
[dankoc@cbsumm27 dankoc]$ bedtools genomecov -bg -i LZ_R4.bed.gz -g /workdir/data/mm10.chromInfo -strand - > LZ_R4_minus.bedGraph
```

Note that a new file is required as input (mm10.chromInfo). This file representes the size of every chromosome in the mouse genome. It's human readable. Have a look at what's inside using "cat /workdir/data/mm10.chromInfo"!

Also notice that this line is run twice in order to split off reads mapping to + and - strand. Remember that leChRO-seq resolves the strand onto which RNA polymerase is engaged, and we need to capture that information when we are analyzing the data.

The final step is to convert these bedGraph files into the bigWig format. There is not much difference in what kind of data is represented between these files, only in how it is represented. BigWig files store an index that allows a computer program to request data from just a portion of the genome without read the entire file. This speeds up some analyses.

The conversion to bigWig is done using the bedGraphToBigWig program, one of the Kent Source utilities written by authors of the UCSC genome browser.

```
[dankoc@cbsumm27 dankoc]$ /workdir/data/bedGraphToBigWig LZ_R4_plus.bedGraph /workdir/data/mm10.chromInfo LZ_R4_plus.bw
[dankoc@cbsumm27 dankoc]$ /workdir/data/bedGraphToBigWig LZ_R4_minus.bedGraph /workdir/data/mm10.chromInfo LZ_R4_minus.bw
```

Note: BED, bedGraph, and bigWig are all standard file formats in bioinformatics. A great resource for learning about these file formats can be found here: https://genome.ucsc.edu/FAQ/FAQformat.html

Look at the mapped data using a genome browser
----------------------------------------------

Viewing mapped reads on a genome browser is the most informative way to determine many features of an experiment, and should be the first analysis after read mapping. Please try to view the bigWig fies that you generated using a genome browser. 

I suggest starting with IGV. Download the IGV genome browser here: http://software.broadinstitute.org/software/igv/home, and follow the instructions to make it run.

Download your bigWig files using filezilla, or another SFTP client of your choice. Once you have bigWig files downloaded, you can just drag and drop them into IGV. 

Look in particular at the pattern of RNA polymerase on genes. We will pick up the discussion here on Thursday. 


Notes/ thoughts
---------------

Note that for tasks that you do all the time, it is much *much* better to have a pipeline set up that automates each of these steps. The PRO-seq, GRO-seq, and ChRO-seq pipeline that we use in my lab can be found here: 
https://github.com/Danko-Lab/proseq2.0

This program is essentially just a "shell script" which automates the commands that you just put in manually. To use it, you just execut a single command which takes in all of the information and provides the location of the BAM and bigWig files at the end. 

