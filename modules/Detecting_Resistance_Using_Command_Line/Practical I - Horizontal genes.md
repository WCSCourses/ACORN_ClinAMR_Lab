# Computational Practical 6
# Detecting resistance using command line I
## Module Developers: Mr. Collins Kigen, Dr. Stanford Kwenda
### 6.0 Introduction
Antibiotic resistance poses a significant health challenge because drugs that were once highly effective are no longer able to cure bacterial infections. The primary factor contributing to acquired antimicrobial resistance is often horizontal gene transfer between bacterial isolates. This transfer can occur through various mechanisms such as membrane vesicle fusion, transduction, transformation, and conjugation. In the first part of this practical, we will analyze Whole Genome Sequences to identify antimicrobial resistance genes acquired through horizontal gene transfer.
### 6.1 Bacterial strains to be analyzed
  1.	_Staphylococcus aureus_ (n=46)
  2.	_Klebsiella pneumoniae_ (n=47)

#### Step 1: Lets start by activating the environment with abricate 
```
conda activate training
```
#### Step 2: Copy the data to your own home directory
```
cd ~
mkdir -p ACORN_course/cp6/data
rsync -avP /data/acorn_training_course_2024/saureus/saureus ACORN_course/cp6/data/
rsync -avP /data/acorn_training_course_2024/klebsiella/klebsiella ACORN_course/cp6/data/
cd ACORN_course/cp6/
```
### 6.2 WGS-based prediction of AMR using ABRicate
#### 6.2.1 Introduction to ABRicate 
ABRicate is a tool for mass screening of contigs for antimicrobial resistance or virulence genes. The name ABRicate was chosen as the first 3 letters are a common acronym for “Anti-Biotic Resistance” 
1.	It only supports contigs, not FASTQ reads
2.	It only detects acquired resistance genes, NOT point mutations
3.	It uses a DNA sequence database, not protein
4.	It comes bundled with multiple databases:

|Abbrv. | Full name|
|--------|---------|
| NCBI | The National Center for Biotechnology Information | 
| CARD | The Comprehensive Antibiotic Resistance Database |
| ARG-ANNOT |	Antibiotic Resistance Gene-ANNOTation |
| Resfinder |	Resfinder |
| MEGARES |	MEGARES |
| EcOH | EcOH |
| PlasmidFinder |	PlasmidFinder |
| Ecoli_VF |	Escherichia coli virulence factors |
| VFDB | Virulence Factor Database |


#### 6.2.2 ABRicate commands
##### Step 3: (i)	Check the help/manual
```
abricate --help
```
<pre>
SYNOPSIS
  Find and collate amplicons in assembled contigs
AUTHOR
  Torsten Seemann (@torstenseemann)
USAGE
  % abricate --list
  % abricate [options] <contigs.{fasta,gbk,embl}[.gz] ...> > out.tab
  % abricate [options] --fofn fileOfFilenames.txt > out.tab
  % abricate --summary <out1.tab> <out2.tab> <out3.tab> ... > summary.tab
GENERAL
  --help                This help.
  --debug               Verbose debug output.
  --quiet               Quiet mode, no stderr output.
  --version             Print version and exit.
  --check               Check dependencies are installed.
  --threads             [N] Use this many BLAST+ threads [1].
  --fofn [X]            Run on files listed in this file [].
DATABASES
  --setupdb             Format all the BLAST databases.
  --list                List included databases.
  --datadir [X]         Databases folder [/opt/homebrew/Cellar/abricate/1.0.1_2/libexec/db].
  --db [X]              Database to use [ncbi].
OUTPUT
  --noheader            Suppress column header row.
  --csv                 Output CSV instead of TSV.
  --nopath              Strip filename paths from FILE column.
FILTERING
  --minid [n.n]         Minimum DNA %identity [80].
  --mincov [n.n]        Minimum DNA %coverage [80].
MODE
  --summary             Summarize multiple reports into a table.
DOCUMENTATION
  https://github.com/tseemann/abricate
</pre>


##### Step 4: (ii)	Check the databases installed
```
abricate --list
```


<pre>

DATABASE        SEQUENCES   DBTYPE  	DATE
card            2631        nucl    	2023-Nov-4
ecoli_vf        2701        nucl    	2023-Nov-4
plasmidfinder 	460         nucl    	2023-Sep-9
ecoh            597         nucl    	2023-Sep-9
ncbi          	5386        nucl    	2023-Sep-9
resfinder      	3077        nucl    	2023-Sep-9

</pre>
#### Step 5: (iii) Update databases
```
abricate-get_db --db ncbi
abricate-get_db --db card
abricate-get_db --db resfinder
```
```
abricate --list
```

#### 6.2.3	Detection of acquired antimicrobial resistance genes
Run abricate using resfinder database and save to a file

**Step 6: Create results directory**
```
mkdir -p amr_genes
```
**Step 7: run abricate**
```
abricate -db resfinder data/saureus/A1-1_S1_L001.fasta > amr_genes/A1-1_resfinder.tab
```

<pre>
abricate           : run the tool
-db                : flag used to specify the database to use
resfinder          : database. Alternatively, we can use NCBI or CARD here
A1-1_S1_L001.fasta : path to contig file
">"                : redirection symbol used to save the output to a file
A1-1_resfinder.tab : path to output file
</pre>


##### Step 8: Open the file with the AMR results
**Navigate to results directory**
```
cd amr_genes
```
**List the output files**
```
ls
```
**Open the results file**
```
less -S A1-1_resfinder.tab
```
<pre>
less                : bash command to open files one page at a time
-S                  : flag to prevent text wrapping
A1-1_resfinder.tab  : path to output file
</pre>
>**NOTE:** To exit from less, press q
>

**Step 9: Let's repeat the same steps using the NCBI database**
```
cd ..
abricate -db ncbi data/saureus/A1-1_S1_L001.fasta > amr_genes/A1-1_ncbi.tab
cd amr_genes
less -S A1-1_ncbi.tab
```

**What difference do you observe?**
#### 6.2.4	Interpreting ABRIcate results
The table below includes some of the columns of the ABRicate resfinder output file 

| #FILE	| SEQUENCE | GENE | PRODUCT RESISTANCE |
|------ |----------|------|--------------------|
| A1-1_S1_L001.fasta | NODE_103_length_6610_cov_29.786534 | ant(9)-Ia	| | 
| A1-1_S1_L001.fasta | NODE_103_length_6610_cov_29.786534 | erm(A) |	Erythromycin; Lincomycin; Clindamycin; Quinupristin; Pristinamycin_IA; Virginiamycin_S |
| A1-1_S1_L001.fasta |	NODE_14_length_45910_cov_19.064907 | mecA | Amoxicillin; Amoxicillin+Clavulanic_acid; Ampicillin; Ampicillin+Clavulanic_acid; Cefepime;Cefixime; Cefotaxime; Cefoxitin; Ceftazidime; Ertapenem;Imipenem; Meropenem; Piperacillin; Piperacillin+Tazobactam |
| A1-1_S1_L001.fasta | NODE_51_length_21735_cov_23.672622 | tet(M) | Doxycycline; Tetracycline; Minocycline |

#### 6.2.5 Combining reports across samples
ABRicate can combine results into a simple matrix of gene presence/absence. An absent gene is denoted . and a present gene is represented by its '%COVERAGE`. This can be individual abricate reports, or a combined one.

##### Step 10: Run abricate on `A1-2_S2_L001.fasta`
```
cd ~/ACORN_course/cp6/
abricate -db resfinder data/saureus/A1-2_S2_L001.fasta > amr_genes/A1-2_resfinder.tab
````
##### Step 11: Combine
```
cd amr_genes
abricate --summary A1-1_resfinder.tab A1-2_resfinder.tab
```
| #FILE |	NUM_FOUND |	ant(9)-Ia_1	| aph(2'')-Ia_2	| blaZ_79 |	erm(A)_1	|erm(B)_18	| mecA_6	|tet(M)_7 |
|-----|----------|-------------|----------------|--------|-----------|----------|----------------|----|
|A1-1_resfinder.tab|	4	|100.00|	.	|.|	100.00|	.|	100.00|	100.00|
|A1-2_resfinder.tab|	4|	.	|100.00|	100.00|	.|	100.00	|100.00|	.|


### Step 12: To run all the genomes in a single report
```
cd ~/ACORN_course/cp6
```
```
abricate -db ncbi data/saureus/*.fasta > amr_genes/all_saureus_amr_genes.tab
abricate --summary amr_genes/all_saureus_amr_genes.tab > amr_genes/saureus_amr_summary.tab
```
#### Step 13: View the results
```
cd amr_genes
less -S saureus_amr_summary.tab
```
## Exercises
1. Run abricate on _S. aureus_ sample A5-17_S17_L001.fasta against the CARD database and list all the amr genes present.
2. From the resfinder database, identify the following:
   
   (i) The samples with ant(4')-Ia gene
   
   (ii) The sample with the largest number of AMR genes. How many are they?
## AMR Genes in _Klebsiella pneumoniae_ genomes

*Run abricate on all the _Klebsiella genomes_ against the NCBI database into a single report and generate a summary report
