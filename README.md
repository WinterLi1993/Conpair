# Conpair
Conpair: concordance and contamination estimator for tumor–normal pairs

Conpair is a fast and robust method dedicated for human tumor-normal studies to perform concordance verification (= samples coming from the same individual), as well as cross-individual contamination level estimation in whole-genome and whole-exome sequencing experiments. Importantly, our method of estimating contamination in the tumor samples is not affected by copy number changes and is able to detect contamination levels as low as 0.1%.

* Version: 0.1 (more options to be added in the future release)
* Author: Ewa A Grabowska, [New York Genome Center](https://www.nygenome.org)
* Contact: egrabowska@nygenome.org

**Required input files:** two bam files (tumor, normal)

**Required software:** GATK 2.3 or later, python 2.7, scipy, numpy, java

**Required data:** Human genome file (GRCh37)  
  
The fasta file can be downloaded from: ftp://ftp.1000genomes.ebi.ac.uk/vol1/ftp/technical/reference/human_g1k_v37.fasta.gz  
In order to be able use the fasta file as a reference 2 additional files are required: 

`human_g1k_v37.dict`  
`human_g1k_v37.fa.fa`  

To create them please follow: http://gatkforums.broadinstitute.org/gatk/discussion/1601/how-can-i-prepare-a-fasta-file-to-use-as-reference


# Manual

**Setting environmental variables:**   
To use Conpair you need to set 2 environmental variables and a PYTHONPATH variable (e.g. by adding following lines to your .bashrc file):  
```
export CONPAIR_DIR=/your/path/to/CONPAIR  
export GATK_JAR=/your/path/to/GenomeAnalysisTK.jar

export PYTHONPATH=${PYTHONPATH}:/your/path/to/CONPAIR/modules/
```

To avoid specifying the reference file every time you run Conpair, please make sure that you have the following files:
`/your/path/to/CONPAIR/data/genomes/human_g1k_v37.fa`  
`/your/path/to/CONPAIR/data/genomes/human_g1k_v37.fa.fai`  
`/your/path/to/CONPAIR/data/genomes/human_g1k_v37.dict`
<br/>

**Most common usage:**   
To generate pileups (GATK required):
```
${CONPAIR_DIR}/scripts/run_gatk_pileup_for_sample.py -B TUMOR_bam -O TUMOR_pileup
${CONPAIR_DIR}/scripts/run_gatk_pileup_for_sample.py -B NORMAL_bam -O NORMAL_pileup
```
Verifying concordance between two samples (tumor and normal):
```  
${CONPAIR_DIR}/scripts/verify_concordance.py -T TUMOR_pileup -N NORMAL_pileup [-O OUTFILE] [-H USE ONLY HOMOZYGOUS MARKERS]
```  
Estimating contamination level in both the tumor and the normal:
```
${CONPAIR_DIR}/scripts/estimate_tumor_normal_contamination.py -T TUMOR_pileup -N NORMAL_pileup [-O OUTFILE]
```  


# Interpretation  
**Concordance**  
To eliminate the effect of copy number variation on the concordance levels, we recommend using the -H flag. 
If two samples are concordant the expected concordance level should be close to 99-100%.  
For discordant samples concordance level should be close to 40%.  
You can observe slighly lower concordance (80-99%) in presence of contamination and/or copy number changes (if the -H option wasn't used) is at least one of the samples.   
<br/>
**Contamination**   
Even a very low contamination level (such as 0.5%) in the tumor sample will have a severe effect on calling somatic mutations, resulting in decreased specificity. Cross-individual contamination in the normal sample usually has a milder effect on somatic calling.
