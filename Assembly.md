# Metagenome assembly
This step reconstructs genomes using the quality controlled sequences. 

You can merge your reads before assembly to improve the assembly and speed up the assembly process.
Used bbmerge.sh to merge the paired end reads

```
bbmerge.sh in=test.qc.1.fastq in2=test.qc.2.fastq out=test.qc.merged.fastq outu2=test.qc.unmerged.2.fastq outu=test.qc.unmerged.1.fastq
```
After merging for each sample, combined all 4 of my samples for coassembly.

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
After checking the assembly and making sure everything looks good. Filter out the short contigs.

```
perl get_sub_length.pl final.contigs.fa 500 > contig.500.fasta
or
perl filterContigByLength.pl final.contigs.fa 500 > contig.500.fasta
```



Added a sample id to every contigs in the contigs.500.fasta file in place
```
perl -pi -le 's/>/>yourShortSampleID_/g' contigs.500.fasta
```



