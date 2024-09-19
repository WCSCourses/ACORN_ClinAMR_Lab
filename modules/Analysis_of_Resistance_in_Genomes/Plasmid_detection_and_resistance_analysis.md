# Introduction
As has been covered in previous lectures/tutorials, plasmids can play a key role in the spread of antibiotic resistance. Plasmids are circular or linear double-stranded DNA molecules capable of autonomous replication and conjugation. So far, we have only covered prediction of AMR from presumably bacterial WGS assembly data. In this tutorial, we want to introduce methods/tools for detection of plasmids. However, we should note that plasmid detection using short read data can be diffcult and complex.


We will analyze K. pneumoniae genomes for plasmid detection using a command-line tool called `mlplasmids`. We will also perform plasmid prediction using an online tool called `plasmidfinder` on the same dataset and compare the results.

### Command-line approach

### First set-up the conda env

#### 1. Create a directory to store the github repositories
```
mkdir -p ~/github
cd ~/github
```

#### 2. Clone the repository
```
git clone https://gitlab.com/mmb-umcu/mlplasmids.git

cd mlplasmids
```

#### 3. Create a conda env for mlplasmid
```
conda deactivate
conda env create -f envs/mlplasmids.yml --solver=libmamba
```

#### 4. Activate the environment
```
conda activate mlplasmids
```

#### 5. Create an output directory
```
outdir=~/ACORN_course/cp11/kpn_plasmids
mkdir -p $outdir
```
#### 6. Run analysis on K. pneumoniea assemblies

```
Rscript scripts/run_mlplasmids.R input_genome.fasta $outdir/input_genome.tab 0.7 'Klebsiella pneumoniae'
```

#### 7. Count the number of putative plasmid contigs
```
grep -c $outdir/input_genome.tab
```
> How many plasmids were detected?


### Plasmid detection using online tools

Navigate to the [plasmidfinder 2](https://cge.food.dtu.dk/services/PlasmidFinder/) webpage and upload the same isolate you have analyzed above.

You might have to provide your email address so you get notified when the analysis is completed.


Before you upload your sequence data, you have to choose the correct database and the correct data type. Once you have selected your file click upload to submit your job to the queue.


#### Exercise 1:
1. How many plasmids were detected?



#### Exercise 2: 
Now we can perform AMR prediction on the identified contigs e.g. using abricate - following the steps that were introduced in a previous session.

##### Step 1: Create a new conda env for a tool called seqtk
We want to use this tool to only select only the plasmid contigs and save them in a new fasta file. This will be our input for predicting plasmid related AMR genes (if any).
```
conda create -n seqtk seqtk -c bioconda --solver=libmamba

```

##### Step 2: Get names of the contigs detected as plasmids

```
# Get contig names
cut -f4 $outdir/input_genome.tab | sed 's/\"//g' | grep -v Contig_name > $outdir/plasmid_contig_names.txt
```

##### Step 3: Get sequences for contigs with plasmids detected
```
# replace input_genome.fasta with actual path to the genome assembly used in step 6
seqtk subseq -l 60 input_genome.fasta $outdir/plasmid_contig_names.txt > $outdir/input_genome.plasmid_contig.fasta

```

##### Step 4: Now run abricate to identify plasmid-related AMR genes if any

```
conda activate training

abricate -db resfinder $outdir/input_genome.plasmid_contig.fasta > $outdir/plasmid_resfinder.tab


```





