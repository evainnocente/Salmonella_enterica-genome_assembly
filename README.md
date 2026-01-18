# _Salmonella enterica_ genome assembly
### BINF_6110 Assignment 1 Part 1 by Eva Burguete-Innocente
### January 18th 2026

## Introduction

Genome assembly is the process of using sequencing reads to construct the genomic sequence of an organism (Wick & Holt, 2019). Accurate and complete genome assemblies are important for several reasons, such as understanding bacterial population genetics, identifying genetic variants, and clarifying bacterial evolution and antimicrobial resistance (Breckell & Silander, 2021; Kumar et al., 2025; Taylor et al., 2019; Wang et al., 2025;). This is particularly relevant for Salmonella enterica, as it can cause severe illness, so accurate detection and species identification is crucial (Luan et al., 2024; Taylor et al., 2019). Nanopore sequencing is faster and more portable than short-read sequencing, but it has historically had a higher error rate, although this has recently improved (Zhang et al., 2023). Additionally, the assembled bacterial genome and its plasmids may not be fully closed (i.e. completely circular) which could result in missing information (Taylor et al., 2019).  Many different assembly methods can be used with long-read sequencing data. Some tools include Canu (Koren et al., 2017), Flye (Kolmogorov et al., 2019), Raven (Vaser & Šikić, 2021), Redbean (Ruan & Li, 2020), Shasta (Shafin et al., 2020), and many more.
    
After a genome is assembled, it must be aligned to a reference genome to confirm sequence accuracy (Breckell & Silander, 2021). However, reference genomes do not always exist for the study species, increasing the challenges of genome assembly (Taylor et al., 2019). 	
    
Wick and Holt (2019), Breckell and Silander (2021), and Kumar et al. (2025) compared genome assembly methods and quantified performance benchmarks. Briefly, Flye is a popular, accurate, and fast assembler (Breckell & Silander, 2021; Kumar et al., 2025; Wick & Holt, 2019). Canu is reliable but has a long computational runtime, and is also no longer under development (Koren et al., 2017; Kumar et al., 2025; Wick & Holt, 2019). Other tools like Shasta were fast, yet not as reliable (Wick & Holt, 2019). No one tool performed best across all benchmarks, therefore different tools may be preferable for different analysis goals (Breckell & Silander, 2021).
    
Wick and Holt (2019) found that Raven v0.0.5 performed very well in benchmarks like reliability and robustness. Raven is an assembler that uses an overlap-layout-consensus assembler algorithm (Vaser & Šikić, 2021). However, it struggled with circularisation (excluding some bases) and assembling small plasmids (Wick & Holt, 2019). Breckell and Silander (2021) found that Raven assembled the least number of contigs, indicating its accuracy at predicting structure. Kumar et al. (2025) found that Raven v1.8.1 was user-friendly, accurate, and fast. Given its accuracy, reliability, and ease of use, I will use Raven to assemble the Salmonella enterica genome. 


## Proposed Methods

### Obtaining raw data and checking read quality

I will obtain the raw reads in .fastq format from NCBI Sequence Read Archive (accession: SRR32410565; Bogaerts et al., 2025). I will also obtain the _S. enterica_ reference genome from NCBI Genome (accession: GCF_000006945.2) for alignment. Then, I will use SeqKit v2.10.1 to assess the quality of the raw reads, using the “-a” (all) parameter to display all metrics (Shen et al., 2016). 

### Genome assembly and alignment

I will assemble the S. enterica genome using Raven v1.8.3 with default parameters and 4 threads to speed up computational time (Vaser & Šikić, 2021). Then, I will align the assembled genome to the reference genome using minimap2 v2.30, specifying “-a” to output the file in .sam format and “-x map-ont” to map reads to the _S. enterica_ reference genome (accession: GCF_000006945.2) (Li, 2016). The genome will be sorted, indexed, and converted to .bam format with samtools v1.21 (Li et al., 2009). 

### Variant calling

I will call variants using bcftools mpileup and bcftools call v1.23, as bcftools has been shown to perform well overall and with long reads in particular (Danecek et al., 2021; Schiffer et al., 2025). I will use the following parameters, as per Schiffer et al. (2025): “-x” to ignore read overlaps, “-I” to skip indels, “-Q 13” for the minimum quality score for a base to be included, “-h 100” for homopolymer errors, “-M 10000” for the maximum read length, “-a” for the annotations included in the output .vcf file, “-m” for multiallelic caller, “—ploidy 1” to specify the ploidy of the organism, “-V indels” to skip indels, and “-Oz” to compress the output file. I will visualise the resulting variants in Integrative Genomics Viewer v2.16.0 (Robinson et al., 2011). All command-line tools will be installed and analyses run on a virtual machine running Ubuntu 25.10 with 7 cores and 12 GB of RAM.

## References

Bogaerts, B., Maex, M., Commans, F., Goeders, N., Van Den Bossche, A., De Keersmaecker, S. C. J., Roosens, N. H. C., Ceyssens, P.-J., Mattheus, W., & Vanneste, K. (2025). Oxford Nanopore Technologies R10 sequencing enables accurate cgMLST-based bacterial outbreak investigation of Neisseria meningitidis and Salmonella enterica when accounting for methylation-related errors. Journal of Clinical Microbiology, 63(10), e00410-25. https://doi.org/10.1128/jcm.00410-25

Breckell, G. L., & Silander, O. K. (2021). Do you want to build a genome? Benchmarking hybrid bacterial genome assembly methods. https://doi.org/10.1101/2021.11.07.467652

Danecek, P., Bonfield, J. K., Liddle, J., Marshall, J., Ohan, V., Pollard, M. O., Whitwham, A., Keane, T., McCarthy, S. A., Davies, R. M., & Li, H. (2021). Twelve years of samtools and bcftools. GigaScience, 10(2), giab008. https://doi.org/10.1093/gigascience/giab008

Kolmogorov, M., Yuan, J., Lin, Y., & Pevzner, P. A. (2019). Assembly of long, error-prone reads using repeat graphs. Nature Biotechnology, 37(5), 540–546. https://doi.org/10.1038/s41587-019-0072-8

Koren, S., Walenz, B. P., Berlin, K., Miller, J. R., Bergman, N. H., & Phillippy, A. M. (2017). Canu: Scalable and accurate long-read assembly via adaptive k -mer weighting and repeat separation. Genome Research, 27(5), 722–736. https://doi.org/10.1101/gr.215087.116

Kumar, M. S., Krishna, M. B., Soman, K. P., Stanley, J., Pourmand, N., Suravajhala, P., & Babu, T. G. S. (2025). Benchmarking long-read assembly tools and preprocessing strategies for bacterial genomes: A case study on E. coli DH5α. Biotechnology Reports, 48, e00931. https://doi.org/10.1016/j.btre.2025.e00931

Li, H. (2016). Minimap and miniasm: Fast mapping and de novo assembly for noisy long sequences. Bioinformatics, 32(14), 2103–2110. https://doi.org/10.1093/bioinformatics/btw152

Li, H., Handsaker, B., Wysoker, A., Fennell, T., Ruan, J., Homer, N., Marth, G., Abecasis, G., & Durbin, R. (2009). The sequence alignment/map format and samtools. Bioinformatics, 25(16), 2078–2079. https://doi.org/10.1093/bioinformatics/btp352

Luan, T., Commichaux, S., Hoffmann, M., Jayeola, V., Jang, J. H., Pop, M., Rand, H., & Luo, Y. (2024). Benchmarking short and long read polishing tools for nanopore assemblies: Achieving near-perfect genomes for outbreak isolates. BMC Genomics, 25(1), 679. https://doi.org/10.1186/s12864-024-10582-x

Martin, M. (2011). Cutadapt removes adapter sequences from high-throughput sequencing reads. EMBnet.Journal, 17(1), 10–12. https://doi.org/10.14806/ej.17.1.200

Robinson, J. T., Thorvaldsdóttir, H., Winckler, W., Guttman, M., Lander, E. S., Getz, G., & Mesirov, J. P. (2011). Integrative genomics viewer. Nature Biotechnology, 29(1), 24–26. https://doi.org/10.1038/nbt.1754

Ruan, J., & Li, H. (2020). Fast and accurate long-read assembly with wtdbg2. Nature Methods, 17(2), 155–158. https://doi.org/10.1038/s41592-019-0669-3

Schiffer, A. M., Rahman, A., Sutton, W., Putnam, M. L., & Weisberg, A. J. (2025). A comparison of short- and long-read whole-genome sequencing for microbial pathogen epidemiology. mSystems, 10(12), e01426-25. https://doi.org/10.1128/msystems.01426-25

Shafin, K., Pesout, T., Lorig-Roach, R., Haukness, M., Olsen, H. E., Bosworth, C., Armstrong, J., Tigyi, K., Maurer, N., Koren, S., Sedlazeck, F. J., Marschall, T., Mayes, S., Costa, V., Zook, J. M., Liu, K. J., Kilburn, D., Sorensen, M., Munson, K. M., … Paten, B. (2020). Nanopore sequencing and the Shasta toolkit enable efficient de novo assembly of eleven human genomes. Nature Biotechnology, 38(9), 1044–1053. https://doi.org/10.1038/s41587-020-0503-6

Shen, W., Le, S., Li, Y., & Hu, F. (2016). Seqkit: A cross-platform and ultrafast toolkit for fasta/q file manipulation. PloS One, 11(10), e0163962. https://doi.org/10.1371/journal.pone.0163962

Taylor, T. L., Volkening, J. D., DeJesus, E., Simmons, M., Dimitrov, K. M., Tillman, G. E., Suarez, D. L., & Afonso, C. L. (2019). Rapid, multiplexed, whole genome and plasmid sequencing of foodborne pathogens using long-read nanopore technology. Scientific Reports, 9(1), 16350. https://doi.org/10.1038/s41598-019-52424-x

Vaser, R., & Šikić, M. (2021). Time- and memory-efficient genome assembly with Raven. Nature Computational Science, 1(5), 332–336. https://doi.org/10.1038/s43588-021-00073-4

Wang, X., Koster, A. D., Koenders, B. B., Jonker, M., Brul, S., & Ter Kuile, B. H. (2025). De novo acquisition of antibiotic resistance in six species of bacteria. Microbiology Spectrum, 13(3), e01785-24. https://doi.org/10.1128/spectrum.01785-24

Wick, R. R., & Holt, K. E. (2019). Benchmarking of long-read assemblers for prokaryote whole genome sequencing (No. 8:2138). F1000Research. https://doi.org/10.12688/f1000research.21782.1

Zhang, T., Li, H., Ma, S., Cao, J., Liao, H., Huang, Q., & Chen, W. (2023). The newest Oxford Nanopore R10.4.1 full-length 16S rRNA sequencing enables the accurate resolution of species-level microbial community profiling. Applied and Environmental Microbiology, 89(10), e00605-23. https://doi.org/10.1128/aem.00605-23


