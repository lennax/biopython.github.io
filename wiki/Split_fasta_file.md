---
title: Split fasta file
layout: wiki
tags:
 - Cookbook
---

Problem
-------

[PLAN](http://bioinfo.noble.org/plan) is a free online service for BLAST
based sequence annotation provided by the Noble institute. At present
the people that run PLAN limit user's queries to 1000 records. This
recipe shows how to split a fasta file containing thousands of records
into smaller files with filenames that include the range of records
included in the file.

Solution
--------

``` python
from Bio import SeqIO

in_name = "huge_run.fasta"
records = list(SeqIO.parse(open(in_name, 'r'), "fasta"))
# How many files of exactly 1000 records can we make?
nfiles = len(records)/1000

for filenumber in range(nfiles):
    split_min = filenumber*1000 
    split_max = ((filenumber+1)*1000)-1
    seqs = records[split_min:split_max]
    out_name = ("_%s-%s." % (split_min+1, split_max+1)).join(in_name.split("."))
    out_handle = open(out_name, 'w')
    SeqIO.write(seqs, out_handle, "fasta")
    out_handle.close()

# That will make a bunch of files with 1000 records, but you will almost always 
# have some "dregs" to mop up. 

if len(records) % 1000 != 0: #checking for leftovers
    lsplit_min = (nfiles+1)*1000
    last_name = ("_%s-%s." % (lsplit_min, len(records)-1)).join(in_name.split("."))
    last_handle = open(last_name, 'w')
    lseqs = records[lsplit_min:-1]
    SeqIO.write(lseqs, last_handle, "fasta")
    last_handle.close()
else:
    continue
```

How it works
------------

The basic approach is do use [ SeqIO.parse()](SeqIO "wikilink") to read
the contents of a fasta file into a list, then pick out subsets to write
with [ SeqIO.write()](SeqIO "wikilink") . Perhaps the trickiest looking
line is the one that sets each new file's name:

``` python
out_name = ("_%s-%s." % (split_min+1, split_max+1)).join(in_name.split("."))
```

which can be broken down thus:

``` python
>>> insert = "_%s-%s." % (1, 1000)
>>> insert
'_1-1000.'
>>> split_name = "huge_run.fasta".split(".")
['huge_run', 'fasta']
>>>(insert).join(split_name)
'huge_run_1-1000.fasta'
```