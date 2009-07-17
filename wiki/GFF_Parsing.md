---
title: GFF Parsing
layout: wiki
tags:
 - Wiki Documentation
---

GFF Parsing
===========

*Note:* GFF parsing is not yet integrated into Biopython. This
documentation is work towards making it ready for inclusion. You can
retrieve the current version of the GFF parser from:
<http://github.com/chapmanb/bcbb/tree/master/gff>. Comments are very
welcome.

Generic Feature Format (GFF) is a biological sequence file format for
representing features and annotations on sequences. It is a tab
delimited format, making it accessible to biologists and editable in
text editors and spreadsheet programs. It is also well defined and can
be parsed via automated programs. GFF files are available from many of
the large sequencing and annotation centers. The
[specification](http://www.sequenceontology.org/gff3.shtml) provides
full details on the format and its uses.

Biopython provides a full featured GFF parser which will handle several
versions of GFF: GFF3, GFF2, and GTF. It supports writing GFF3, the
latest version.

GFF parsing differs from parsing other file formats like GenBank or PDB
in that it is not record oriented. In a GenBank file, sequences are
broken into discrete parts which can be parsed as a whole. In contrast,
GFF is a line oriented format with support for nesting features. GFF is
also commonly used to store only biological features, and not the
primary sequence.

These differences have some consequences in how you will deal with GFF:

-   Files are first examined to determine available annotations and
    define items of interest.
-   Sequences will often be parsed separately, from an associated
    FASTA file.
-   Parsing needs to consider available memory, which can be quickly
    used up on files with many annotations. This problem can be solved
    via two methods:
    -   Limiting parsing to features of interest.
    -   Iterating over portions of the file.

The documentation below provides a practical guide to examining, parsing
and writing GFF files in Python.

Examining your GFF file
-----------------------

``` python
import pprint
from BCBio.GFF import GFFExaminer

in_file = "your_file.gff"
examiner = GFFExaminer()
in_handle = open(in_file)
pprint.pprint(examiner.parent_child_map(in_handle))
in_handle.close()
```

    {('Coding_transcript', 'gene'): [('Coding_transcript', 'mRNA')],
     ('Coding_transcript', 'mRNA'): [('Coding_transcript', 'CDS'),
                                     ('Coding_transcript', 'exon'),
                                     ('Coding_transcript', 'five_prime_UTR'),
                                     ('Coding_transcript', 'intron'),
                                     ('Coding_transcript', 'three_prime_UTR')]}

``` python
import pprint
from BCBio.GFF import GFFExaminer

in_file = "your_file.gff"
examiner = GFFExaminer()
in_handle = open(in_file)
pprint.pprint(examiner.available_limits(in_handle))
in_handle.close()
```

    {'gff_id': {('I',): 159,
                ('II',): 3,
                ('III',): 2,
                ('IV',): 5,
                ('V',): 2,
                ('X',): 6},
     'gff_source': {('Allele',): 1,
                    ('Coding_transcript',): 102,
                    ('Expr_profile',): 1,
                    ('GenePair_STS',): 8,
                    ('Oligo_set',): 1,
                    ('Orfeome',): 8,
                    ('Promoterome',): 5,
                    ('SAGE_tag',): 1,
                    ('SAGE_tag_most_three_prime',): 1,
                    ('SAGE_tag_unambiguously_mapped',): 12,
                    ('history',): 30,
                    ('mass_spec_genome',): 7},
     'gff_source_type': {('Allele', 'SNP'): 1,
                         ('Coding_transcript', 'CDS'): 27,
                         ('Coding_transcript', 'exon'): 33,
                         ('Coding_transcript', 'five_prime_UTR'): 4,
                         ('Coding_transcript', 'gene'): 2,
                         ('Coding_transcript', 'intron'): 29,
                         ('Coding_transcript', 'mRNA'): 4,
                         ('Coding_transcript', 'three_prime_UTR'): 3,
                         ('Expr_profile', 'experimental_result_region'): 1,
                         ('GenePair_STS', 'PCR_product'): 8,
                         ('Oligo_set', 'reagent'): 1,
                         ('Orfeome', 'PCR_product'): 8,
                         ('Promoterome', 'PCR_product'): 5,
                         ('SAGE_tag', 'SAGE_tag'): 1,
                         ('SAGE_tag_most_three_prime', 'SAGE_tag'): 1,
                         ('SAGE_tag_unambiguously_mapped', 'SAGE_tag'): 12,
                         ('history', 'CDS'): 30,
                         ('mass_spec_genome', 'translated_nucleotide_match'): 7},
     'gff_type': {('CDS',): 57,
                  ('PCR_product',): 21,
                  ('SAGE_tag',): 14,
                  ('SNP',): 1,
                  ('exon',): 33,
                  ('experimental_result_region',): 1,
                  ('five_prime_UTR',): 4,
                  ('gene',): 2,
                  ('intron',): 29,
                  ('mRNA',): 4,
                  ('reagent',): 1,
                  ('three_prime_UTR',): 3,
                  ('translated_nucleotide_match',): 7}}

GFF Parsing
-----------

### Basic GFF parsing

``` python
from BCBio.GFF import GFFParser

in_file = "your_file.gff"
parser = GFFParser()

in_handle = open(in_file)
for rec in parser.parse(in_handle):
    print rec
in_handle.close()
```

### Providing initial sequence records

``` python
from BCBio.GFF import GFFParser
from Bio import SeqIO

in_seq_file = "seqs.fa"
in_seq_handle = open(in_seq_file)
seq_dict = SeqIO.to_dict(SeqIO.parse(in_seq_handle, "fasta"))
in_seq_handle.close()

in_file = "your_file.gff"
parser = GFFParser()
in_handle = open(in_file)
for rec in parser.parse(in_handle, base_dict=seq_dict):
    print rec
in_handle.close()
```

### Limiting parsed lines

``` python
from BCBio.GFF import GFFParser

in_file = "your_file.gff"
parser = GFFParser()

limit_info = dict(
        gff_source = ["Coding_transcript"])

in_handle = open(in_file)
for rec in parser.parse(in_handle, limit_info=limit_info):
    print rec.features[0]
in_handle.close()
```

### Iterating over portions of a file

``` python
from BCBio.GFF import GFFParser

in_file = "your_file.gff"
parser = GFFParser()

in_handle = open(in_file)
for rec in parser.parse_in_parts(in_handle, target_lines=1000):
    print rec
in_handle.close()
```

Writing GFF3
------------

``` python
from BCBio.GFF import GFF3Writer
from Bio import SeqIO

in_file = "your_file.gb"
out_file = "your_file.gff"
in_handle = open(in_file)
out_handle = open(out_file, "w")
writer = GFF3Writer()
writer.write(SeqIO.parse(in_handle, "genbank"), out_handle)

in_handle.close()
out_handle.close()
```