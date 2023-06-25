---
layout: post
title:  "Creating an otter assembly pipeline"
categories: otter assembly
permalink: "/otter-assembly/"
show_excerpts: True
---

Creating an otter assembly pipeline using snakemake, commandline tools and Julia!

![Sea otter](/assets/sea_otter.png){: .image-left }

# Background
Genome assembly has been around for quite some time. Starting from assembling simple organisms such as the _Escherichia coli_ and the _Saccharomyces cerevisiae_.
Only after the [Human Genome Project (1990-2003)](https://www.genome.gov/human-genome-project), genome assembly really kicked off.

There are two broad ways of performing genome assembly. The first of which is the _de novo_ assembly, which entails that all the reads that are created in a certain run are used in the process of assembly. One of the big problems with this technique is that it can be very computer intensive, since the overlap of reads is a very computer intensive task, let alone for over 3 million sequences in cases of _small_ genome projects.
Since one of the goals of this project was to make a genome assembler that could be run on someone's laptop, the other technique is used: reference guided assembly. With the reference guided assembly technique, the reads that are found in a specific run are mapped to a reference genome, increasing performanc of the algorithm, and per extension making it possible to create genomes using a laptop.

| genome assembly method | pros | cons |
|------------------------|------|------|
| _de novo_ | unique links, truer information | computer intensive, slow |
| reference guided | highly performant, is able to perform variant calling more easily | needs a reference, wrong references leads to wrong results.


After genome assembly, annotation can be performed, such as finding genes that are on the genome and researching how many chromosomes an organism has.

# Why Sea otters?
Sea otters are adorable, I have watched many video's of them over the years, and each one of them makes my heart melt. So for my spare time I wanted to find out what the otter genome is like. It should be stated however, that the pipeline still works on other organisms as well. 

# Unique aspects to the pipeline
A few characteristics that have been added to the pipeline are the following:
* All of the output files are stored in separate folders per category
* General overview of the fastq-reads file in a text-file
* Analysis of the quality per base in the reads file
* General overview of the VCF-file in a text-file
* Analysis of the VCF-file with heatmaps per contig


The following data were all results from the assembly of the _Streptococcus ruminantium_, since this organism was easy to perform assembly on, with quick feedback loops.

The input file of the reads was 1.5Gb, and the reference was 2.1Mb.

| input file | size |
|------------|------|
| reads ([DRR481114](https://trace.ncbi.nlm.nih.gov/Traces/?view=run_browser&acc=DRR481114&display=metadata)) | 1.5 Gb |
| reference ([NZ_AP018400.1](https://www.ncbi.nlm.nih.gov/nuccore/NZ_AP018400.1)) | 2.1 Mb |


The `head -6` can be found in the codeblock 1. Here it shows the read count, average length of reads, minimum length of reads (which surprisingly turned out to be zero), maximum length of reads, the average quality of reads and the average GC%. This particular script is the `analyser.jl` script, which took around 10s to run on this data, results of performance may vary due to incosistent read lengths.


_Codeblock 1_: The `head -6` results of the analysis of the reads-file using `analyser.jl`.
It shows the read-count, average length of reads, minimum length of reads, maximum length of reads, average quality of the reads and the average GC%.
```
Read count:			 115352
Average length of reads:	 6263.98
Minimum length of reads:	 0
Maximum length of reads:	 263040
Average quality of reads:	 16.613954385095916
Average GC%:			 40.86%
```

The quality was also plotted in the QC-plot which can be found in figure 1. This figure broadly shows all the different qualities that could be found on each of the different positions, with their minimum, maximum, Q1, median & Q3-values being shown. 

_Figure 1_: The QC-plot. It shows the Minimum, maximum, Q1, Q3 and median values for each read position (in case the position has more than a thousand reads). Notice how the quality of the reads steeply increases in the first 10-100 bases. After 20k bases, the read length becomes a little more inconsistent and therefore all the values fluctuate on their qualities.
[read_qc_plot](/assets/sea_otter.png){: .image-left }
