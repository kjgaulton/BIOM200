## BIOM200 Human genetics and genomics module
<br/>

Kyle Gaulton, Assistant Professor, Department of Pediatrics

kgaulton@ucsd.edu

Fall Quarter 2019

<br/><br/>

##  Getting started

SSH into your account on TSCC (ssh username@login-tscc.sdsc.edu)

Clone this repository into your home directory on TSCC, i.e.:

```
git clone https://github.com/kjgaulton/BIOM200.git
```

<br/><br/>


## Exercise - prioritize clinically-significant variants from a human exome

The goal of this exercise is to identify potential pathogenic variants from human exome sequencing.

The Exome data you will annotate is in the repository you cloned here: **Exome VCF**: ~/BIOM200/data/hu82436A.vcf.gz

And you will annotate this exome using data on pathogenic variants from ClinVar:  **ClinVar VCF**: ~/BIOM200/data/clinvar_20190909.vcf.gz

Download VCFanno in order to functionally annotate variants
  Either download executable directly: 
  
  ```
  wget https://github.com/brentp/vcfanno/releases/download/v0.3.2/vcfanno_linux64 -O vcfanno
  ```
  
  Or install with conda:
  
  ```
  conda install -c bioconda vcfanno
  ```
  
  Make sure that you have permissions to run the program:
  
  ```
  chmod +x vcfanno
  ```
  
  

Run VCFanno to annotate exome with ClinVar, and ExAC and 1000 Genomes allele frequencies, using config file 'biom_config.toml' (there might be several 'warnings' but should still produce the correct output).  Make sure you are in your home directory when runnings this command

  ```
  ./vcfanno BIOM200/biom_config.toml BIOM200/data/hu82436A.vcf.gz > hu82436A.annot.vcf
  ```
  
This will add several columns to the INFO field of the VCF in the resulting annotated file 'hu82436A.annot.vcf', including: clinical_impact (benign, pathogenic, etc.), clinical_class (type of variant - snp, deletion, etc.), exac_allele_freq (allele frequency in ExAC), tgp_allele_freq (allele frequency in 1000 Genomes)

From this annotated VCF, extract all variants with clinical_impact=Pathogenic, and determine whether or not they are also present in ExAC or 1000 Genomes and what their allele frequencies are (if there is no 'exac_allele_freq' or 'tgp_allele_freq' info tag then it isn't in these databases)

**Questions**: 
1.  How many rare, likely pathogenic variants does this individual carry? 
2.  What types of variants are they (i.e. what changes do they make to the protein)? 
3.  What genes are they in, and what diseases do these variants cause?  

<br/><br/>
## Exercise - identify genes and pathways enriched in GWAS summary statistic data

The goal of this exercise is to take summary statistics from a GWAS of body-mass index (BMI), and identify genes and pathways enriched near significant BMI-associated loci.

BMI summary statistic data: /oasis/tscc/scratch/kgaulton/GIANT_BMI.tbl - copy into your home directory

  ```
  cp /oasis/tscc/scratch/kgaulton/GIANT_BMI.tbl.gz ~
  ```

Unzip the file:

 ```
 gunzip GIANT_BMI.tbl.gz
 ```

Download PLINK and BEDOPS:

  ```
  wget http://s3.amazonaws.com/plink1-assets/plink_linux_x86_64_20190617.zip
  unzip plink_linux_x86_64_20190617.zip
  
  wget https://github.com/bedops/bedops/releases/download/v2.4.36/bedops_linux_x86_64-v2.4.36.tar.bz2
  tar -xvjf bunzip2 bedops_linux_x86_64-v2.4.36.tar
  ```
  
Use PLINK to extract genome-wide significant variants and run LD pruning to retain one 'index' variant per locus: 
 
  ```
  plink --bfile /oasis/tscc/scratch/kgaulton/hapmap_CEU --clump GIANT_BMI.tbl --clump-p1 .00000005 --clump-r2 .5 --out BMI_vars
  ```
  
Format the PLINK output file 'BMI_vars.clumped' to produce a sorted .bed file and redirect to output file 'BMI_vars.bed':
  
  ```
  awk -v OFS='\t' '{ print "chr"$1,$4,$4,$3 }' BMI_vars.clumped | head -n -2 | sort -k1,1 -k2,2g > BMI_vars.sort.bed
  ```
  
Find gene closest to each 'index' BMI variant in the 'BMI_vars.sort.bed' file and redirect to output file 'BMI_genes.txt':

  ```
  closest-features --closest BMI_vars.sort.bed ~/BIOM200/data/GENCODE_V31.bed | awk '{ print $7 }' > BMI_genes.txt
  ```
  
Using this list of genes in 'BMI_genes.txt', run gene set enrichment analyses of Gene Ontology (GO) terms using GSEA:
http://software.broadinstitute.org/gsea/msigdb/annotate.jsp (you may have to register with your email)

**Questions:**
1.  What pathways are enriched in genes near BMI-associated variants?  What does this potentially tell us about the biology underlying obesity?
2.  Are there any specific genes near BMI-associated variants in these pathways with compelling biology or therapeutic value?
3.  **BONUS**:  What are the drawbacks to this approach to annotating genes at trait-associated loci?  What would be a better way to annotating genes affected by variants at these loci?


##  Potentially helpful commands

grep - extract information from a file 

more - view beginning of a file (hit spacebar scroll through more)

ls - list contents of a directory


