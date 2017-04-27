---
layout: post
title:  "Monday Before Holidays"
date:   2016-12-19
categores: zika-seq fauna
---

Last week, after starting this notebook, I got really lax in how diligently I was taking notes. It is a new week, so I'll try to start again and keep better records of what I am doing. In addition to the things that I was doing at the start of the week, I was given a couple other jobs by Trevor:
* Moving Nextflu from old augur to operate in the new augur: `blab/nextflu/augur` -> `neststrain/augur`
* Complete zika-seq bioinformatics with re-deposited data in Dropbox, following duplicate Metrichor runs early last week. _I don't know if Alli has updated dropbox yet._
* Scrape the latest London titer reports for new data to add to `tdb`. So far, I have done:
  * Scrape the pdfs using Tabula -> output to `~/Desktop/tdb_uploads/`
  * Concatenated tables were picked apart and put into folders for `h1n1pdm`, `h3n2`, `vic`, and `yam`.

#### Order of Priorities (Trevor):
1. Zika-seq pipeline
2. Importing September titer report to tdb
3. Getting `upload` fully debugged for CDC data
4. Augur transition for Nextstrain.
  * Trevor will go through augur and figure out specific areas that may need some tlc

####Today's Goals:
* Keep on editing files for `tdb`
* lab meeting
* zika-seq pipeline

### TDB Uploads
When I run `python tdb/upload.py -db test_tdb --subtype h3n2 --fstem 5-01 --preview`, I am getting the error:
```
Connected to the "test_tdb" database
Uploading Viruses to TDB
Traceback (most recent call last):
  File "tdb/upload.py", line 407, in <module>
    connTDB.upload(**args.__dict__)
  File "tdb/upload.py", line 71, in upload
    measurements = self.parse(ftype, **kwargs)
  File "/Users/bpotter/nextstrain/fauna/tdb/parse.py", line 23, in parse
    flat_measurements = self.read_flat(**kwargs)
  File "/Users/bpotter/nextstrain/fauna/tdb/parse.py", line 56, in read_flat
    for row in table_reader:
_csv.Error: new-line character seen in unquoted field - do you need to open the file in universal-newline mode?
```
I sent a message to Charleton, and I tried a couple Perl scripts such as `perl -pi.bak -e 's/\R/\012/' /path/to/file` to change the line endings in my `.tsv` file.

Looks like the error was that I was not using the `--ftype tables` flag for the parser. Now I need to change `cdc_titer tdb/upload` to change the field names correctly so that every data point isn't trimmed.

It is working (sort of) now! I need to run `python tdb/upload.py -db test_tdb --subtype h3n2 --path data/ --ftype tables --preview` to upload all CSVs in the directory `data/`. Unfortunately, now `upload.py` is trimming all the measurements because they are missing the attributes `['virus_strain_passage', 'virus_strain_passage_category', 'serum_antigen_passage', 'serum_antigen_passage_category', 'assay-type', 'virus_cdc_id']`, so getting all of these fields properly formatted is my next goal.

I updated `tdb/upload.py` and `tdb/parse.py` so that they will handle files containing tables that are not formatted in the CDC style. Everything seems to be working well. Updated functions:
##### `parse.py`
* `flist = glob.glob(path + '/September*csv')`
  * This (and the relevant places in `upload`) needs to have '#BP' removed.
##### `upload.py`
* `format_id`
* `format_assay_type`
* `format_measurements` added conditional for if there is just one passage given
* Test prints in `upload`
### Zika-seq

I met briefly with Alli, we are going to meet tomorrow to talk about the Zika-seq pipeline a little bit. It turns out I have been using the online version of Metrichor, rather than the offline version (Albacore). We will decide how we are going to transfer and back-up files, then I will continue with the pipeline tomorrow. Our goal is to have a couple draft genomes done by the end of the week so that there is some data to show going into the holidays.

### Lab Meeting: Alli
We discussed [this paper](https://s3.amazonaws.com/objects.readcube.com/articles/downloaded/nature/f32052ad48099c0fae42c01b082bd5fd4335cea8bf8aa4fd1894ee8b5f4fad21.pdf?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIS5LBPCM5JPOCDGQ%2F20161219%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20161219T174514Z&X-Amz-Expires=108885&X-Amz-SignedHeaders=host&X-Amz-Signature=9527e007653a9c7c51a28a02cc583989399e665eaa8a7d4a78a46053168d6490), titled 1970s and ‘Patient 0’ HIV-1 genomes illuminate early HIV/AIDS history in North America. The authors extracted RNA from serum samples dating back to 1978-79 that was isolated from men who have sex with men for Hepatitis B vaccine development. From those, they were able to extract RNA from some of the sera that tested HIV positive to make a handful of genomes for both NYC and SF populations. They were able to use these genomes to reconstruct phylogenies that approximate that HIV (subtype B, at least) made the jump from the Caribbean to New York around 1971. They also found that it looks like New York was the epicenter of the HIV epidemic in the US, and that the SF samples seemed to be less basal than the NYC samples, as well as far more clustered, indicating that they were all related more recently as a result of 1(ish) infection coming to SF from NYC.

In addition to their analysis of samples from the late '70s, they also did the first sequencing of the infamous 'Patient 0', which was originally 'Patient O'. Their findings seem to fully exonerate this individual from the apocryphal story of how they were singularly responsible for much of the HIV spread throughout the USA in the 1970s. Instead, it seems like they were just one of the best people at contact tracing, so they were actually one of the most helpful people from a public health perspective.

_Tomorrow:_
* Clean up remaining titer tables
* Figure out the nonetype for `rethinkdb`
* Upload h3n2 (__top priority for upload__)
* Meet with Alli about Zika-seq
* Hopefully get h1n1pdm uploaded
