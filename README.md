### BIOM200 Human genetics and genomics module
<br/>

Kyle Gaulton, kgaulton@ucsd.edu
Fall 2019


Clone this repository into your home directory, i.e.:

```git clone https://github.com/kjgaulton/BIOM200.git```

<br/><br/>


#### Exercise - prioritize clinically-significant variants from a human exome
***

The goal of this exercise is to identify potential pathogenic variants from human exome sequencing.

- Copy human exome VCF - hu82436A.vcf.gz from /oasis/tscc/scratch/kgaulton/ (don't forget index .tbi)

- Download VCFanno in order to functionally annotate variants
  Either download executable directly: 
  
  ``` wget https://github.com/brentp/vcfanno/releases/download/v0.3.2/vcfanno_linux64```
  
  Or install with conda:
  
  ```conda install -c bioconda vcfanno```
  
- Copy ClinVar VCF - clinvar_20190909.vcf.gz and index from /oasis/tscc/scratch/kgaulton/

- Run VCFanno using config file 'biom_config.toml'

  ```./vcfanno biom_config.toml hu82436A.vcf.gz > hu82436A.annot.vcf```
  
- This will add several columns to the INFO field of the VCF, including: clinical_significance (benign, pathogenic, etc.), clinical_class (type of variant - snp, deletion, etc.), af_exac (allele frequency in ExAC)

- From this annotated VCF, find all variants with clinical_significance=Pathogenic, and no af_exac value (meaning is it not present in ExAC)

- What gene(s) are these variants in, and by searching the variant ID online what diseases do they cause?  



#### Exercise - identify genes and pathways enriched in GWAS summary statistic data
***
