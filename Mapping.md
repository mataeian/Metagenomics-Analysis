# Read Mapping

During Mapping you align short reads back to references genomes, genes, or contigs. The results is used to analyse contig coverages, quantify genes, infer taxonomic diversities, functional and pathway profiles, and improve binning results. 

There are multiple tools to do the mapping with. I used bbmap.

```
bbmap.sh ref=contig.500.fasta in=test.qc.1.fastq in2=test.qc.2.fastq out=Mapping/test.bbmap.sam covstats=Mapping/test.bbmap_covstats.txt scafstats=Mapping/test.bbmap_scafstats.txt
```
