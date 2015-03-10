![RNA-seq Flowchart - Module 3](Images/RNA-seq_Flowchart3.png)

#3-i. Adapter Trim
[OPTIONAL]
Use Flexbar to trim sequence adapter from the read FASTQ files.  The output of this step will be trimmed FASTQ files for each data set.

Refer to the Flexbar project and manual for a more detailed explanation:
* http://sourceforge.net/projects/flexbar/
* http://sourceforge.net/p/flexbar/wiki/Manual/

Flexbar basic usage:
```
flexbar -r reads [-t target] [-b barcodes] [-a adapters] [options]
```
	
Extra options specified below:

* '--adapter-min-overlap 7' requires a minimum of 7 bases to match the adapter
* '--adapter-trim-end RIGHT' uses a trimming strategy to remove the adapter from the 3' end or RIGHT end of the read
* '--max-uncalled 300' allows as many as 300 uncalled or N bases (MiSeq read lengths can be 300bp)
* '--min-read-length' the minimum read length allowed after trimming is 25bp.  TopHat has a default segment length of 25bp.  The smallest read size should be at least one segment length.
* '--threads 8 ' use 8 threads
* '--zip-output GZ' the input FASTQ files are gzipped so we will output gzipped FASTQ to save space
* '--adapters ' define the path to the adapter FASTA file to trim
* '--reads ' define the path to the read 1 FASTQ file of reads
* '--reads2 ' define the path to the read 2 FASTQ file of reads
* '--target ' a base path for the output files.  The value will _1.fastq.gz and _2.fastq.gz for read 1 and read 2 respectively

##Flexbar trim
```
cd $RNA_HOME/
export RNA_DATA_DIR=$RNA_HOME/data/
echo $RNA_DATA_DIR

mkdir -p $RNA_DATA_DIR/trimmed
export RNA_DATA_TRIM_DIR=$RNA_DATA_DIR/trimmed
echo $RNA_DATA_TRIM_DIR
```

In our tests, each sample took ~30 seconds to trim
```
flexbar --adapter-min-overlap 7 --adapter-trim-end RIGHT --adapters /gscmnt/gc7004/info/illumina_adapters/illumina_multiplex.fa --max-uncalled 300 --min-read-length 25 --threads 8 --zip-output GZ --reads $RNA_DATA_DIR/UHR_Rep1_ERCC-Mix1_Build37-ErccTranscripts-chr22.read1.fastq.gz --reads2 $RNA_DATA_DIR/UHR_Rep1_ERCC-Mix1_Build37-ErccTranscripts-chr22.read2.fastq.gz --target $RNA_DATA_TRIM_DIR/UHR_Rep1_ERCC-Mix1_Build37-ErccTranscripts-chr22
flexbar --adapter-min-overlap 7 --adapter-trim-end RIGHT --adapters /gscmnt/gc7004/info/illumina_adapters/illumina_multiplex.fa --max-uncalled 300 --min-read-length 25 --threads 8 --zip-output GZ --reads $RNA_DATA_DIR/UHR_Rep2_ERCC-Mix1_Build37-ErccTranscripts-chr22.read1.fastq.gz --reads2 $RNA_DATA_DIR/UHR_Rep2_ERCC-Mix1_Build37-ErccTranscripts-chr22.read2.fastq.gz --target $RNA_DATA_TRIM_DIR/UHR_Rep2_ERCC-Mix1_Build37-ErccTranscripts-chr22
flexbar --adapter-min-overlap 7 --adapter-trim-end RIGHT --adapters /gscmnt/gc7004/info/illumina_adapters/illumina_multiplex.fa --max-uncalled 300 --min-read-length 25 --threads 8 --zip-output GZ --reads $RNA_DATA_DIR/UHR_Rep3_ERCC-Mix1_Build37-ErccTranscripts-chr22.read1.fastq.gz --reads2 $RNA_DATA_DIR/UHR_Rep3_ERCC-Mix1_Build37-ErccTranscripts-chr22.read2.fastq.gz --target $RNA_DATA_TRIM_DIR/UHR_Rep3_ERCC-Mix1_Build37-ErccTranscripts-chr22
```        

```
flexbar --adapter-min-overlap 7 --adapter-trim-end RIGHT --adapters /gscmnt/gc7004/info/illumina_adapters/illumina_multiplex.fa --max-uncalled 300 --min-read-length 25 --threads 8 --zip-output GZ --reads $RNA_DATA_DIR/HBR_Rep1_ERCC-Mix2_Build37-ErccTranscripts-chr22.read1.fastq.gz --reads2 $RNA_DATA_DIR/HBR_Rep1_ERCC-Mix2_Build37-ErccTranscripts-chr22.read2.fastq.gz --target $RNA_DATA_TRIM_DIR/HBR_Rep1_ERCC-Mix2_Build37-ErccTranscripts-chr22
flexbar --adapter-min-overlap 7 --adapter-trim-end RIGHT --adapters /gscmnt/gc7004/info/illumina_adapters/illumina_multiplex.fa --max-uncalled 300 --min-read-length 25 --threads 8 --zip-output GZ --reads $RNA_DATA_DIR/HBR_Rep2_ERCC-Mix2_Build37-ErccTranscripts-chr22.read1.fastq.gz --reads2 $RNA_DATA_DIR/HBR_Rep2_ERCC-Mix2_Build37-ErccTranscripts-chr22.read2.fastq.gz --target $RNA_DATA_TRIM_DIR/HBR_Rep2_ERCC-Mix2_Build37-ErccTranscripts-chr22
flexbar --adapter-min-overlap 7 --adapter-trim-end RIGHT --adapters /gscmnt/gc7004/info/illumina_adapters/illumina_multiplex.fa --max-uncalled 300 --min-read-length 25 --threads 8 --zip-output GZ --reads $RNA_DATA_DIR/HBR_Rep3_ERCC-Mix2_Build37-ErccTranscripts-chr22.read1.fastq.gz --reads2 $RNA_DATA_DIR/HBR_Rep3_ERCC-Mix2_Build37-ErccTranscripts-chr22.read2.fastq.gz --target $RNA_DATA_TRIM_DIR/HBR_Rep3_ERCC-Mix2_Build37-ErccTranscripts-chr22	
```

| [[Previous Section|PreAlignment-QC]] | [[This Section|Adapter Trim]] | [[Next Section|Alignment]] |
|:------------------------------------:|:--------------------------:|:--------------------------------------------:|
| [[Data QC|PreAlignment-QC]]          | [[Adapter Trim|Adapter-Trim]]    | [[Alignment|Alignment]]         |