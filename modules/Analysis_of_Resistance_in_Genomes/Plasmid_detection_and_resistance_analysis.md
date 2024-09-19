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




