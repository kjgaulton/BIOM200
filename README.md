## BIOM200 Human genetics and genomics module
<br/>

Kyle Gaulton, Assistant Professor, Department of Pediatrics

kgaulton@ucsd.edu

Fall Quarter 2019

<br/><br/>

##  Getting started

Clone this repository into your home directory on TSCC, i.e.:

```git clone https://github.com/kjgaulton/BIOM200.git```

<br/><br/>


## Exercise - prioritize clinically-significant variants from a human exome

The goal of this exercise is to identify potential pathogenic variants from human exome sequencing.

- Copy human exome VCF hu82436A.vcf.gz and index hu82436A.vcf.gz.tbi into your home directory from /oasis/tscc/scratch/kgaulton/

- Download VCFanno in order to functionally annotate variants
  Either download executable directly: 
  
  ``` wget https://github.com/brentp/vcfanno/releases/download/v0.3.2/vcfanno_linux64```
  
  Or install with conda:
  
  ```conda install -c bioconda vcfanno```
  
- Copy ClinVar VCF clinvar_20190909.vcf.gz and index into your home directory from /oasis/tscc/scratch/kgaulton/

- Run VCFanno to annotate exome with ClinVar using config file 'biom_config.toml' (there might be several 'warnings' but should still produce the correct output)

  ```./vcfanno biom_config.toml hu82436A.vcf.gz > hu82436A.annot.vcf```
  
- This will add several columns to the INFO field of the VCF in the annotated file 'hu82436A.annot.vcf', including: clinical_impact (benign, pathogenic, etc.), clinical_class (type of variant - snp, deletion, etc.), exac_allele_freq (allele frequency in ExAC), tgp_allele_freq (allele frequency in 1000 Genomes)

From this annotated VCF, find all variants with clinical_impact=Pathogenic, and not present in ExAC or 1000 Genomes (meaning theres is no 'exac_allele_freq' or 'tgp_allele_freq' info tag)

**Questions**: 
1.  How many rare, likely pathogenic variants does this individual carry? 
2.  What types of variants are they (i.e. what changes do they make to the protein)? 
3.  What genes are they in, and what diseases do these variants cause?  

<br/><br/>
## Exercise - identify genes and pathways enriched in GWAS summary statistic data

The goal of this exercise is to take summary statistics from a GWAS of body-mass index (BMI), and identify genes and pathways enriched near significant BMI-associated loci.

- Copy BMI summary data GIANT_BMI.tbl to your home directory from /oasis/tscc/scratch/kgaulton/

- Download PLINK:

  ```wget http://s3.amazonaws.com/plink1-assets/plink_linux_x86_64_20190617.zip
  
  unzip plink_linux_x86_64_20190617.zip```
  
- Use PLINK to extract genome-wide significant variants and run LD pruning to retain one 'index' variant per locus: 
 
  ```plink --bfile /oasis/tscc/scratch/kgaulton/hapmap_CEU --clump GIANT_BMI.tbl --clump-p1 .00000005 --clump-r2 .5 --out BMI_vars```
  
- Format PLINK output file to .bed format:
  
  ```awk -v OFS='\t' '{ print "chr"$1,$4,$4,$3 }' BMI_vars.clumped | head -n -2 | sort -k1,1 -k2,2g > BMI_vars.bed```
- 
