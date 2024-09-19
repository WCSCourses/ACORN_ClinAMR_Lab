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

#### 8. Now we can perform AMR prediction on the identified contigs e.g. using abricate - following the steps that were introduced in a previous session.

```

```


### Plasmid detection using online tools

Navigate to the [plasmidfinder 2](https://cge.food.dtu.dk/services/PlasmidFinder/) webpage and upload the same isolate you have analyzed above.

You might have to provide your email address so you get notified when the analysis is completed.




