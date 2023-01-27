# Metagenome assembly
This step reconstructs genomes using the quality controlled sequences. 

You can merge your reads before assembly to improve the assembly and speed up the assembly process.
Used bbmerge.sh to merge the paired end reads

```
bbmerge.sh in=test.qc.1.fastq in2=test.qc.2.fastq out=test.qc.merged.fastq outu2=test.qc.unmerged.2.fastq outu=test.qc.unmerged.1.fastq
```



Create assembly directory
#make an "assembly" directory in your project directory  
>mkdir assembly  
#cd into assembly directory  
>cd assembly/  
Assembly of qc reads with Megahit
#print megahit version  
>megahit -v
#print megahit usage message   
>megahit -h  
#do the assembly using quality controlled reads  
>megahit -1 your_qc_R1_file -2 your_qc_R2_file -t 8 -m 0.5 -o megahit_assembly  >& megahit.log.txt 
After finishing the assembly, we first check the "megahit.log.txt" file to make sure no errors has occurred during assembly and also to look at some of the simple stats such as min length of the contigs, max length of the contigs, N50

#check log file to make sure there are no errors occurred during the assembly
>more megahit.log.txt
#check the simple stats for the assembly
>tail megahit.log.txt
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



Step 4: Assembling reads
A variety of assembly programs are publicly available, including IDBA, SOAPDenovo2, megahit, and metaspades. Those assemblers are also available on our server: 
· Idba: 
	o Path on the server: /export/data/programs/idba
	o Document: http://github.com/loneknightpy/idba
· Megahit: 
	o Path on the server: /export/data/programs/megahit
	o Document: http://github.com/voutcn/megahit
· SPAdes: 
	o Path on the server: /export/data/programs/SPAdes
	o Document: http://cab.spbu.ru/software/spades
		 
· SOAPdenovo2: 
	o Path on the server: /export/data/programs/SOAP/SOAPdenovo2-bin-LINUX-generic-r240
	o Document: https://github.com/aquaskyline/SOAPdenovo2
Example commands: 
· Metaspades path on the server: /export/data/programs/SPAdes/
· Get usage message: metaspades.py -h
· Example command: metaspades.py -1 test.qc.unmerged.1.fastq -2 test.qc.unmerged.2.fastq -s test.qc.merged.fastq -o test_metaspades -t 8
· megahit path on the server: /export/data/programs/megahit/
· Print out megahit usage message:  megahit -h
· Megahit -1 test.qc.1.fastq -2 test.qc.2.fastq -o megahit_out -t 8 --min-contig-len 500 
After assembling the quality controlled reads into contigs, you can use metaQuast to evaluates and compares metagenome assembles. MetaQuast is available on the server at: /export/data/programs/quast. Please following the manual at: http://quast.sourceforge.net/metaquast.html on how to use metaQuast and to interpret the results. 