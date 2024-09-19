# Phylogenetic analysis Module

**!Note**: you have to replace the {input} bracket with your input to be able to run the command. 

## Core Genome 
We will use `snippy` again for this practical, before that we have to install `snippy` 

```
conda create -n snp -c bioconda -c conda-forge snippy=4.6.0 bcftools=1.15.1 snpeff=4.3 samtools=1.19
conda activate snp
```

Firstly, we create a input `tab` file, which contains the sample ID and its path to the contig sequence file, to supply to snippy. 
The `tab` file should follow the format below 

```
# input.tab = ID R1 [R2]
Isolate1	/path/to/R1.fq.gz	/path/to/R2.fq.gz
Isolate1b	/path/to/R1.fastq.gz	/path/to/R2.fastq.gz
Isolate1c	/path/to/R1.fa		/path/to/R2.fa
# single end reads supported too
Isolate2	/path/to/SE.fq.gz
Isolate2b	/path/to/iontorrent.fastq
# or already assembled contigs if you don't have reads
Isolate3	/path/to/contigs.fa
Isolate3b	/path/to/reference.fna.gz
```  
So, many people may want to ask, how to create an `input.tab` or `sample_list` anyway ?

First, we have to retrive the sample list by using `ls` and write it into a tab file

`ls {path to your folder} > sample_list.tab`
for example:
`ls /data/acorn_training_2024/kleb/kleb/*.fa > sample_list.tab`

Now download the file then edit it in excel or notepad.  

After we have the input sample list file, we can use that to generate a bash script

`snippy-multi {input.sample} --ref {input.ref} --cpus 16 > snippy_core.sh`
Then one would run this to generate the output script.
The first parameter should be the `input.tab` file.
The remaining parameters should be any remaining shared `snippy` parameters. The `ID` will be used for each isolate's `--outdir`.

We can check the bash script 
`less snippy_core.sh`
Run the script
`bash ./snippy_core.sh`
After `snippy` done, you can find core genome alignment file with the extension of `.aln` 

More detail of the output:

Extension | Description
----------|--------------
.aln | A core SNP alignment in the `--aformat` format (default FASTA)
.full.aln | A whole genome SNP alignment (includes invariant sites)
.tab | Tab-separated columnar list of **core** SNP sites with alleles but NO annotations
.vcf | Multi-sample VCF file with genotype `GT` tags for all discovered alleles
.txt | Tab-separated columnar list of alignment/core-size statistics
.ref.fa | FASTA version/copy of the `--ref`

For more information, you can follow [the github link](https://github.com/tseemann/snippy)

## Phylogeny tree construction 

### Distance-based method: Neighbor Joining


Neighbor-Joining is a quick method to give an initial view of a large set of sequences. We will use `rapidnj` to create neighbor-joining phylogenetic tree.

We have to install `rapidnj` first.
`conda create -n rapidnj -c bioconda -c conda-forge rapidnj`

Then we activate the conda env

`conda activate rapidnj`

The command to run `rapidnj` is fairly simple. 

`rapidnj  {input.alignment} -i fa > nj.nwk` 

We can use this `newick` file to visualise the phylogenetic tree. 
For more information about `rapid`, you can follow their [website](https://wheelerlab.org/software/ninja/docs.html).

### Character-based method: Maximum likelihood tree 

Maximum likehood tree is the most popular phylogeny method due to its statistical robustness model selection and accuracy. 
For ML tree, we use `iqtree`.

`iqtree -s {input.alignment} -nt AUTO -m {input.model}`

The use of `iqtree` is a bit more complicated where you have to supply the `subsitution model` 

Go to [iqtree](http://www.iqtree.org/doc/Substitution-Models#dna-models) and choose your model. 

The most common model are `GTR` and you can try it. 

You can add flag `-bb INT` to add bootstrap option. 




