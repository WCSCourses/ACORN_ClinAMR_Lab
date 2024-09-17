## Computational Practical 6: Detecting resistance using command line II
In this practical we will focus on detecting chromosomal point mutations associated with antimicrobial resistance. Chromosomal mutations conferring resistance to antibiotics occur as spontaneous, random, and relatively rare alterations in the DNA composition of bacteria. 

### Mechanisms of Mutational Resistance to Antibiotics.

* the target site is altered so that binding of the antibiotic is reduced or eliminated,
* there is a block in the transport of the drug into the cell,
* the antibiotic is detoxified or inactivated, or
* the inhibited step in a metabolic pathway is bypassed.
  
### Point Finder
PointFinder is a tool designed for detecting antimicrobial resistance in bacterial pathogens through Whole Genome Sequencing (WGS) by identifying chromosomal point mutations. It consists of two databases: one containing chromosomal gene sequences in fasta format, and the other with information on codon positions and mutations. PointFinder utilizes BLASTn to find the best match for each gene in the chromosomal gene database, analyzing only those hits with ≥80% identity. The tool examines each alignment by comparing query positions with corresponding positions in the database sequence. Any mismatches are saved and compared against the chromosomal mutation database. Users can choose to view all mismatches or only those that are known and found at specific positions in the chromosomal database.

### Step 1: Installation
```
mkdir -p ~/ACORN_course/cp6
cd ~/ACORN_course/cp6
git clone https://git@bitbucket.org/genomicepidemiology/pointfinder.git
cd pointfinder
git clone https://git@bitbucket.org/genomicepidemiology/pointfinder_db.git
pip3 install cgecore
```
#### Help page

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
**Step 2: Create results directory**
```
results_dir=~/ACORN_course/cp6/mutations
mkdir -p $results_dir
```
**Detect mutations**
```
./PointFinder.py -i ~/ACORN_course/cp6/data/saureus/A1-1_S1_L001.fasta -o $results_dir -p pointfinder_db/ -s staphylococcus_aureus -m blastn -m_p $(which blastn)
```
**View the results file**
```
cd $results_dir
ls
less -S A1-1_blastn_results.tsv
```
### Interpreting PointFinder output
<pre>
Mutation        Nucleotide change	Amino acid change	Resistance		PMID
gyrA p.S84L	TCA -> TTA		S -> L			Ciprofloxacin   	2174869
gyrA p.S84L	TCT -> CCT		S -> P			Ciprofloxacin   	2174869
</pre>				
### Exercises
Find and list all the mutations in Klebsiella A6-5_S5_L001.fasta
### References
1.	Ea Zankari, Rosa Allesøe, Katrine G Joensen, Lina M Cavaco, Ole Lund, Frank M Aarestrup, PointFinder: a novel web tool for WGS-based detection of antimicrobial resistance associated with chromosomal point mutations in bacterial pathogens, Journal of Antimicrobial Chemotherapy, Volume 72, Issue 10, October 2017, Pages 2764–2768, https://doi.org/10.1093/jac/dkx217
2.	Seemann T, Abricate, Github https://github.com/tseemann/abricate
3.	National Research Council (US) Committee to Study the Human Health Effects of Subtherapeutic Antibiotic Use in Animal Feeds. The Effects on Human Health of Subtherapeutic Use of Antimicrobials in Animal Feeds. Washington (DC): National Academies Press (US); 1980. Appendix C, Genetics of Antimicrobial Resistance. Available from: https://www.ncbi.nlm.nih.gov/books/NBK216503/
