---
layout: post
title:  "Starting My Lab Notebook and USVI Analysis"
date:   2016-12-12 15:20:00 -0800
categories: organization
---
# Starting my notebook.
Today, during lab meeting, I decided to start keeping this lab notebook, inspired by John's great organization. Hopefully I'll be able to use this as a tool to keep myself more organized, as well as to hold myself accountable and to be able to record what I'm doing in Trevor's lab. I have a few aims for this notebook, and I will to keep up-to-date with all of them daily:
* What are my daily goals at the start of every day?
* What did I do, how was it done, and what did it accomplish?
* Describe talks and readings.
* Which of my goals did I accomplish, and what will I do tomorrow?
* What non-Bedford work did get done?

Hopefully I'll be able to accomplish this!

## Today's goals (according to the Slack #tasks channel):
* run zika-seq pipeline
* expand dropbox to hold all the fast5's
* lab meeting
* add indicator fxn for coalescences to skyline

##Zika-seq
I started working on analyzing the data that Alli is generating in USVI today. the overall pipeline according to Alli:

###Here's a high-level overview of different parts of the pipeline:
1. The minION sequences the library producing small voltage graphs (squiggle graphs). These are saved as `FAST5` files locally on the computer running the minION.
2. Squiggle graphs are base called using `Metrichor`, a cloud-based neural net program that Oxford Nanopore puts out. Base-called reads are re-deposited to the local laptop (There is apparently a command line version of `Metrichor` that you can specifically request, I'll work on this).
3. Base called reads are sorted into two folders on the local computer: a "pass" fold and a "fail" folder. Reads in the pass folder will be de-multiplexed (there will be separate subdirectories for each barcode). Fail folder will not be, so we'll need to script a de-multiplexing strategy. Nick has some example code [here](https://raw.githubusercontent.com/zibraproject/zibraproject.github.io/master/data/nanonetcall.diff.txt).
4. Use Nick's totally awesome [`poretools` package ](http://poretools.readthedocs.io/en/latest/) to get read stats (longest read, how many reads, etc.) and to get the files into `fastq` or `fasta` format (format choice depends on what you'll do downstream). _Nanopore trivia: ONT didn't have a way to extract sequences into fasta as part of their original analysis system, so they owe Nick big time_.
5. Map the reads to a reference using `bwa` (algorithm of choice for ONT data). Convert SAM file format to BAM file format and create SAM index file.
6. QC the aligned reads using `samtools`.
7. Have a look at the alignment, look briefly for SNPs, poor coverage areas, or any sort of weirdness.
8. Call variants (SNPs) with [nanopolish ](https://github.com/jts/nanopolish) which improves accuracy greatly by realigning the squiggles to the basecalled data. This will give you a VCF file. Side note: Jared talks a lot about [how nanopolish works](http://simpsonlab.github.io/2015/10/07/nanopolish-v0.4.0/) on his blog, which is incredibly helpful. [Also worthwhile to read](http://simpsonlab.github.io/2015/03/30/optimizing-hmm/). Heads up, we are using the **R9 chemistry**, so we'll need to use the newer version of nanopolish.
9. Call the consensus sequence given the VCF file, the bam file, the reference sequence using Nick's script margin_cons.py (or adapt). Primer sites need to be masked.
####Handy tutorials!
* [Basic tutorial describing data and poretools](http://porecamp.github.io/2016/tutorials/PoreCamp2016-02-MinIONData.pdf)
* [For mapping reads](http://porecamp.github.io/2016/tutorials/mappingtute.html)
* [Nick's basic notes on the pipeline with links](http://porecamp.github.io/2016/tutorials/bioinftute.html)

I did forget that Alli still needs to base-call the squiggle graphs, so I spent too much time today trying to run `poretools` on the `.fast5` files that had not been base called. When the base calling is done (using `Metrichor`), then the base called `.fast5` files will be available in directores labelled for each barcode:
* `~/Dropbox/usvi_zika_data/usvi_library1/NB01` ... `~/Dropbox/usvi_zika_data/usvi_library1/NB12`.

Today I did successfully get `poretools` installed and running, for the functions that made sense. I was able to make 2 figures for summary statistics on the squiggle graphs, though I'm not exactly sure what times the x-axis refers to on both:
![My helpful screenshot]({{ site.url }}/assets/screenshot.jpg)
