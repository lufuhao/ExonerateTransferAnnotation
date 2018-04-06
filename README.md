# ExonerateTransferAnnotation


## Description

+  Transfer GFF3 to new assemblies
+  Reccommend: use [RATT](http://www.sanger.ac.uk/science/tools/pagit) first
+  Use this tool for some remaining genes
>  Note: Exonerate might not transfer short exon/CDS. so need a further check
>        check method included

## Steps

### Step1: get cDNA and CDS sequence for each mRNA separately

1. Requirements
    gff2fasta_luf.pl available at https://github.com/lufuhao/FuhaoBin/ subfolder FuhaoPerl 

2. CMD

  For cDNA sequences
    output: "your_prefix.cdna.fasta"
>    gff2fasta_luf.pl -f genome.fa -g your.gff3 -s 3 -p your_prefix

  For CDS sequences
    output: "your_prefix.cds.fasta"
>    gff2fasta_luf.pl -f genome.fa -g your.gff3 -s 3 -p your_prefix

  Output gene ID list for later
    outputL: "your_prefix.genelist"
>    perl -lane 'if ($F[2]=~/^gene$/i) {$F[8]=~s/^.*ID=//;$F[8]=~s/;.*$//;print $F[8];}' your.gff3 | sort -u > your_prefix.genelist

### Step2: run Exonerate in server mode

1. Requirements
    

