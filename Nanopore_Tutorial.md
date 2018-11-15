# Tutorial: Nanopore Analysis Pipeline

The following is a tutorial that demonstrates a pipeline used for analysis of Oxford Nanopore genetic data. This tutorial will require the following (brief installation instructions are included below):

[Canu Assembler](https://canu.readthedocs.io/en/latest/)  
[Bandage](https://rrwick.github.io/Bandage/)  
[Prokka](https://github.com/tseemann/prokka)  


## Installation
### Canu

Canu is a packaged correction, trimming, and assembly program that is forked from the Celera assembler codebase. Install the latest release by running the following:

```
git clone https://github.com/marbl/canu.git
cd canu/src
make
```

### Bandage
Bandage is an assembly visualization software. Install it by visiting [this link](https://github.com/rrwick/Bandage/releases/), and downloading the version appropriate for your device.


### Prokka
Prokka is a gene annotation program. Install it by visiting [this link](https://github.com/tseemann/prokka) and running the installation commands appropriate for your device.


### Data
### TODO, how are we providing access to this data? If this is an internal tutorial, link to drive will work. If not then not sure
2018.12.02_SWI_9_2, Isolate from local saline lake, 18 runs/fastq files. Saved under runs_fastq directory.


## Assembly
Canu can be used directly on the data without any preprocessing. The only additional information needed is an estimate of the genome size of the sample. For the saline isolate, we estimate 3,000,000 base pairs. Then, use the folliowing Canu command to assemble our data:

```
canu -nanopore_raw -p test_canu -d test_canu runs_fastq/*.fastq genomeSize=3000000 gnuplotTested=true
```

A quick description of all flags and parameters: 
-nanopore_raw - specifies data is Oxford Nanopore with no data preprocessing
-p - specifies prefix for output files, use “test_canu” as default
-d - specifies directory to run test and output files in, use “test_canu” as default
genomeSize - estimated genome size of isolate
gnuplotTested - setting to true will skip gnuplot testing; gnuplot is not needed for this pipeline

Running this command will output various files into the test_canu directory. The assembled contigs are located in the test.contigs.fasta file. These contigs can be better visualized using Bandage.


## Assembly Visualization
Opening Bandage and a GUI window should pop up. In the toolbar, click File > Load Graph, and select the test.contigs.gfa. You should see something like the following:

<br /><img src="https://github.com/sabeelmansuri/bowman_archive/blob/master/Bandage.png" width="300"><br />

This graph reveals that one of our contigs appears to be a whole circular chromosome! A quick comparison with the test.contigs.fasta file reveals this is Contig 1. We extract only this sequence from the contigs file to examine further. Note that the first contig takes up the first 38,673 lines of the file, so use `head`:

```
head -n38673 test_canu/test_canu.contigs.fasta >> test_canu/contig1.fasta 
```

## NCBI BLAST
We blast this Contig using NCBI’s nucleotide BLAST database (linked [here](https://blast.ncbi.nlm.nih.gov/Blast.cgi)) with all default options. The top hit is:

```
Hit: Halomonas sp. hl-4 genome assembly, chromosome: I  
Organism: Halomonas sp. hl-4  
Phylogeny: Bacteria/Proteobacteria/Gammaproteobacteria/Oceanospirillales/Halomonadaceae/Halomonas  
Max score: 65370  
Query cover: 72%  
E value: 0.0  
Ident: 87%  
```

It appears this chromosome is the genome of an organism in the genus *Halomonas*. We may now be interested in
the gene annotation of this genome.


## Gene Annotation
Prokka will take care of gene annotation, the only required input is the contig1.fasta file.

```
prokka --outdir circular --prefix test_prokka test_canu/contig1.fasta
```

The newly created circular directory contains various files with data on the gene annotation. Take a look inside test_prokka.txt for a quick summary of the annotation.


## Summary
The analysis above has taken Oxford Nanopore sequenced data, assmebled contigs, identified the closest matching
organism, and annotated its genome.
