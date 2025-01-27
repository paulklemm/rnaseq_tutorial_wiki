#0-vi. Environment
This tutorial assumes use of a Linux computer with an 'x86_64' architecture.  The rest of the tutorial should be conducted in a linux Terminal session.  In other words you must already be logged into the Amazon EC2 instance as described in the previous section.

Before proceeding you must define a global working directory by setting the environment variable: 'RNA_HOME'  
Log into a server and SET THIS BEFORE RUNNING EVERYTHING.    

Create a working directory and set the 'RNA_HOME' environment variable

```bash

mkdir -p ~/workspace/rnaseq/

export RNA_HOME=~/workspace/rnaseq

```
	
Make sure whatever the working dir is, that it is set and is valid

```bash

echo $RNA_HOME

```

You can place the RNA_HOME variable in your .bashrc and then logout and login again to avoid having to worry about it. This has been done for you in the pre-configured amazon instance that you will be using.

Additional environment variables used throughout this tutorial:

```bash

export RNA_DATA_DIR=$RNA_HOME/data
export RNA_DATA_TRIM_DIR=$RNA_DATA_DIR/trimmed

export RNA_REFS_DIR=$RNA_HOME/refs
export RNA_REF_INDEX=$RNA_REFS_DIR/chr22_with_ERCC92
export RNA_REF_FASTA=$RNA_REF_INDEX.fa
export RNA_REF_GTF=$RNA_REF_INDEX.gtf

export RNA_ALIGN_DIR=$RNA_HOME/alignments/hisat2

```


| [[Previous Section|Unix-Bootcamp]]  | [[This Section|Environment]] | [[Next Section|Resources]] |
|:-----------------------------------:|:----------------------------:|:--------------------------:|
| [[Unix Bootcamp|Unix-Bootcamp]]     | [[Environment|Environment]]  | [[Resources|Resources]]    |
