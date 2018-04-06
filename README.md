# ExonerateTransferAnnotation


## Description

+  Transfer GFF3 to new assemblies
+  Reccommend: use [RATT](http://www.sanger.ac.uk/science/tools/pagit) first
+  Use this tool for some remaining genes
+  More options available for each script by add '-h' or '--help'

    Note: 
      * exonerate might not transfer short exon/CDS very well
      * so need a further check; Method included as below.

## Steps

### Step1: get cDNA and CDS sequence for each mRNA separately

#### 1.1 Requirements

  * gff2fasta_luf.pl :                        [FuhaoBin](https://github.com/lufuhao/FuhaoBin/) subfolder FuhaoPerl

#### 1.2 CMD

  For cDNA sequences

    Output: "your_prefix.cdna.fasta"


>    ```<span style="color:blue">gff2fasta_luf.pl -f old_genome.fa -g your.gff3 -s 3 -p your_prefix</span>```


  For CDS sequences

    Output: "your_prefix.cds.fasta"

>    gff2fasta_luf.pl -f old_genome.fa -g your.gff3 -s 3 -p your_prefix

  Output gene ID list for later

    Output: "your_prefix.genelist"

>    perl -lane 'if ($F[2]=~/^gene$/i) {$F[8]=~s/^.\*ID=//;$F[8]=~s/;.\*$//;print $F[8];}' your.gff3 | sort -u > your_prefix.genelist

### Step2: run Exonerate in server mode

#### 2.1. Requirements

  * exonerate_server_running_est2genome.sh  :   Included
  * fasta_splitter.pl :                        [FuhaoBin](https://github.com/lufuhao/FuhaoBin/) subfolder FuhaoPerl
  * exonerate_gff2_to_gff3.pl :               [FuhaoBin](https://github.com/lufuhao/FuhaoBin/) subfolder FuhaoPerl
  * [Exonerate](https://www.ebi.ac.uk/about/vertebrate-genomics/software/exonerate)

#### 2.2. CMD

- Set up Exonerate server on your new_genome.fa

    * Convert genome to esd database; use '-s TRUE' if your genome softmasked
    * Index EST file; memory limit could be higher if allowed
    * Load database to server, Note the server port number \[Default: 12886\] if use a different one

> fasta2esd -s FALSE -f new_genome.fa -o your_prefix.esd
>
> esd2esi $opt_p.esd your_prefix.esi --memorylimit 1024
>
> exonerate-server your_prefix.esi --port 12886  &

- Map cDNA and CDS separately 

    * Split fasta file into small ones to accelerate mapping
    * Exonerate mapping
    * Convert Exonerate GFF2 to GFF3

    Output: your_prefix.cdna.EXONERATE_OUT.gff3

> exonerate_server_running_est2genome.sh -i your_prefix.cdna.fasta -r 12886 -s -p your_prefix.cdna

    Output: your_prefix.cds.EXONERATE_OUT.gff3

> exonerate_server_running_est2genome.sh -i your_prefix.cds.fasta -r 12886 -s -p your_prefix.cds



    Note:
      * To close Exonerate server, add '-x' option in your last exonerate_server_running_est2genome.sh run or use 'ps -ef' to check exonerate process number and kill it

      * use '-b [INT]' to declare the bestn number you want to avoid unwanted mapping

### Step3: Intergrate cDNA and CDS 

#### 3.1. Requirements

  * exonerate_cDNA_CDS_merge_to_GFF3.sh :  Included
  * exonerate_GFF3_reformat.pl :           Included

#### 3.2. CMD

    Output: your_prefix.integrated.gff3

> exonerate_cDNA_CDS_merge_to_GFF3.sh -i your_prefix.genelist -n your_prefix.cdna.EXONERATE_OUT.gff3 -s your_prefix.cds.EXONERATE_OUT.gff3 -o your_prefix.integrated.gff3

### Step4: Inspect GFF3 with genome viewer

#### 4.1. Requirements

  * gff3_checker.pl :             [FuhaoBin](https://github.com/lufuhao/FuhaoBin/) subfolder FuhaoPerl

#### 4.2. Requirements

    Output: your_prefix.integrated.correct.gff3

> gff3_checker.pl your_prefix.integrated.gff3 your_prefix.integrated.correct.gff3 new_genome.fa > gff3checker.log 2>&1

    Note:
      * check log file 'gff3checker.log' to view the error message
      * Manually correct the error in your your_prefix.integrated.gff3
      * Need genome browser: IGV


## Author:
>
>  Fu-Hao Lu
>
>  Post-Doctoral Scientist in Micheal Bevan laboratory
>
>  Cell and Developmental Department, John Innes Centre
>
>  Norwich NR4 7UH, United Kingdom
>
>  E-mail: Fu-Hao.Lu@jic.ac.uk
