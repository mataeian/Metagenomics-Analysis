# Metagenome assembly
This step reconstructs genomes using the quality controlled sequences. 

You can merge your reads before assembly to improve the assembly and speed up the assembly process.
Used bbmerge.sh to merge the paired end reads

```
bbmerge.sh in=test.qc.1.fastq in2=test.qc.2.fastq out=test.qc.merged.fastq outu2=test.qc.unmerged.2.fastq outu=test.qc.unmerged.1.fastq
```
After merging for each sample, combined all 4 of my samples:

```
cat test1.qc.merged.fastq test2.qc.merged.fastq test3.qc.merged.fastq test4.qc.merged.fastq > merged_Total.fastq

cat test1.qc.unmerged.1.fastq test2.qc.unmerged.1.fastq test3.qc.unmerged.1.fastq test4.qc.unmerged.1.fastq > unmerged_1_Total.fastq

cat test1.qc.unmerged.2.fastq test2.qc.unmerged.2.fastq test3.qc.unmerged.2.fastq test4.qc.unmerged.2.fastq > unmerged_2_Total.fastq
```

First used metaspades for assembly. 
metaSPAdes can provide longer contigs, but is much slower
```
metaspades.py -1 unmerged_1_Total.fastq -2 unmerged_2_Total.fastq -s merged_Total.fastq -o MetaSpades/
```
It crashed. tried to continue:
```
metaspades.py --continue
```

Keeps crashing. Did the assembly for each sample seperately and it is fine. So pobably thefile size is why it keeps crashing for the combined file.


Used Megahit for assembly:
Megahit is a fast assembler but metaspades gives larger contigs.

```
megahit -1 unmerged_1_Total.fastq -2 unmerged_2_Total.fastq -r merged_Total.fastq -t 12 -o Megahit_output --keep-tmp-files --presets meta-large
```
Checked auality of assembly using metaquast

```
metaquast.py final.contigs.fa -o MetaQuast/ --gene-finding
```

Also used stats.sh to check my assembly

```
stats.sh final.contigs.fa
```



After the successful assembly, your contig file is "megahit_assembly/final.contigs.fa".

Assembly of qc reads with metaSPAdes
While Megahit is a fast assembler, metaSPAdes can provide longer contigs, but is much slower. Regardless of assembly method, the produced contig file (.fasta or .fa) can be used in the rest of the metagenomics pipeline. Here is a metaSPAdes assembly example:

#assume you are in "assembly" directory  
#print metaspades version  
>metaspades.py -h  
#print metaspades usage message  
>metaspades.py -h  
#do the assembly using quality controlled reads  
>metaspades.py -1 your_qc_R1_file -2 your_qc_R2_file -t 8 -m 100 -o metaspades_assembly  >& metaspades.log.txt
After finishing the assembly, we first check the "metaspades.log.txt" file to make sure no errors occurred during assembly

#check log file to make sure there are no errors occurred during the assembly
>more metaspades.log.txt  
After the successful assembly, your contig file is "metaspades_assembly/contigs.fasta". To know more about metaSPAdes output, please check its website.

Co-assembly
The process of assembling the quality controlled sequences from multiple samples togehter is called co-assembly

#assume you are in "assembly" directory
#megahit coassembly using quality controlled reads  
>megahit -1 your_qc_R1_file1,your_qc_R1_file2,your_qc_R1_file3 -2 your_qc_R2_file,your_qc_R2_file2,your_qc_R3_file3 -t 8 -m 0.5 -o megahit_coassembly >& megahit_coassembly.log.txt
#do the assembly using quality controlled reads  
>metaspades.py --pe1-1 your_qc_R1_file --pe2-1 your_qc_R1_file2 --pe3-1 your_qc_R1_file3  --pe1-2 your_qc_R1_file --pe2-2 your_qc_R1_file2 --pe3-2 your_qc_R1_file3  -t 8 -m 100 -o metaspades_coassembly  >& metaspades_coassembly.log.txt
Assembly stats
While MEGAHIT generates some simple stats for your assembles, metaSPAdes are not generating stasts at the end of the assembling. You can use the "seqStats.pl" from the bin directory to generate the basic stats.

#assume you are in the "assembly" directory and downloaded the "seqStats.pl" file into the directory
#generate the stats for metaSPAdes assembly
>perl seqStats.pl -f fasta -s metaspades_assembly/contigs.fasta > metaspades_assembly/contigs.stats.txt
#check the produced stats for the assembly  
>more metaspades_assembly/contigs.stats.txt
Filter out short contigs
For all the downstream analysis, the contigs shorter than 500 bp will be excluded. You can use "filterContigByLength.pl" script from the bin directory to fitler out the contigs shorter than 500 bp:

#assume you are in the "assembly" directory and downloaded "filterContigByLength.pl" to the directory
#filter out contigs shorter than 500 bp from metaSPAdes assembly
>perl filterContigByLength.pl metaspades_assembly/contigs.fasta > metaspades_assembly/contigs.500.fasta
#filter out contigs shorter than 500 bp from MEGAHIT assembly
>perl filterContigByLength.pl megahit_assembly/final.contigs.fa > megahit_assembly/contigs.500.fasta
Add prefix for every contig ids in a fasta file
For a lot of times, we are analyzing multiple samples together. To make sure all the contigs from different assemblies are having unique ids when we merge them into one fasta file, we can added a prefix for each contig in a fasta format assembly file

#add a sample id to every contigs in the contigs.500.fasta file in place
perl -pi -le 's/>/>yourShortSampleID_/g' contigs.500.fasta




