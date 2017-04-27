---
layout: post
title:  "Metrichor and nextstrain/augur"
date:   2016-12-13
categores: zika-seq augur
---
## Goals from yesterday:
* Run the pipeline properly, if the base-called reads are on dropbox
* Finalize my presentation for the deep learning reading group
* Debug “vector of zeroes” error in skyline
* Get augur running and test v1 vs v2 for h3n2
##### This morning's update:
* Download and install `Metrichor`
* Base-call reads for USVI data

## Zika-seq
Fist thing today, I downloded and installed `Metrichor` from its [website](https://metrichor.com/s/). After a brief chat with Alli, I started a run on the `Users/bpotter/Dropbox/usvi_zika_data/usvi_library1/pass/*.fast5` files in Dropbox using the workflow "2D Basecalling RNN for SQK-NSK007 plus Human Exome - v1.97" at 11:01am.
* Uploaded Directory: `~/Dropbox/usvi_zika_data/usvi_library1/pass/uploaded`
* Download Directory: `~/Dropbox/usvi_zika_data/usvi_library1/pass/downloads`
* Download Mode: Data & Telemetry

## Nextstrain/augur
Started by running the exact line of code specified in #nextstrain-augur using sequences acquired with: `vdb/download.py -db vdb -v flu --fstem h3n2` and `python tdb/download.py -db tdb -v flu --fstem h3n2 --ftype json`.

First command: `python flu/seasonal_flu.py --lineage h3n2 --resolution 3y`.
