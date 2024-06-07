#!/bin/sh

# number of CPU used

THREADS=12

# Input & Ouput path
REF='/media/alex/Prometheus/Smusiva_v14_pmchr.fasta'
PATH_FASTQ='/media/alex/Prometheus/sphaerulina_fastq/'
OUTPUT='/media/alex/Prometheus/'


OUTPUT_MAPPING="$OUTPUT/Mapping/"
OUTPUT_NOMAPPING="$OUTPUT/NOMapping/"
mkdir $OUTPUT
mkdir $OUTPUT_MAPPING
mkdir $OUTPUT_NOMAPPING
## Indexing the reference genome
bwa index -a bwtsw /media/alex/Prometheus/Smusiva_v14_pmchr.fasta

for fastq in ${PATH_FASTQ}*R1.fastq.gz; do 


  ID=`basename -s _R1.fastq.gz $fastq`

  bwa mem -t ${THREADS} $REF ${PATH_FASTQ}${ID}_R1.fastq.gz ${PATH_FASTQ}${ID}_R2.fastq.gz -R "@RG\tID:${ID}\tSM:${ID}\tPL:ILLUMINA" | samtools view -F 0x04 -q 30 -bh -U ${OUTPUT_NOMAPPING}${ID}below_q30.bam | samtools sort -o ${OUTPUT_MAPPING}${ID}.filt_sort.bam

done
