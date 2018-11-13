# Tutorial: Nanopore Analysis Pipeline

The following is a tutorial that demonstrates a pipeline used for analysis of Oxford Nanopore genetic data. This tutorial will require the following (installation instructions are included below):

Canu Assembler [https://canu.readthedocs.io/en/latest/]
Bandage [https://rrwick.github.io/Bandage/]
Prokka [https://github.com/tseemann/prokka]


## Installation
### Canu

Canu is a packaged correction, trimming, and assembly program that is forked from the Celera assembler codebase. Install the latest release by running the following in your software directory:

```
git clone https://github.com/marbl/canu.git
cd canu/src
make
```

### Bandage

Bandage is an assembly visualization software. Install it by visiting this link [https://github.com/rrwick/Bandage/releases/], and downloading the version appropriate for your device.


### Prokka

Prokka is a gene annotation program. Install it by visiting this link [https://github.com/tseemann/prokka] and running the installation commands appropriate for your device


### Data
[TEMP] 2018.12.02_SWI_9_2, Isolate from local saline lake, 18 runs/fastq files. Saved under runs_fastq directory.


## Assembly
Canu can be used directly without any data preprocessing. The only additional information needed is an estimate of the genome size of the sample. For the saline isolate, we will use 3,000,000 base pairs. We can then run the following command to assemble our data:

```
canu -nanopore_raw -p test_canu -d test_canu runs_fastq/*.fastq genomeSize=3000000 gnuplotTested=true
```

A quick description of all flags and parameters: 
-nanopore_raw - specifies data is Oxford Nanopore with no data preprocessing
-p - specifies prefix for output files, use “test_canu” as default
-d - specifies directory to run test and output files in, use “test_canu” as default
genomeSize - estimated genome size of isolate
gnuplotTested - setting to true will skip gnuplot testing and speed up the analysis. gnuplot is not needed for this pipeline

Running this command will output various files into the test_canu directory. Our assembled contigs are located in the test.contigs.fasta file. If you explore this file, you’ll find Contig 1 to be the longest. 

Let’s see this in a more visual way. We’re interested in the test.contigs.gfa file, which will allow us to view our data using Bandage. 


## Assembly Visualization
Open Bandage and a GUI window should pop up. In the toolbar, click Fie > Load Graph, and select the test.contigs.gfa. You should see something like the following:


[BANDAGE IMAGE]


This graph suggests that the circular contig appears to be a whole chromosome! Our test.contigs.fasta shows us that this is Contig 1, so we proceed to examine this contig moving forward. 



## NCBI BLAST
We blast this Contig using NCBI’s BLAST database with all default options and retrieve:

[INSERT TEXTBOX OF FINDINGS]



## Gene Annotation
Now that the sample has been identified, we annotate the constructed genome using Prokka.
