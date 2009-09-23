---
title: TreeIO
layout: wiki
---

This module handles the parsing and generation of phylogenetic tree file
formats.

This code is not yet part of Biopython, and therefore the documentation
has not been integrated into the Biopython Tutorial yet either.

Availability
------------

This module is being developed alongside phyloXML support as a Google
Summer of Code 2009 project. To use TreeIO, see the
[PhyloXML](PhyloXML "wikilink") page for instructions on installing from
a [GitHub](GitUsage "wikilink") branch.

Functions
---------

Wrappers for supported file formats are available from the top level of
the module:

``` python
from Bio import TreeIO
```

Like SeqIO and AlignIO, this module provides four functions: parse(),
read(), write() and convert(). Each function accepts either a file name
or an open file handle, so data can be also loaded from compressed
files, StringIO objects, and so on. If the file name is passed as a
string, the file is automatically closed when the function finishes;
otherwise, you're responsible for closing the handle yourself.

The second argument to each function is the target format. Currently,
the following formats are supported:

-   phyloxml
-   newick
-   nexus

See the [PhyloXML](PhyloXML "wikilink") and [Tree](Tree "wikilink")
pages for more examples of using tree objects.

### parse()

Incrementally parse each tree in the given file or handle, returning an
iterator of Tree objects (i.e. some subclass of the Bio.Tree.BaseTree
Tree class, depending on the file format).

``` python
>>> trees = TreeIO.parse('phyloxml_examples.xml', 'phyloxml')
>>> for tree in trees:
...     print tree.name
```

    example from Prof. Joe Felsenstein's book "Inferring Phylogenies"
    example from Prof. Joe Felsenstein's book "Inferring Phylogenies"
    same example, with support of type "bootstrap"
    same example, with species and sequence
    same example, with gene duplication information and sequence relationships
    similar example, with more detailed sequence data
    network, node B is connected to TWO nodes: AB and C
    ...

If there's only one tree, then the next() method on the resulting
generator will return it.

``` python
>>> tree = TreeIO.parse('phyloxml_examples.xml', 'phyloxml').next()
>>> tree.name
'example from Prof. Joe Felsenstein\'s book "Inferring Phylogenies"'
```

Note that this doesn't immediately reveal whether there are any
remaining trees -- if you want to verify that, use read() instead.

### read()

Parse and return exactly one tree from the given file or handle. If the
file contains zero or multiple trees, a ValueError is raised. This is
useful if you know a file contains just one tree, to load that tree
object directly rather than through parse() and next(), and as a safety
check to ensure the input file does in fact contain exactly one
phylogenetic tree at the top level.

``` python
tree = TreeIO.read('example.xml', 'phyloxml')
print tree
```

### write()

Write a sequence of Tree objects to the given file or handle. Passing a
single Tree object instead of a list or iterable will also work. (See,
TreeIO is friendly.)

``` python
tree1 = TreeIO.read('example1.xml', 'phyloxml')
tree2 = TreeIO.read('example2.xml', 'phyloxml')
TreeIO.write([tree1, tree2], 'example-both.xml', 'phyloxml')
```

### convert()

Given two files (or handles) and two formats, *both supported by
Bio.Tree*, convert the first file from the first format to the second
format, writing the output to the second file.

**Warning:** The only format the currently uses the Bio.Tree base
classes is PhyloXML. The Nexus/Newick trees are based on a different set
of classes, so converting between PhyloXML and these other formats will
not work (for now).

``` python
TreeIO.convert('example.nhx', 'newick', 'example2.nex', 'nexus')
```

Sub-modules
-----------

Within the TreeIO module are parsers for specific file formats (or
wrappers thereof), conforming to the basic TreeIO API and sometimes
adding additional features.

### PhyloXMLIO

Support for the [phyloXML](http://www.phyloxml.org/) format. See the
[PhyloXML](PhyloXML "wikilink") page for details.

### NewickIO

Wrappers around Bio.Nexus.Trees to support the Newick (a.k.a. New
Hampshire) format through the TreeIO API.

### NexusIO

Wrappers around Bio.Nexus to support the Nexus tree format.

The Nexus format actually contains several sub-formats for different
kinds of data; to represent trees, Nexus provides a block containing
some metadata and one or more Newick trees. (Another kind of Nexus block
can represent alignments; this is handled in AlignIO.) So to parse a
complete Nexus file with all block types handled, use Bio.Nexus
directly, and to extract just the trees, use Bio.TreeIO.