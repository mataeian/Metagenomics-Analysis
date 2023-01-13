# Sequence quality assesment

Used FastQC to evaluate my sequence quality. This tool exports a html summary report for each file.

```
#make a folder in my directory called fastqc_output
mkdir fastqc_output

#checked program usage message using this command
fastqc -h

#FastQC program can accept both compressed or uncompressed sequence file.

#command to execute the program.
fastqc -t  2 -f Sample.R1.fastq.gz Sample_R2.fastq.gz -o FastQC_output/

```
It produced a html format sequence evaluation report. transfered the .html files to my computer and opened for analysis. I could also use this command to open them in firefox:
```
firefox Sample_R1.fastqc.html
```
