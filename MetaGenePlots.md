BioMG 7810 practical day 2: Meta gene plots.
============================================

Visulizing raw or processed data can be a very informative way to undersatnd the biology of a process. The first step today is going to be downloading and visualizing 

Scaled meta gene function
-------------------------

I have placed an R script that generates meta-gene plots here: /workdir/data/ScaledMetaPlotFunctions.r. [Credit for this file: Greg Booth in John Lis' lab; Andre Martins; others.]

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

The lines that you will need to change are simply which files are used. These are found at the bottom of the file. Scroll down to the bottom. 

<img align="left" src="etc\BioHPCpwd.png" width="900">
