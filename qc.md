# Short reads quality control

The QC procedure was used to clip off the technical sequences (primer, adapter) and filter out artifacts and low-quality reads, short reads, and contamination reads. This could significantly affect downstream analysis.

A number of tools are publicly available for quality control such as Bbduk.sh, sickle, and cutadapt. 
I used an in house pipeline in EBG, which integrated multiple tools, in order to get the good quality control results.

```
#created qc directory and moved into the directory
mkdir qc
perl fastq_qc.pl -1 test.R1.fastq.gz -2 test.R2.fastq.gz -s test -minlen 50 -l TruSeq -t 8
```

After finishing QC, you need to verify the improved quality of the quality controlled reads again with FastQC program
```
fastqc -t 2 -f fastq test.qc.1.fastq test.qc.2.fastq
```
