# Assembly

## Short read 

`spades.py --isolate -1 /data/acorn_training_course_2024/RawRead/kleb/A6-1_S1_L001_R1_001.fastq.gz -2 /data/acorn_training_course_2024/RawRead/kleb/A6-1_S1_L001_R2_001.fastq.gz -o spades_output -t 8 -k 55,71 --cov-cutoff 20`


## Long read 

Nanoq
`nanoq --min-len 1000 --input /data/acorn_training_course_2024/RawRead/kleb/SQK-NBD114-24_barcode01.fastq --min-qual 0 > read.filt.fastq`

Flye
`flye --nanohq read.filt.fastq -g 5000000 -i 0 --threads 8 -o flye_output`

Minimap2
`minimap2 -t 7 0x map-ont flye_output/assembly.fasta read.filt.fastq > alignment.paf`

Racon 
`racon -t 8 /data/acorn_training_course_2024/RawRead/kleb/SQK-NBD114-24_barcode01.fastq alignment.paf flye_output/assembly.fasta > final_assembly.fasta`


### Hybrid 

`unicycler -1 /data/acorn_training_course_2024/RawRead/kleb/A6-1_S1_L001_R1_001.fastq.gz -2 /data/acorn_training_course_2024/RawRead/kleb/A6-1_S1_L001_R2_001.fastq.gz -l  /data/acorn_training_course_2024/RawRead/kleb/SQK-NBD114-24_barcode01.fastq -o unicyler_output`