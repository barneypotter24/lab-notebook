---
layout: post
title:  "Starting My Lab Notebook and USVI Analysis"
date:   2016-12-12 15:20:00 -0800
categores: organization zika-seq lab-meeting
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

## Zika-seq
I started working on analyzing the data that Alli is generating in USVI today. the overall pipeline according to Alli:

### Here's a high-level overview of different parts of the pipeline:
1. The minION sequences the library producing small voltage graphs (squiggle graphs). These are saved as `FAST5` files locally on the computer running the minION.
2. Squiggle graphs are base called using `Metrichor`, a cloud-based neural net program that Oxford Nanopore puts out. Base-called reads are re-deposited to the local laptop (There is apparently a command line version of `Metrichor` that you can specifically request, I'll work on this).
3. Base called reads are sorted into two folders on the local computer: a "pass" fold and a "fail" folder. Reads in the pass folder will be de-multiplexed (there will be separate subdirectories for each barcode). Fail folder will not be, so we'll need to script a de-multiplexing strategy. Nick has some example code [here](https://raw.githubusercontent.com/zibraproject/zibraproject.github.io/master/data/nanonetcall.diff.txt).
4. Use Nick's totally awesome [`poretools` package ](http://poretools.readthedocs.io/en/latest/) to get read stats (longest read, how many reads, etc.) and to get the files into `fastq` or `fasta` format (format choice depends on what you'll do downstream). _Nanopore trivia: ONT didn't have a way to extract sequences into fasta as part of their original analysis system, so they owe Nick big time_.
5. Map the reads to a reference using `bwa` (algorithm of choice for ONT data). Convert SAM file format to BAM file format and create SAM index file.
6. QC the aligned reads using `samtools`.
7. Have a look at the alignment, look briefly for SNPs, poor coverage areas, or any sort of weirdness.
8. Call variants (SNPs) with [nanopolish ](https://github.com/jts/nanopolish) which improves accuracy greatly by realigning the squiggles to the basecalled data. This will give you a VCF file. Side note: Jared talks a lot about [how nanopolish works](http://simpsonlab.github.io/2015/10/07/nanopolish-v0.4.0/) on his blog, which is incredibly helpful. [Also worthwhile to read](http://simpsonlab.github.io/2015/03/30/optimizing-hmm/). Heads up, we are using the **R9 chemistry**, so we'll need to use the newer version of nanopolish.
9. Call the consensus sequence given the VCF file, the bam file, the reference sequence using Nick's script margin_cons.py (or adapt). Primer sites need to be masked.

**Handy tutorials:**
* [Basic tutorial describing data and poretools](http://porecamp.github.io/2016/tutorials/PoreCamp2016-02-MinIONData.pdf)
* [For mapping reads](http://porecamp.github.io/2016/tutorials/mappingtute.html)
* [Nick's basic notes on the pipeline with links](http://porecamp.github.io/2016/tutorials/bioinftute.html)

I did forget that Alli still needs to base-call the squiggle graphs, so I spent too much time today trying to run `poretools` on the `.fast5` files that had not been base called. When the base calling is done (using `Metrichor`), then the base called `.fast5` files will be available in directores labelled for each barcode:
* `~/Dropbox/usvi_zika_data/usvi_library1/NB01` ... `~/Dropbox/usvi_zika_data/usvi_library1/NB12`.

Today I did successfully get `poretools` installed and running, for the functions that made sense. I was able to make a figure for summary statistics on the squiggle graphs, though I'm not exactly sure what times the x-axis refers to:
![Reads]({{ site.url }}/images/usvi_library1_yield_plot_reads.png)

## Dropbox
I expanded my dropbox to hold 1 Terrabyte of data today ($99, will be reimbursed by financial dept). Now it has enough space to hold all the `.fast5` files that Alli is pushing from USVI.

## Lab meeting
John talked at lab meeting today, giving a practice run of his rotation talk. For that talk he discussed 2 different models that he came up with for flu prediction. The first is based on using epitope mutations (genotypic variation) in HI proteins to predict changes in titers (phenotypic variation). In particular, there are issues with predicting titers when entire clades are removed from training data, so he had to work on that issue. He also presented a way that he was using HI data (phenotype) to predict clade frequency (fitness).

After giving his rotation talk, John gave a second powerpoint about his greedy algorithm for determining epitope masks that may help predictive power. His algorithm seemed to capture good epitope masks, at least as measured by [Matthews correlation coefficient](https://en.wikipedia.org/wiki/Matthews_correlation_coefficient) (a correlation coefficient based on confusion matrices):

|            | Pred True | Pred False |
|:-----------|:---------:|-----------:|
| Was True   |    TP     |     FN     |
| Was False  |    FP     |     TN     |

John was really well organized, which made me want to start this detailed lab notebook.

## Indicator function
I did not get to writing the indicator function that I was hoping to add to skyline. Hopefully I will be able to do so tomorrow.

## Tomorrow's tasks
* Lab Notebook
* Add the indicator function
* Run the pipeline properly, if the base-called reads are on dropbox
* Finalize my presentation for the deep learning reading group
* Debug "vector of zeroes" error in skyline
