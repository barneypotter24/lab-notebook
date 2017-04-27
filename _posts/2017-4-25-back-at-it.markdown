---
layout: post
title:  "Back at It"
date:   2017-04-25 11:00:00 -0800
categories: zika-seq nextstrain-fauna
---

# zika-seq
Started setting up docker image for our own `blab/zika-seq` docker repo.
* Trevor added me to the org `blab` on docker, made `blab/zika-seq`
* Log onto docker with `docker login -u <USERNAME>`
* Build docker image with `docker build -t blab/zika-seq:latest .`
  - Error in `RUN apt-get install -y r-base` from `DOCKERFILE`
```
E: Failed to fetch http://archive.ubuntu.com/ubuntu/pool/main/f/freetype/libfreetype6_2.6.1-0.1ubuntu2.1_amd64.deb  404  Not Found [IP: 91.189.88.152 80]

E: Unable to fetch some archives, maybe run apt-get update or try with --fix-missing?
```
  - Added `--fix-missing` to `DOCKERFILE` line with error, re-run.
  - Tons of warnings for `RUN git clone --recursive https://github.com/jts/nanopolish.git && cd nanopolish && make -j8`, but no full error message got thrown.
  - Commented `ADD http://www.timeapi.org/utc/now /tmp/bustcache` from `DOCKERFILE`, otherwise container seems to be working correctly.
  

# nextstrain-fauna
Updated `vdb` with 521 new viruses from GISAID EPIFLU.
Notes from [`nextstrain/fauna`](https://github.com/nextstrain/fauna/blob/master/FLU.md#upload-documents-to-vdb) github on `vdb` updates:

1. Download sequences and meta information from [GISAID](http://platform.gisaid.org/)
  * In EPIFLU, select host as `human`, select `HA` as required segment, select Submission Date >= last upload date to vdb
  * Ideally download about 5000 isolates at a time, may have to split downloads by submission date
  * Download Isolates as XLS with YYYY-MM-DD date format
  * Download Isolates as "Sequences (DNA) as FASTA"
    * Select all DNA
    * Fasta Header as 0: DNA Accession no., 1: Isolate name, 2: Isolate ID, 3: Segment, 4: Passage details/history, 5: Submitting lab
    * `DNA Accession no. | Isolate name | Isolate ID | Segment | Passage details/history | Submitting lab`
2. Move files to `fauna/data` as `gisaid_epiflu.xls` and `gisaid_epiflu.fasta`.
3. Upload to vdb database
  * `python vdb/flu_upload.py -db vdb -v flu --source gisaid --fname gisaid_epiflu`
  * Recommend running with `--preview` to confirm strain names and locations are correctly parsed before uploading
  	* Can add to [geo_synonyms file](source-data/geo_synonyms.tsv), [flu_strain_name_fix file](source-data/flu_strain_name_fix.tsv) and [flu_fix_location_label file](source-data/flu_fix_location_label.tsv) to fix some of the formatting.

# nextflu
Started download of all non-cdc data for new nextflu build:
`python download_all.py --all --copy_data --augur_path ../../blab/nextflu/augur/`

# Reading
Started reading [Volz _et al._](https://academic.oup.com/mbe/article-lookup/doi/10.1093/molbev/msx077) Phylodynamic Inference across Epidemic Scales
