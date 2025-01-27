#5-i Kallisto


##Using Kallisto for reference free transcript expression abundance estimation from RNA-seq data

For more information on Kallisto, refer to the <a href="https://pachterlab.github.io/kallisto/about.html">Kallisto project page</a> and <a href="https://pachterlab.github.io/kallisto/manual.html">Kallisto manual page</a>.

##Install Kallisto
**TODO: after development is complete, this can be moved to the installation section**

```bash

cd $RNA_HOME/
cd tools/
wget https://github.com/pachterlab/kallisto/releases/download/v0.42.4/kallisto_linux-v0.42.4.tar.gz
tar -zxvf kallisto_linux-v0.42.4.tar.gz

```

##Add Kallisto to PATH environment variable

```bash

export PATH=/home/ubuntu/workspace/rnaseq/tools/kallisto_linux-v0.42.4/:$PATH
kallisto

```

##Obtain transcript sequences in fasta format
Note that we already have fasta sequences for the reference genome sequence from earlier in the RNA-seq tutorial. However, Kallisto works directly on target *cDNA/transcript* sequences. Remember also that we have transcript models for genes on chromosome 22. These transcript models were downloaded from Ensembl in GTF format. This GTF contains a description of the coordinates of exons that make up each transcript but it does not contain the transcript sequences themselves. So currently we do not have transcript sequences needed by Kallisto. There are many places we could obtain these transcript sequences. For example, we could download them directly in Fasta format from the <a href="http://www.ensembl.org/info/data/ftp/index.html">Ensembl FTP site</a>.

To allow us to compare Kallisto results to expression results from Cufflinks, we will create a custom Fasta file that corresponds to the transcripts we used for the Cufflinks analysis. How can we obtain these transcript sequences in Fasta format?

We could download the complete fasta transcript database for human and pull out only those for genes on chromosome 22. Or we could use a tool from `tophat` called `gtf_to_fasta` to generate a fasta sequence from our GTF file. This approach is convenient because it will also include the sequences for the ERCC spike in controls, allowing us to generate Kallisto abundance estimates for those features as well.

```bash

cd $RNA_HOME/refs
gtf_to_fasta $REF_GTF $REF_FASTA chr22_ERCC92_transcripts.fa

```

Use `less` to view the file `chr22_ERCC92_transcripts.fa`. Note that this file has messy transcript names. Use the following hairball perl one-liner to tidy up the header line for each fasta sequence

```bash

cd $RNA_HOME/refs
cat chr22_ERCC92_transcripts.fa | perl -ne 'if ($_ =~/^\>\d+\s+\w+\s+(ERCC\S+)[\+\-]/){print ">$1\n"}elsif($_ =~ /\d+\s+(ENST\d+)/){print ">$1\n"}else{print $_}' > chr22_ERCC92_transcripts.clean.fa
wc -l chr22_ERCC92_transcripts*.fa

```

View the resulting 'clean' file using `less chr22_ERCC92_transcripts.clean.fa`. View the end of this file use `tail chr22_ERCC92_transcripts.clean.fa`. Note that we have one fasta record for each Ensembl transcript on chromosome 22 and we have an additional fasta record for each ERCC spike-in sequence.

Create a list of all transcript IDs for later use:

```bash

cd $RNA_HOME/refs
cat chr22_ERCC92_transcripts.clean.fa | grep ">" | perl -ne '$_ =~ s/\>//; print $_' | sort | uniq > transcript_id_list.txt

```

##Build a Kallisto transcriptome index
Remember that Kallisto does not perform *alignment* or use a reference genome sequence. Instead it performs *pseudoalignment* to determine the *compatibility* of reads with targets (transcript sequences in this case). However, similar to alignment algorithms like Tophat or STAR, Kallisto requires an **index** to assess this compatibility efficiently and quickly.

```bash

cd $RNA_HOME/refs
mkdir kallisto
cd kallisto
kallisto index --index=chr22_ERCC92_transcripts_kallisto_index chr22_ERCC92_transcripts.clean.fa

```

##Generate abundance estimates for all samples using Kallisto
As we did with `Cufflinks` and `HT-Seq` we will generate transcript abundances for each of our demonstration samples using `Kallisto`.

```bash

export RNA_DATA_DIR=$RNA_HOME/data/
echo $RNA_DATA_DIR

cd $RNA_HOME/expression/
mkdir kallisto
cd kallisto

kallisto quant --index=$RNA_HOME/refs/chr22_ERCC92_transcripts_kallisto_index --output-dir=UHR_Rep1_ERCC-Mix1 --threads=4 --plaintext $RNA_DATA_DIR/UHR_Rep1_ERCC-Mix1_Build37-ErccTranscripts-chr22.read1.fastq.gz $RNA_DATA_DIR/UHR_Rep1_ERCC-Mix1_Build37-ErccTranscripts-chr22.read2.fastq.gz
kallisto quant --index=$RNA_HOME/refs/chr22_ERCC92_transcripts_kallisto_index --output-dir=UHR_Rep2_ERCC-Mix1 --threads=4 --plaintext $RNA_DATA_DIR/UHR_Rep2_ERCC-Mix1_Build37-ErccTranscripts-chr22.read1.fastq.gz $RNA_DATA_DIR/UHR_Rep2_ERCC-Mix1_Build37-ErccTranscripts-chr22.read2.fastq.gz
kallisto quant --index=$RNA_HOME/refs/chr22_ERCC92_transcripts_kallisto_index --output-dir=UHR_Rep3_ERCC-Mix1 --threads=4 --plaintext $RNA_DATA_DIR/UHR_Rep3_ERCC-Mix1_Build37-ErccTranscripts-chr22.read1.fastq.gz $RNA_DATA_DIR/UHR_Rep3_ERCC-Mix1_Build37-ErccTranscripts-chr22.read2.fastq.gz

kallisto quant --index=$RNA_HOME/refs/chr22_ERCC92_transcripts_kallisto_index --output-dir=HBR_Rep1_ERCC-Mix2 --threads=4 --plaintext $RNA_DATA_DIR/HBR_Rep1_ERCC-Mix2_Build37-ErccTranscripts-chr22.read1.fastq.gz $RNA_DATA_DIR/HBR_Rep1_ERCC-Mix2_Build37-ErccTranscripts-chr22.read2.fastq.gz
kallisto quant --index=$RNA_HOME/refs/chr22_ERCC92_transcripts_kallisto_index --output-dir=HBR_Rep2_ERCC-Mix2 --threads=4 --plaintext $RNA_DATA_DIR/HBR_Rep2_ERCC-Mix2_Build37-ErccTranscripts-chr22.read1.fastq.gz $RNA_DATA_DIR/HBR_Rep2_ERCC-Mix2_Build37-ErccTranscripts-chr22.read2.fastq.gz
kallisto quant --index=$RNA_HOME/refs/chr22_ERCC92_transcripts_kallisto_index --output-dir=HBR_Rep3_ERCC-Mix2 --threads=4 --plaintext $RNA_DATA_DIR/HBR_Rep3_ERCC-Mix2_Build37-ErccTranscripts-chr22.read1.fastq.gz $RNA_DATA_DIR/HBR_Rep3_ERCC-Mix2_Build37-ErccTranscripts-chr22.read2.fastq.gz
```

Create a single TSV file that has the TPM abundance estimates for all six samples.

```bash

cd $RNA_HOME/expression/kallisto
paste */abundance.tsv | cut -f 1,2,5,10,15,20,25,30 > transcript_tpms_all_samples.tsv
ls -1 */abundance.tsv | perl -ne 'chomp $_; if ($_ =~ /(\S+)\/abundance\.tsv/){print "\t$1"}' | perl -ne 'print "target_id\tlength$_\n"' > header.tsv
cat header.tsv transcript_tpms_all_samples.tsv | grep -v "tpm" > transcript_tpms_all_samples.tsv2
mv transcript_tpms_all_samples.tsv2 transcript_tpms_all_samples.tsv
rm -f header.tsv

```

##Compare transcript abundance estimates from Kallisto to isoform abundance estimates from Cufflinks
How similar are the results we obtained from Kallisto to those from Cufflinks? We can compare the expression value for each Ensembl transcript from chromosome 22 as well as the ERCC spike in controls.

To do this comparison, we need to gather the expression estimates for each of our replicates from both approaches. The Kallisto results were neatly organized into a single file above. Next we need to obtain the transcript expression results from cufflinks (e.g. `$RNA_HOME/expression/tophat_cufflinks/ref_only/HBR_Rep1_ERCC-Mix2/isoforms.fpkm_tracking)`. 

```bash

Work in progress...

```

##Create a custom transcriptome database to examine a specific set of genes
For example, suppose we just want to quickly assess the presence of ribosomal RNA genes only. We can obtain these genes from an Ensembl GTF file. In the example below we will use our chromosome 22 GTF file for demonstration purposes. But in a 'real world' experiment you would you a GTF for all chromosomes. Once we have found GTF records for ribosomal RNA genes, we will create a fasta file that contains the sequences for these transcripts, and then index this sequence database for use with Kallisto.

```bash

cd $RNA_HOME/refs
grep rRNA $REF_GTF > genes_chr22_ERCC92_rRNA.gtf 
gtf_to_fasta genes_chr22_ERCC92_rRNA.gtf $REF_FASTA chr22_rRNA_transcripts.fa
cat chr22_rRNA_transcripts.fa | perl -ne 'if ($_ =~/^\>\d+\s+\w+\s+(ERCC\S+)[\+\-]/){print ">$1\n"}elsif($_ =~ /\d+\s+(ENST\d+)/){print ">$1\n"}else{print $_}' > chr22_rRNA_transcripts.clean.fa
cat chr22_rRNA_transcripts.clean.fa

cd $RNA_HOME/refs
kallisto index --index=chr22_rRNA_transcripts_kallisto_index chr22_rRNA_transcripts.clean.fa

```

We can now use this index with Kallisto to assess the abundance of rRNA genes in a set of samples.

##Exercise: Do a performance test using a real large dataset
Obtain an entire lane of RNA-seq data for a breast cancer cell line and matched 'normal' cell line here:

Tumor (<a href="https://xfer.genome.wustl.edu/gxfer1/project/gms/testdata/bams/hcc1395/gerald_C1TD1ACXX_8_ACAGTG.bam">download</a>)<br>
Normal (<a href="https://xfer.genome.wustl.edu/gxfer1/project/gms/testdata/bams/hcc1395/gerald_C2DBEACXX_3.bam">download</a>)

For more information on this data refer to this page:<br>
https://github.com/genome/gms/wiki/HCC1395-WGS-Exome-RNA-Seq-Data

Download the data

```bash

cd $RNA_HOME/data/
mkdir hcc1395
cd hcc1395
wget https://xfer.genome.wustl.edu/gxfer1/project/gms/testdata/bams/hcc1395/gerald_C1TD1ACXX_8_ACAGTG.bam
wget https://xfer.genome.wustl.edu/gxfer1/project/gms/testdata/bams/hcc1395/gerald_C2DBEACXX_3.bam

```

Since the paths above will download BAM files but Kallisto expects FASTQ files for the read data. You will need to convert from BAM back to FASTQ. Try using Picard to do this.

Example BAM to FASTQ conversion commands (note that you need to specify the correct path for your Picard installation), followed by compressing the resulting FastQ files to save space:

```bash

java -Xmx2g -jar /home/ubuntu/tools/picard.jar SamToFastq INPUT=gerald_C1TD1ACXX_8_ACAGTG.bam FASTQ=hcc1395_tumor_R1.fastq SECOND_END_FASTQ=hcc1395_tumor_R2.fastq VALIDATION_STRINGENCY=LENIENT
gzip hcc1395_tumor*.fastq
java -Xmx2g -jar /home/ubuntu/tools/picard.jar SamToFastq INPUT=gerald_C2DBEACXX_3.bam FASTQ=hcc1395_normal_R1.fastq SECOND_END_FASTQ=hcc1395_normal_R2.fastq VALIDATION_STRINGENCY=LENIENT
gzip hcc1395_normal*.fastq

```

Now repeat the concepts above to obtain abundance estimates for all genes.

Note:
- You will have to get all transcripts instead of just those for a single chromosome
- You will have to create a new index for this new set of transcript sequences
- Try using the `time` command in Unix to track how long the `kallisto index` and `kallisto quant` commands take
- In our tests, on an Amazon instance, using 6 threads, it took ~10 minutes to process each of the HCC1395 samples. Each of these samples has ~150 million paired-end reads.

| [[Previous Section|Transcript-Assembly-Visualization]]       | [[This Section|Kallisto]]   | [[Next Section|Abbreviations]]  |
|:------------------------------------------------------------:|:---------------------------:|:------------------------------:|
| [[Splicing Visualization|Transcript-Assembly-Visualization]] | [[Kallisto|Kallisto]]       | [[Abbreviations|Abbreviations]] |
