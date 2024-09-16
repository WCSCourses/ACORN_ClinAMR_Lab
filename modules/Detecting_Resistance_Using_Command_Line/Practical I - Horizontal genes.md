# Computational Practical 6
# Detecting resistance using command line I
## Module Developers: Mr. Collins Kigen, Dr. Stanford Kwenda
### 6.0 Introduction
Antibiotic resistance poses a significant health challenge because drugs that were once highly effective are no longer able to cure bacterial infections. The primary factor contributing to acquired antimicrobial resistance is often horizontal gene transfer between bacterial isolates. This transfer can occur through various mechanisms such as membrane vesicle fusion, transduction, transformation, and conjugation. In the first part of this practical, we will analyze Whole Genome Sequences to identify antimicrobial resistance genes acquired through horizontal gene transfer.
### 6.1 Bacterial strains to be analyzed
  1.	_Staphylococcus aureus_
  2.	_Klebsiella pneumoniae_
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
##### (i)	Check the help/manual
```
abricate –help
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


##### (ii)	Check the databases installed
```
abricate –list
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
#### (iii) Update databases
```
abricate-get_db --db ncbi
abricate-get_db --db card
abricate-get_db --db resfinder
```
```
abricate -list
```

#### 6.2.3	Detection of acquired antimicrobial resistance genes
Run abricate using resfinder database and save to a file
```
abricate -db resfinder data/Saureus/A1-1_S1_L001.fasta > A1-1_resfinder.tab
```

<pre>
abricate           : run the tool
-db                : flag used to specify the database to use
resfinder          : database. Alternatively, we can use NCBI or CARD here
A1-1_S1_L001.fasta : path to contig file
">"                : redirection symbol used to save the output to a file
A1-1_resfinder.tab : path to output file
</pre>


##### Open the file with the AMR results
```
less -S A1-1_resfinder.tab
```
<pre>
less                : bash command to open files one page at a time
-S                  : flag to prevent text wrapping
A1-1_resfinder.tab  : path to output file
</pre>

#### 6.2.4	Interpreting ABRIcate results
The table below includes some of the columns of the ABRicate resfinder output file 

| #FILE	| SEQUENCE | GENE | PRODUCT RESISTANCE |
|------ |----------|------|--------------------|
| A1-1_S1_L001.fasta | NODE_103_length_6610_cov_29.786534 | ant(9)-Ia	| | 
| A1-1_S1_L001.fasta | NODE_103_length_6610_cov_29.786534 | erm(A) |	Erythromycin; Lincomycin; Clindamycin; Quinupristin; Pristinamycin_IA; Virginiamycin_S |
| A1-1_S1_L001.fasta |	NODE_14_length_45910_cov_19.064907 | mecA | Amoxicillin; Amoxicillin+Clavulanic_acid; Ampicillin; Ampicillin+Clavulanic_acid; Cefepime;Cefixime; Cefotaxime; Cefoxitin; Ceftazidime; Ertapenem;Imipenem; Meropenem; Piperacillin; Piperacillin+Tazobactam |
| A1-1_S1_L001.fasta | NODE_51_length_21735_cov_23.672622 | tet(M) | Doxycycline; Tetracycline; Minocycline |


