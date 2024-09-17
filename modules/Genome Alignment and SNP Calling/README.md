# Genome Alignment and SNP Calling of Bacterial Genomes: A Tutorial for ACORN Integrated AMR Analysis Workshop in Kilifi, Kenya (September 2024)

@Author: Arun Decano

## Introduction

Variant calling involves crucial steps in comparative genomics that researchers to identify genetic variations between bacterial strains. Short-read sequencing data (from Illumina) and long-read sequencing data (from PacBio or Oxford Nanopore) have distinct characteristics, each with specific tools and methods for optimal processing. This tutorial provides step-by-step instructions for performing SNP (Single Nucleotide Polymorphism) and other forms of variant calling in bacterial genomes using both short-read and long-read (nanopore) sequencing data.

## Prerequisites

Before starting, ensure you have the following software installed:
- For short reads:
  - snippy
  - samtools
  - bcftools
- For long reads:
  - minimap2
  - samtools
  - Medaka
  - DeepVariant
- General tools:
  - FastQC for quality control
  - MultiQC for aggregating QC reports
  - snpEff

## Data Preparation

### 1. Make sure you're using the training environment and in the correct working directory where the files are accessible,

     ```
     conda activate training
     ```

     ```
     cd /data/acorn_training_course_2024/RawRead/kleb
     ```
     
### 2. Quality Control:
   - Use FastQC to assess the quality of your sequencing reads.

  > You may need to create an environmnt for fastqc
     
     ```
     fastqc *.fastq
     ```
   - Aggregate the results with MultiQC.
     ```
     multiqc .
     ```

### 3. Cleaning (if necessary):
   - Use Fastp for short reads to remove adapters and low-quality bases.
     
     ```
     fastp -i in.R1.fq.gz -I in.R2.fq.gz -o out.R1.fq.gz -O out.R2.fq.gz
     ```

    
    
## Short Read Alignment and SNP Calling with Snippy

Snippy is an all-in-one tool for bacterial SNP calling using short-read data. It aligns the reads to a reference genome and calls variants.

### 1. Run Snippy:
   - Use snippy to perform alignment and SNP calling in one step.
     ```
     snippy --cpus 4 --outdir snippy_output --reference reference.fasta --R1 output_R1_paired.fastq --R2 output_R2_paired.fastq
     ```

### 2. Examine the Output:
     
   - The output directory will contain various files, including:
     - snps.vcf: The called SNPs in VCF format.
     - snps.tab: A tabular summary of SNPs.
     - alignment.bam: The aligned reads in BAM format.
     - alignment.bam.bai: The BAM index file.

## Long Read Alignment with minimap2 and SNP Calling with Medaka or DeepVariant.

### Run Medaka:
  - Use Medaka to call SNPs from the long-read BAM file.

    ```
    conda activate medaka_env
    ```

    ```
    #medaka_haploid_variant -i longread.input.fastq.gz -r reference.fasta

    medaka_haploid_variant -i SQK-NBD114-24_barcode01.fastq -r Ref_Kpn_MGH78578.fasta
    
    ```

### Run DeepVariant:
   - Use minimap2 for aligning long reads.
     ```
     minimap2 -a reference.fasta long_reads.fastq > aligned_long_reads.sam
     ```
     
   - Convert and sort the alignment.
     ```
     samtools view -S -b aligned_long_reads.sam > aligned_long_reads.bam
     samtools sort aligned_long_reads.bam -o sorted_long_reads.bam
     ```
     
   - Index the BAM file.
     ```
     samtools index sorted_long_reads.bam
     ```
   - Remove duplicates.
     ```
     samtools rmdup sorted_long_reads.bam deduplicated.bam
     ```

   - Use DeepVariant to call SNPs from the long-read BAM file.
     ```
     # Assuming DeepVariant is installed and properly set up

     run_deepvariant --model_type PACBIO --ref reference.fasta --reads sorted_long_reads.bam --output_vcf dv_output.vcf --output_gvcf dv_output.g.vcf --num_shards 4
     ```
### Use PEPPER DeepVariant for nanopore reads: https://github.com/kishwarshafin/pepper

```
## Pull the docker image.
sudo docker pull kishwars/pepper_deepvariant:r0.8

# Run PEPPER-Margin-DeepVariant
sudo docker run \
-v "${INPUT_DIR}":"${INPUT_DIR}" \
-v "${OUTPUT_DIR}":"${OUTPUT_DIR}" \
kishwars/pepper_deepvariant:r0.8 \
run_pepper_margin_deepvariant call_variant \
-b "${INPUT_DIR}/${BAM}" \
-f "${INPUT_DIR}/${REF}" \
-o "${OUTPUT_DIR}" \
-p "${OUTPUT_PREFIX}" \
-t "${THREADS}" \
--ont_r10_q20
```


## Post-processing and Analysis

### 1. Filter SNPs:
   - Apply filters to the VCF files to ensure high-quality SNPs using bcftools. Remove SNPs/Indels with MQ<30 and DP<10.
     ```
     bcftools filter -s LowQual -e '%QUAL<30 || DP<10' snippy_output/snps.vcf > filtered_snps_short.vcf
     bcftools filter -s LowQual -e '%QUAL<30 || DP<10' medaka.annotated.vcf > filtered_snps_long.vcf
     ```

### 2. Annotation of SNPs:
   - Use snpEff to annotate the SNPs: https://pcingola.github.io/SnpEff/snpeff/running/
     
     ```
     #short reads
      java -Xmx8g -jar snpEff.jar <reference_database> filtered_snps_short.vcf > annotated_snps_short.vcf

     #long reads
      java -Xmx8g -jar snpEff.jar reference filtered_snps_long.vcf > annotated_snps_long.vcf
     ```

### 3. Visualization:
   - Visualize the alignment and SNPs using tools using Integrative Genomics Viewer (IGV).
   - Download IGV from https://igv.org/doc/desktop/#DownloadPage/.
   - Example pop-up info of a selected SNP:
     
ID: .

Chr: NC_009648.1

Position: 2,803,960

Reference: C*

Alternate: T

Qual: 882.977

Type: SNP

Is Filtered Out: No

Alleles:

Alternate Alleles: T

*Variant Attributes*

QA: 1039

AB: 0

QR: 32

Depth: 30

RO: 1

TYPE: snp

AO: 29

   - Explanation of each metric:

*Basic Information*

ID: This field is empty, indicating no specific identifier (such as a dbSNP ID) is assigned to this SNP.

Chr: NC_009648.1 is the chromosome or contig reference name from the genome assembly.

Position: 2,803,960 is the position on the chromosome where this SNP is located.

Reference: C* indicates the reference allele (the allele found in the reference genome) at this position is "C".

Alternate: T is the alternate allele observed in this SNP, meaning a "C" in the reference is replaced by "T" in the observed variant.

Qual (Quality): 882.977 is a quality score for the variant call. It indicates the confidence in the variant being true; higher scores mean greater confidence.

Type: SNP specifies that this variant is a single nucleotide polymorphism.

Is Filtered Out: No indicates that this SNP has passed any filters applied during variant calling, suggesting it is considered a reliable call.

*Alleles Information*

Alternate Alleles: T is the alternate allele for this SNP.

*Variant Attributes*

QA (Quality of Alternate): 1039 is the sum of base quality scores for the reads supporting the alternate allele (T). Higher values suggest more confidence in the variant call.

AB (Allele Balance): 0 represents the proportion of reads supporting the alternate allele relative to the total depth. Since it is 0, this may mean that the calculation could not be performed, or the data isn't available.

QR (Quality of Reference): 32 is the sum of base quality scores for the reads supporting the reference allele (C). Lower values suggest fewer or less confident reads supporting the reference allele.

Depth: 30 is the total read depth at this position, representing the number of reads covering the SNP site.

RO (Reference Observations): 1 indicates the number of reads supporting the reference allele (C).

TYPE: snp reaffirms that this variant is a single nucleotide polymorphism.

AO (Alternate Observations): 29 is the number of reads supporting the alternate allele (T).


## References

1. Bush SJ, Foster D, Eyre DW, Clark EL, De Maio N, Shaw LP, Stoesser N, Peto TEA, Crook DW, Walker AS, Wilson DJ. (2020). Genomic diversity affects the accuracy of bacterial SNP calling pipelines. Genome Biology, 21, 20. https://doi.org/10.1186/s13059-019-1921-9

2. Li H. (2018). Minimap2: pairwise alignment for nucleotide sequences. Bioinformatics, 34(18), 3094-3100. https://doi.org/10.1093/bioinformatics/bty191

3. Poplin R, Chang PC, Alexander D, Schwartz S, Colthurst T, Ku A, Newburger D, Dijamco J, Nguyen N, Afshar PT, Gross SS, Dorfman L, McLean CY, DePristo MA. (2018). A universal SNP and small-indel variant caller using deep neural networks. Nature Biotechnology, 36(10), 983-987. https://doi.org/10.1038/nbt.4235

4. Cingolani P, Platts A, Wang LL, Coon M, Nguyen T, Wang L, Land SJ, Lu X, Ruden DM. (2012). A program for annotating and predicting the effects of single nucleotide polymorphisms, SnpEff: SNPs in the genome of Drosophila melanogaster strain w1118; iso-2; iso-3. Fly, 6(2), 80-92. https://doi.org/10.4161/fly.19695

5. Robinson JT, Thorvaldsdóttir H, Winckler W, Guttman M, Lander ES, Getz G, Mesirov JP. (2011). Integrative genomics viewer. Nature Biotechnology, 29(1), 24-26. https://doi.org/10.1038/nbt.1754

6. Andrews S. (2010). FastQC: A quality control tool for high throughput sequence data. Available online: http://www.bioinformatics.babraham.ac.uk/projects/fastqc

7. Ewels P, Magnusson M, Lundin S, Käller M. (2016). MultiQC: summarize analysis results for multiple tools and samples in a single report. Bioinformatics, 32(19), 3047-3048. https://doi.org/10.1093/bioinformatics/btw354



