---
layout: post
title:  "Cleaning Tables"
date:   2016-12-20
categores: fauna
---

#### Goals for today:
* Clean up remaining titer tables
* Figure out the nonetype for `rethinkdb`
* Upload h3n2 (__top priority for upload__)
* Meet with Alli about Zika-seq
* Hopefully get h1n1pdm uploaded

### Fauna
Most of today was spent editing titer tables for uploading to fauna. All HI h3n2 tables are cleaned up, but I learned that tables 9-X are from a non-HI assay, called PRNT (plaque reduction neutralization titer). This assay was performed by the London folks because of the 160 mutation in h3n2 (3c2.a clade) that has caused a decrease in the power of HI assays, because it changed how the proteins bind to ligands used in the assays. Trevor explained that these assays measure how dilute sera need to be to cause 50% of a virus to stop killing cells in a cell plate.

For the PRNT assay tables, I am uploading them to the same `titer` field in `tdb`, but I am changing the `assay-type` to be indicative of this different assay type. Go figure.

### Zika-seq
I talked to Alli briefly, she is uploading un-base-called `.fast5` files to the cluster. I can find those files by going to `rhino` and navigating to `/fh/fast/bedford_t/data/`
