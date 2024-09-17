# Assembly 

*De novo* assembly is a very important step and the first for downstream genomical analysis, especially for bacterial genome. 

==With different types of raw reads, we need a different assembler==. 

**!Note**: you have to replace the {input} bracket with your input to be able to run the command.

## Short read assembly 

Short reads are the output from **ILLUMINA**, **MGI**, etc. with a fixed read length (around ~100-300bp) with a very large amount of reads (can up to millions). Thus, the algorithms of short reads assembly requires a lot of computational resources. Additionally, different K-mer can result different set of of contigs.  

We will use SPAdes as the short read assembler 
Firstly, we use `--isolate` mode to indicate our input is from bacteiral isolate with pair-end reads (`-1`, `-2`). 

We assembly the short read using this command: 

`spades.py --isolate -1 {input.read1} -2 {input.read2} -o {output}`

We can try with other flags (**Optional if you have time**)

`spades.py --isolate -1 {input.read1} -2 {input.read2} -o {output} -t 8 -k 55,71,91,111 --cov-cutoff 20`

- `-t 8`: use 4 threads/cores to run assembly. You can change the number to increase or decrease number of threads which suits your commputer
- `-k 55,71,91,111` set of k-mer used for assembly. To reduce the time of assembly, you can reduce the number of number. K-mer must not higher the read length. 
- `--cov-cutoff 20` minimum cut-off of deep of coverage. 


After that, check the assembly stat by using `quast` and `Bandage`. Which K-mer assembly you choose as the final assembly ?

You can install quast using `conda` and it should  be installed into training env. 

`conda install -c bioconda quast`

then use `quast`

`quast {input.assembly}`

`Bandage` can be installed from the [website](https://rrwick.github.io/Bandage/)

## Long read assembly

The amount of reads of long read output is lower, comparing to short-read. In addition, the size of reads is vary, with not fixed length. Subsequently, long read assembly required more steps than short read assembly. 

Firstly, we install nanoq to filter our raw reads by lenght. 

`conda install -c bioconda nanoq`

We discard the reads that have length lower than 1000bp
`nanoq --min-len 1000 --input {input.rawread} --min-qual 0 > reads.filt.fastq`

Using `flye` for assembly

`flye --nano-hq  {input.read} -g 5000000 -i 0 --threads 8 -o flye`

- `--nano-hq`: indicate out input is nanopore with high quality.  
- `-g`: estimated genome size 

Using minimap2 to create a reference seq for `racon` polishing

`minimap2 -t 7 -x map-ont {input.flye_output} {input.read} > alignments.paf`

Using racon for polishing

`racon -t 8 {input.read} {input.aligments} {input.flye} > final_assembly.fasta`

Again, we can check assembly stat by using `quast` and `bandage`

## Hybrid assembly 

Beware, **Advanced bioinformatic** ahead. 

With this part, you can feel yourself as a true bioinformatician by installing program via github.

We use `Unicycler` for the hybrid assembly. 
For more information, please visit [this website](https://github.com/rrwick/Unicycler)

```
git clone https://github.com/rrwick/Unicycler.git
cd Unicycler
python3 setup.py install
```

With both short and long reads from a same sample, we can conduct hibrid assembly using `Unicycler` 

`unicycler -1 {input.read1} -2 {input.read2} -l {input.read} -o {output}`
