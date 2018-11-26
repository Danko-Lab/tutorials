Detailed protocol for mapping PRO-seq, GRO-seq, and ChRO-seq data.
==================================================================

Global Run-On and Sequencing (GRO-seq), Precision Run-On and Sequencing (PRO-seq), and Chromatin Run-On and Sequencing are technologies for mapping the location and orientation of actively transcribing RNA polymerase I, II, and III (Pol) across the genome.  All Run-on technologies provide a genome-wide readout of gene and lincRNA transcription, as well as the location and relative activities of active enhancers and promoters that regulate gene expression.

<BR><BR>

Finalize your BioHPC reservation
--------------------------------

Four of you had accounts. For those who did not, find the e-mail invitation from BioHPC (should have received this yesterday, Nov. 26th).
<img align="left" src="etc\BioHPCpwd.png" width="600">

<BR><BR>


Log into the high performance compute server
--------------------------------------------

Secure shell (a.k.a. SSH) is a protocol that lets you log into a computer and control it remotely. We are logging into high performance computers (HPC) hosted by the BioHPC service, as part of the BioHPC cloud: https://biohpc.cornell.edu/Default.aspx 

An SSH clinet called "PuTTY" is installed on computers in B30B. 

* Select the Windows start menu and type "putty".

* Under Hostname, enter the host name assinged to you (see the Power Point slide).

* Select Open and enter your username (you netID) and your password.

Hint: You can download PuTTY for your own computer from this URL: 
``` https://www.putty.org/ ```

Look at the raw data ChRO-seq data in fastq format
--------------------------------------------------

The next step is to use LINUX commands to navigate to the raw ChRO-seq data.
