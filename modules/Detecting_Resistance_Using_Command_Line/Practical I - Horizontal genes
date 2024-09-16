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
card            2631        nucl    	2024-Sep-9
ecoli_vf        2701        nucl    	2024-Sep-9
plasmidfinder 	460         nucl    	2024-Sep-9
ecoh            597         nucl    	2024-Sep-9
ncbi          	5386        nucl    	2024-Sep-9
resfinder      	3077        nucl    	2024-Sep-9

</pre>


#### 6.2.3	Detection of acquired antimicrobial resistance genes
Run abricate using resfinder database and save to a file
```
abricate -db resfinder A1-1_S1_L001.fasta > A1-1_resfinder.tab
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
| A1-1_S1_L001.fasta | NODE_103_length_6610_cov_29.786534 | ant(9)-Ia_1	| | 
| A1-1_S1_L001.fasta | NODE_103_length_6610_cov_29.786534 | erm(A)_1 |	Erythromycin; Lincomycin; Clindamycin; Quinupristin; Pristinamycin_IA; Virginiamycin_S |
| A1-1_S1_L001.fasta |	NODE_14_length_45910_cov_19.064907 | mecA_6 | Amoxicillin; Amoxicillin+Clavulanic_acid; Ampicillin; Ampicillin+Clavulanic_acid; Cefepime;Cefixime; Cefotaxime; Cefoxitin; Ceftazidime; Ertapenem;Imipenem; Meropenem; Piperacillin; Piperacillin+Tazobactam |
| A1-1_S1_L001.fasta | NODE_51_length_21735_cov_23.672622 | tet(M)_7 | Doxycycline; Tetracycline; Minocycline |


## Computational Practical 6: Detecting resistance using command line II
In this practical we will focus on detecting chromosomal point mutations associated with antimicrobial resistance. Chromosomal mutations conferring resistance to antibiotics occur as spontaneous, random, and relatively rare alterations in the DNA composition of bacteria. 

### Mechanisms of Mutational Resistance to Antibiotics.

* the target site is altered so that binding of the antibiotic is reduced or eliminated,
* there is a block in the transport of the drug into the cell,
* the antibiotic is detoxified or inactivated, or
* the inhibited step in a metabolic pathway is bypassed.
  
### Point Finder
PointFinder is a tool designed for detecting antimicrobial resistance in bacterial pathogens through Whole Genome Sequencing (WGS) by identifying chromosomal point mutations. It consists of two databases: one containing chromosomal gene sequences in fasta format, and the other with information on codon positions and mutations. PointFinder utilizes BLASTn to find the best match for each gene in the chromosomal gene database, analyzing only those hits with ≥80% identity. The tool examines each alignment by comparing query positions with corresponding positions in the database sequence. Any mismatches are saved and compared against the chromosomal mutation database. Users can choose to view all mismatches or only those that are known and found at specific positions in the chromosomal database.

### Commands
#### Help page
```
cd ...
```
```
./PointFinder.py -h
```
<pre>
usage: PointFinder.py [-h] -i INPUTFILES [INPUTFILES ...] -o OUT_PATH -s
                      SPECIES -p DB_PATH -m {kma,blastn} -m_p METHOD_PATH [-n]
                      [-t THRESHOLD] [-l MIN_COV] [-u]
                      [-g SPECIFIC_GENES [SPECIFIC_GENES ...]]
                      [-r {early,all,specified}]
</pre>
### Detecting point mutations
```
mkdir results
```
```
./PointFinder.py -i /home/amrlenovo2/Documents/ACORN/saureus/A1-11_S11_L001.fasta -o S_aureus -p pointfinder_db/ -s staphylococcus_aureus -m blastn -m_p /usr/bin/blastn
```
### Interpreting PointFinder output
<pre>
Mutation        Nucleotide change	Amino acid change	Resistance		PMID
gyrA p.S84L	TCA -> TTA		S -> L			Ciprofloxacin   	2174869
gyrA p.S84L	TCT -> CCT		S -> P			Ciprofloxacin   	2174869
</pre>				
 
### References
1.	Ea Zankari, Rosa Allesøe, Katrine G Joensen, Lina M Cavaco, Ole Lund, Frank M Aarestrup, PointFinder: a novel web tool for WGS-based detection of antimicrobial resistance associated with chromosomal point mutations in bacterial pathogens, Journal of Antimicrobial Chemotherapy, Volume 72, Issue 10, October 2017, Pages 2764–2768, https://doi.org/10.1093/jac/dkx217
2.	Seemann T, Abricate, Github https://github.com/tseemann/abricate
3.	National Research Council (US) Committee to Study the Human Health Effects of Subtherapeutic Antibiotic Use in Animal Feeds. The Effects on Human Health of Subtherapeutic Use of Antimicrobials in Animal Feeds. Washington (DC): National Academies Press (US); 1980. Appendix C, Genetics of Antimicrobial Resistance. Available from: https://www.ncbi.nlm.nih.gov/books/NBK216503/


