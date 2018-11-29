BioMG 7810 practical day 2: Meta gene for ATAC-seq.
===================================================

One model of shutdown is that paused Pol II functions as a nucleosome exclusion factor, of sorts, prevent nucleosomes from encroaching on DNA. If this model is true, we may expect to see nucleosomes fill-in on regulatory DNA, resulting in a loss of the nucleosome free region.

To test this hypothesis, Adriana Alexander preformed ATAC-seq (see here: https://www.nature.com/articles/nmeth.2688) in each of these three separate stages of Prophase I. Here we are going to use this data to ask how chromatin accessability to Tn5 nuclease changes following these three stages of prophase I.

Adding ATAC-seq
---------------

I have placed bigWig files representing ATAC-seq counts here: ```/workdir/data/bigWigs/atacseq```. 

Next, we edit the same meta-gene script we used to write out ChRO-seq meta plots near the TSS (```writeMetaPlots.ChROseq.R```). Note that this is a slightly more complicated modification than simply changing directories, ylims, tweaking colors, etc.

Download this script using filezilla (if you don't have it left over from before), and open it using Notepad++. 

Note that ATAC-seq data, at least this representation, does not provide information about DNA strand. Note that one of the two metaPlot functions on lines 9-19 takes single stranded data.

```
metaPlot <- function(bed, HP_FILE, path="./", stp= 100, halfWindow= 10000, ...) {
        bed <- center.bed(bed, halfWindow, halfWindow)
        HP <- load.bigWig(paste(path, HP_FILE, sep=""))
        H_meta <- metaprofile.bigWig(bed, HP, step=stp)

        N = length(H_meta$middle)
        x = 1:N*stp

        plot.metaprofile(H_meta, X0= 0, ...)
        abline(v=halfWindow, lty="dotted")
}
```

We are simply going to run this function on the ATAC-seq bigWig files. Proceed down to line 67, and enter the following: 

```
pth="/workdir/data/bigWigs/atacseq/"
LZatac <- metaPlot(tss[,1:3], "LZ_merged.bw", main="L/Z ATAC-seq", path=pth, stp=stp, halfWindow=hW)
```

Those two lines change the path, and direct the script to analyze LZ merged ATAC-seq file. 

Hint: When I'm making change son more than one file, I prefer to work in interactive mode with an open R session, and type the commands in as I go. When I'm done, I paste them into the script.

How would you instruct the script to analyze Pachytene and Diplotene conditions? Simply add additional lines that will analyze the P and D ATAC-seq bigWig files!

```
Patac <- metaPlot(tss, "P_merged.bw", main="P ATAC-seq", path=pth, stp=stp, halfWindow=hW)
Datac <- metaPlot(tss, "D_merged.bw", main="D ATAC-seq", path=pth, stp=stp, halfWindow=hW)
```

Each of those lines will write the plots for each condition into this PDF: ```PausePlots.raw-ProphaseI-ChRO-seq.pdf```. To put them on the same plot, add the following commands: 

```
ylims <- c(0, max(LZatac$data$middle)/LZatac$signal*10^8)
pdf("ATAC-seq_TSS.pdf")
 plot(-100000, -1000000, xlim= c(0,NROW(LZatac$data$middle)), ylim=ylims, xlab="Distance to TSS", ylab="ATAC-seq Signal")
 lines(LZatac$data$middle/LZatac$signal*10^8, col= "#cb6751")
 lines(Patac$data$middle/Patac$signal*10^8,  col= "#9e6ebd")
 lines(Datac$data$middle/Datac$signal*10^8, col= "#7aa457")
dev.off()
```

Finally, download and view ```ATAC-seq_TSS.pdf```.

The resulting figure is here: 
<img align="left" src="etc\ATACmeta.png" width="900">
