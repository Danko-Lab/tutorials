BioMG 7810 practical day 2: Pausing index plots.
================================================

Motivated by our observations examining RNA polymerase distribuion using meta-gene plots, we next ask a specific question: Does the ratio of pause:body transcription change during prophase I?

What is the 'pausing index'?
----------------------------

The pausing index is deinfed as the ratio of reads in the pause peak to the gene body [Pause]/[Gene body]. 

<img align="left" src="etc\PausingIndex.png" width="900">

<BR><BR>
Note that the window definitions are arbitrary. Many different definitions have been used in the literature, and there are valid arguments for all of them. The main important aspect of this type of analysis is that your results are consistent regardless of the definition of pausing index. 

You can show from first principals that the pausing index is appxosimately proportional to the rate at which Pol II is "released" from a paused state into productive elongation. I will post pictures of this if I have time before class.

Let's compute the 'pausing index'
---------------------------------

First, we need to get counts/ kilobase in the pause and gene body windows. I have placed an R script that will hel pyou do this here: ```/workdir/data/getCounts.R```.

Download that script and use Notepad++ to edit the following lines: 
* Line 5: Point to the location of gene annotations (```/local/data/final_tus.txt```).
* Line 23: Edit the path to point to the raw bigWig files (```//workdir/data/bigWigs/raw```).

Use Filezilla to upload the modified file into your working directory, and run it: 

```
[dankoc@cbsumm22 dankoc]$ R --no-save < getCounts.R
```

If that command completes successfully, it will write two RData files that can be read by R. These RData files contain the raw and normalized counts for each biological replicate and each condition.

```
[dankoc@cbsumm22 dankoc]$ ls -lha *.RData
-rw-rw-r-- 1 lz539 danko 7.0M Sep 16 17:27 data-counts.RData
-rw-rw-r-- 1 lz539 danko 8.8M Sep 16 17:30 data-rpkms.RData
```

The next step is to use these raw counts to plot the pausing index. 

To ease the transition for those not familiar with R, I have also provided a script that can write out violin plots (see here: ```/workdir/data/pauseIndexCDFs.R```). I would encourage those with R experience to poke around and look at the data in several different ways. To work with this sciprt, download it to your machine and make the following changes: 
* Line 26: Specify the path to your own ```data-rpkms.RData``` file.


<img align="left" src="etc\PI-by-stage.png" width="900">
