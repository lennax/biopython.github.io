---
title: SeqRecord
permalink: wiki/SeqRecord
layout: wiki
---

This page will describe the SeqRecord object used in BioPython to hold a
sequence (as a Seq object) with identifiers (ID and name), description
and optionally annotation and sub-features.

Most of the sequence file format parsers in BioPython can return
SeqRecord objects (and may offer a format specific record object too).
The new [SeqIO](SeqIO "wikilink") system will only return SeqRecord
objects.