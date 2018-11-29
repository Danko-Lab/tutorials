BioMG 7810 practical day 2: Meta gene plots.
============================================

Visulizing raw or processed data can be a very informative way to undersatnd the biology of a process. The first step today is going to be downloading and visualizing 

Scaled meta gene function
-------------------------

First, we are going to look at transcription over the entire gene. I have placed an R script that generates meta-gene plots over an entire gene region here: /workdir/data/ScaledMetaPlotFunctions.r. [Credit for this file: Greg Booth in John Lis' lab; Andre Martins; others.]

Download this script using filezilla, and open it using Notepad++. 

The top of the file contains R functions that are used to generate the meta plot (e.g., mround, scale_params, collect.many.scaled, etc.). These look something like this: 
```
function_name = function(parameters){
  instructions...
}
```

For instance, the scale_params function: 

```
scale_params = function(start, end, step=50, buffer=2000){
  length = end-start
  if (length <1000){
    buffer =  buffer
    step1  = step
    scaleFact = 1
  }
  else{
    length = ((end-start)-2*buffer)/2
    buffer = mround(length, base = 500)
    step1 = (buffer/500)
    scaleFact = step1/step # scale factor is divided by your scaled window count to adjust as if it were a 10bp window (i.e. window size = 5bp will have a scale factor of 2)
  }
  return(c(buffer,step1,scaleFact))
}
```

The lines that you will need to change are simply which files are used. These are found at the bottom of the file. Scroll down to the bottom. Changes that you need to make include: 
* Line 250: This points to the location of gene annotations. I have placed this file in ```/workdir/data/final_tus.txt```.
* Line 258: This line sets the path to the bigWig files that are used in the analysis. In this analysis we are going to use spike-in normalized bigWigs. I have placed these here: ```/workdir/data/bigWigs/norm```.
* Line 259-261: Change the bigWig file names, as appropriate.
* Line 270: Normalized values for this new dataset may not be the same as the script was written for. Remove the ylim argument. Also change the file names.

After making these changes, upload the script to your working directory. Run it using: 
```
[dankoc@cbsumm22 dankoc]$ R --no-save < ScaledMetaPlotFunctions.r 
```

Note that two new PDF files were added to your directory: 
```
[dankoc@cbsumm22 dankoc]$ ls -lha *.pdf
-rw-rw-r-- 1 dankoc dankoc 110K Nov 29 12:12  MetaGene.LenNorm-0-50.pdf
-rw-rw-r-- 1 dankoc dankoc 118K Nov 29 12:12  MetaGene.LenNorm.pdf
```

Download the resulting PDF files and look at them (mine is below):

<img align="left" src="etc\chroseq.lnmeta.png" width="900">

Metagene Focused on TSS
-----------------------

Note that one of the major differences in this analysis was the paused peak. Next, focus on this and make a meta plot without normalizing to length. I have placed a script to complete this task here: ```/workdir/data/writeMetaPlots.ChROseq.R```. 

Again, please download this script and edit the following lines: 
* Line 3: Change the gene annotation file.
* Line 51: Change the path to the bigWig files.

Then upload the file and run it using: 
```
[dankoc@cbsumm22 dankoc]$ R --no-save < writeMetaPlots.ChROseq.R
```

Again, download and look at the result. 

<img align="left" src="etc\chroseq.tssmeta.png" width="900">

Think about what do you make of this?! We're about to discuss it!
