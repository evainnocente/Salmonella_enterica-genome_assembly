# _Salmonella enterica_ genome assembly
### BINF_6110 Assignment 1 Part 2 by Eva Burguete-Innocente
### February 8th 2026

## Introduction

Genome assembly is the process of using sequencing reads to construct the genomic sequence of an organism (Wick & Holt, 2019). Accurate and complete genome assemblies are important for several reasons, such as understanding bacterial population genetics, identifying genetic variants, and clarifying bacterial evolution and antimicrobial resistance (Breckell & Silander, 2021; Kumar et al., 2025; Taylor et al., 2019; Wang et al., 2025;). This is particularly relevant for Salmonella enterica, as it can cause severe illness, so accurate detection and species identification is crucial (Luan et al., 2024; Taylor et al., 2019). Nanopore sequencing is faster and more portable than short-read sequencing, but it has historically had a higher error rate, although this has recently improved (Zhang et al., 2023). Additionally, the assembled bacterial genome and its plasmids may not be fully closed (i.e. completely circular) which could result in missing information (Taylor et al., 2019).  Many different assembly methods can be used with long-read sequencing data. Some tools include Canu (Koren et al., 2017), Flye (Kolmogorov et al., 2019), Raven (Vaser & Šikić, 2021), Redbean (Ruan & Li, 2020), Shasta (Shafin et al., 2020), and many more.
After a genome is assembled, it must be aligned to a reference genome to confirm sequence accuracy (Breckell & Silander, 2021). However, reference genomes do not always exist for the study species, increasing the challenges of genome assembly (Taylor et al., 2019). 	
	Wick and Holt (2019), Breckell and Silander (2021), and Kumar et al. (2025) compared genome assembly methods and quantified performance benchmarks. Briefly, Flye is a popular, accurate, and fast assembler (Breckell & Silander, 2021; Kumar et al., 2025; Wick & Holt, 2019). Canu is reliable but has a long computational runtime, and is also no longer under development (Koren et al., 2017; Kumar et al., 2025; Wick & Holt, 2019). Other tools like Shasta were fast, yet not as reliable (Wick & Holt, 2019). No one tool performed best across all benchmarks, therefore different tools may be preferable for different analysis goals (Breckell & Silander, 2021).
	Wick and Holt (2019) found that Raven v0.0.5 performed very well in benchmarks like reliability and robustness. Raven is an assembler that uses an overlap-layout-consensus assembler algorithm (Vaser & Šikić, 2021). However, it struggled with circularisation (excluding some bases) and assembling small plasmids (Wick & Holt, 2019). Breckell and Silander (2021) found that Raven assembled the least number of contigs, indicating its accuracy at predicting structure. Kumar et al. (2025) found that Raven v1.8.1 was user-friendly, accurate, and fast. Given its accuracy, reliability, and ease of use, I will use Raven to assemble the Salmonella enterica genome. 


## Methods

I inspected the quality of the raw reads with FastQC v0.12.1 (source) and assembled the S. enterica genome using Flye v2.9.6 with –nano-hq for high-quality reads and four threads, as Raven stopped working (Kolmogorov et al., 2019). However, Flye is also a very well-performing tool and is particularly successful with plasmid assembly (Wick and Holt 2019; Breckell & Silander 2021; Kumar et al., 2025). I inspected the quality of the assembled genome by comparing it to the reference genome with QUAST v5.3.0 (Gurevich et al., 2013). A visual comparison of the reference and assembled genome was generated using Proksee v2026-01-10 (Grant et al., 2023). The raw reads were then aligned to the reference genome using minimap2 v2.30, specifying “-a” to output the file in .sam format and “-x map-ont” to map reads to a reference genome (accession: GCF_000006945.2) (Li, 2016). The assembled genome was sorted, indexed, and converted to .bam format with samtools v1.21 (Li et al., 2009).

Clair3 v1.2.0 was used to call variants, as this tool is designed specifically for long-read sequencing data (Zheng et al., 2021). It is a deep-learning method which combines pileup and full-alignment networks (Zheng et al., 2021). Variants were called with the following parameters: --no_phasing_for_fa and --include_all_ctgs, and --haploid-precise as the S. enterica genome is haploid (Schiffer et al., 2025; Zheng et al., 2021). 

I extracted start and end positions of contigs in the reference genome for plotting with Circos v0.69-8 (Krzywinski et al., 2009). I used bcftools view -v (v1.23) to split the variants into separate SNPs and indels files, then bedtools makewindows and coverage (v2.31.1) to specify windows of 5000 base pairs and count the variants in each window, to prepare the data for plotting with Circos (Danecek et al., 2021; Quinlan & Hall, 2010). 

Proksee (https://proksee.ca/) was used to generate a plot showing the % identity (BLAST) of the assembled genome to the reference genome.  I used Integrative Genomics Viewer v2.16.0 to look at individual genes and variants (Robinson et al., 2011). All command-line tools are installed in conda environments and analyses run on a virtual machine running Ubuntu 25.10 with 8 cores and 12 GB of RAM.	


## Results

There were 196031 sequences in the raw reads, with 52.24% GC content, high average read quality, and a total length of 4, 951, 383 base pairs. The assembled genome was quite similar to the reference: the genome fraction was 95.669%, 52.19% GC content, and total length 5, 104, 812 base pairs, with 27.39 mismatches per 100kbp.  The assembly had 3 contigs, while the reference has two. The assembled genome had mostly very high percent identity to the reference genome, except for a few regions, including the plasmid (Fig. 1). 

There were 10526 variants called, with 9346 SNPs and 1190 indels. Figure 2. shows the frequency of SNPs (black bars) and frequency of indels (green bars) mapped to their position on the reference genome. Some genes contained a high number of both SNPs and indels, particularly STM1022 (Fig 3.).


## Discussion

S. enterica typhimurium is a notable cause of disease in humans and animals worldwide, and can adapt to new environments and successfully infect hosts via mutation and evolution. Genetic characterisation of this bacterium is critical for understanding its disease-causing abilities and to combat development of antimicrobial resistance. (Xiang et al., 2024). 
The assembled S. enterica genome was of high quality and had high sequence identity to the reference genome, with only a few regions of lesser % identity according to BLAST, most notably in the plasmid pLST (NC_002377.2) (Fig. 1) which had a high frequency of both SNPs and indels (Fig 2.). Plasmids often contain antibiotic resistance genes, and evolve quickly, which may explain why the assembled plasmid had lower sequence identity to the reference genome (Peng et al., 2018). Genetic variation in S. enterica typhimurium strains may also arise from transposons or hybridisation with other plasmids (Hiley, Graham, & Jennison, 2019). 

According to Fig. 2, a variant-rich region occurred in the genome around 110-120kb. However, when visualising the region on IGV, this variant-rich region did not correspond, and the discrepancy could not be resolved. However, numerous other variants were located in the genome. In particular, a SNP in the STM1987 gene changed G to A at position 206995. A SNP in the STM1987 gene resulted in an amino acid change which increased survival of S. enterica in human cells and fitness during infection of a mouse model (Sokaribo et al., 2021). Although this is not the same SNP as is present in the assembled genome, this result highlights the critical role that single SNPs can play in S. enterica virulence and highlights an avenue for future research.

Genes involved in virulence are often acquired by S. enterica via horizontal gene transfer, particularly transduction of bacteriophages (Hermans et al., 2006). The Gifsy-2 prophage is present in S. enterica typhimurium serovar, is genetically diverse (Bosma, Noireaux, & Bowden, 2025), and contributes to virulence (Ho et al., 2002). When inspecting this region in IGV (annotated as STM1022), many SNPs and indels were present (Fig 3.). Mohammed & Cormican (2015) also identified many variants in STM1022, including a non-synonymous coding SNP at position 111767 which resulted in an amino acid change from lysine to glutamate. I identified an indel that encompassed position 1111767, where the reference allele was TGG and the alternate was either a deletion to result in T or an insertion to result in TCCCCGG, either one of which would result in a frameshift and change in the amino acid sequence. The genetic diversity of the Gifsy-2 prophage reflects its involvement in virulence and its ability to target a broad variety of hosts (Figueroa‐Bossi et al., 2021). 

In conclusion, the assembled genome of S. enterica was of high quality and contained many variants similar to those of the reference genome S. enterica typhimurium, some of which are known to be involved in pathogenicity.


## Figures

<img width="975" height="642" alt="image" src="https://github.com/user-attachments/assets/4e569cf7-c4f8-4fe5-b7a1-6836a1223e94" />

Figure 1. Comparison of the assembled genome to the reference genome. BLAST was used to calculate the sequence identity of the two genome, and the % identity can be seen via the height of the green bars in the outer ring of the plot.


<img width="975" height="975" alt="image" src="https://github.com/user-attachments/assets/9f0ace56-75b7-4470-bb65-f82059900e0c" />

Figure 2. Frequency of SNPs (black bars) and indels (green bars) mapped to their positions on the reference genome. The plasmid pLST is shown in blue and the remainer of the genome in red.


<img width="975" height="807" alt="image" src="https://github.com/user-attachments/assets/c2f3ed47-af9e-47fc-af50-3fbb72570e57" />

Figure 3. Zoomed in screenshot of IGV, showing the STM1022 gene and the number variants associated with it.





## References

Bosma, J. S., Noireaux, V., & Bowden, S. D. (2025). A Gifsy prophage-encoded protein confers broad phage resistance in Salmonella enterica and is widely distributed across Enterobacteriaceae. Applied and Environmental Microbiology, 91(12), e01384-25. https://doi.org/10.1128/aem.01384-25

Breckell, G. L., & Silander, O. K. (2021). Do you want to build a genome? Benchmarking hybrid bacterial genome assembly methods. https://doi.org/10.1101/2021.11.07.467652

Danecek, P., Bonfield, J. K., Liddle, J., Marshall, J., Ohan, V., Pollard, M. O., Whitwham, A., Keane, T., McCarthy, S. A., Davies, R. M., & Li, H. (2021). Twelve years of samtools and bcftools. GigaScience, 10(2), giab008. https://doi.org/10.1093/gigascience/giab008

Figueroa‐Bossi, N., Uzzau, S., Maloriol, D., & Bossi, L. (2001). Variable assortment of prophages provides a transferable repertoire of pathogenic determinants in Salmonella. Molecular Microbiology, 39(2), 260–272. https://doi.org/10.1046/j.1365-2958.2001.02234.x

Grant, J. R., Enns, E., Marinier, E., Mandal, A., Herman, E. K., Chen, C.-Y., Graham, M., Van Domselaar, G., & Stothard, P. (2023). Proksee: In-depth characterization and visualization of bacterial genomes. Nucleic Acids Research, 51(W1), W484–W492. https://doi.org/10.1093/nar/gkad326

Gurevich, A., Saveliev, V., Vyahhi, N., & Tesler, G. (2013). QUAST: Quality assessment tool for genome assemblies. Bioinformatics (Oxford, England), 29(8), 1072–1075. https://doi.org/10.1093/bioinformatics/btt086

Hermans, A. P. H. M., Beuling, A. M., van Hoek, A. H. A. M., Aarts, H. J. M., Abee, T., & Zwietering, M. H. (2006). Distribution of prophages and SGI-1 antibiotic-resistance genes among different Salmonella enterica serovar Typhimurium isolates. Microbiology, 152(7), 2137–2147. https://doi.org/10.1099/mic.0.28850-0

Hiley, L., Graham, R. M. A., & Jennison, A. V. (2019). Genetic characterisation of variants of the virulence plasmid, pSLT, in Salmonella enterica serovar Typhimurium provides evidence of a variety of evolutionary directions consistent with vertical rather than horizontal transmission. PLOS ONE, 14(4), e0215207. https://doi.org/10.1371/journal.pone.0215207

Ho, T. D., Figueroa-Bossi, N., Wang, M., Uzzau, S., Bossi, L., & Slauch, J. M. (2002). 
Identification of gtge, a novel virulence factor encoded on the gifsy-2 bacteriophage of salmonella enterica serovar typhimurium. Journal of Bacteriology, 184(19), 5234–5239. https://doi.org/10.1128/JB.184.19.5234-5239.2002

Kolmogorov, M., Yuan, J., Lin, Y., & Pevzner, P. A. (2019). Assembly of long, error-prone reads using repeat graphs. Nature Biotechnology, 37(5), 540–546. https://doi.org/10.1038/s41587-019-0072-8

Koren, S., Walenz, B. P., Berlin, K., Miller, J. R., Bergman, N. H., & Phillippy, A. M. (2017). Canu: Scalable and accurate long-read assembly via adaptive k -mer weighting and repeat separation. Genome Research, 27(5), 722–736. https://doi.org/10.1101/gr.215087.116

Krzywinski, M., Schein, J., Birol, İ., Connors, J., Gascoyne, R., Horsman, D., Jones, S. J., & Marra, M. A. (2009). Circos: An information aesthetic for comparative genomics. Genome Research, 19(9), 1639–1645. https://doi.org/10.1101/gr.092759.109

Kumar, M. S., Krishna, M. B., Soman, K. P., Stanley, J., Pourmand, N., Suravajhala, P., & Babu, T. G. S. (2025). Benchmarking long-read assembly tools and preprocessing strategies for bacterial genomes: A case study on E. coli DH5α. Biotechnology Reports, 48, e00931. https://doi.org/10.1016/j.btre.2025.e00931

Li, H. (2016). Minimap and miniasm: Fast mapping and de novo assembly for noisy long sequences. Bioinformatics, 32(14), 2103–2110. https://doi.org/10.1093/bioinformatics/btw152

Li, H., Handsaker, B., Wysoker, A., Fennell, T., Ruan, J., Homer, N., Marth, G., Abecasis, G., & Durbin, R. (2009). The sequence alignment/map format and samtools. Bioinformatics, 25(16), 2078–2079. https://doi.org/10.1093/bioinformatics/btp352

Luan, T., Commichaux, S., Hoffmann, M., Jayeola, V., Jang, J. H., Pop, M., Rand, H., & Luo, Y. (2024). Benchmarking short and long read polishing tools for nanopore assemblies: Achieving near-perfect genomes for outbreak isolates. BMC Genomics, 25(1), 679. https://doi.org/10.1186/s12864-024-10582-x

Peng, M., Salaheen, S., Buchanan, R. L., & Biswas, D. (2018). Alterations of salmonella enterica serovar typhimurium antibiotic resistance under environmental pressure. Applied and Environmental Microbiology, 84(19), e01173-18. https://doi.org/10.1128/AEM.01173-18

Quinlan, A. R., & Hall, I. M. (2010). BEDTools: A flexible suite of utilities for comparing genomic features. Bioinformatics (Oxford, England), 26(6), 841–842. https://doi.org/10.1093/bioinformatics/btq033

Robinson, J. T., Thorvaldsdóttir, H., Winckler, W., Guttman, M., Lander, E. S., Getz, G., & Mesirov, J. P. (2011). Integrative genomics viewer. Nature Biotechnology, 29(1), 24–26. https://doi.org/10.1038/nbt.1754

Ruan, J., & Li, H. (2020). Fast and accurate long-read assembly with wtdbg2. Nature Methods, 17(2), 155–158. https://doi.org/10.1038/s41592-019-0669-3

Schiffer, A. M., Rahman, A., Sutton, W., Putnam, M. L., & Weisberg, A. J. (2025). A comparison of short- and long-read whole-genome sequencing for microbial pathogen epidemiology. mSystems, 10(12), e01426-25. https://doi.org/10.1128/msystems.01426-25

Shafin, K., Pesout, T., Lorig-Roach, R., Haukness, M., Olsen, H. E., Bosworth, C., Armstrong, J., Tigyi, K., Maurer, N., Koren, S., Sedlazeck, F. J., Marschall, T., Mayes, S., Costa, V., Zook, J. M., Liu, K. J., Kilburn, D., Sorensen, M., Munson, K. M., … Paten, B. (2020). Nanopore sequencing and the Shasta toolkit enable efficient de novo assembly of eleven human genomes. Nature Biotechnology, 38(9), 1044–1053. https://doi.org/10.1038/s41587-020-0503-6

Sokaribo, A. S., Balezantis, L. R., MacKenzie, K. D., Wang, Y., Palmer, M. B., Chung, B., Herman, N. J., McCarthy, M. C., Chen, J. M., & White, A. P. (2021). A snp in the cache 1 signaling domain of diguanylate cyclase stm1987 leads to increased in vivo fitness of invasive salmonella strains. Infection and Immunity, 89(4), e00810-20. https://doi.org/10.1128/IAI.00810-20

Taylor, T. L., Volkening, J. D., DeJesus, E., Simmons, M., Dimitrov, K. M., Tillman, G. E., Suarez, D. L., & Afonso, C. L. (2019). Rapid, multiplexed, whole genome and plasmid sequencing of foodborne pathogens using long-read nanopore technology. Scientific Reports, 9(1), 16350. https://doi.org/10.1038/s41598-019-52424-x

Vaser, R., & Šikić, M. (2021). Time- and memory-efficient genome assembly with Raven. Nature Computational Science, 1(5), 332–336. https://doi.org/10.1038/s43588-021-00073-4

Wang, X., Koster, A. D., Koenders, B. B., Jonker, M., Brul, S., & Ter Kuile, B. H. (2025). De novo acquisition of antibiotic resistance in six species of bacteria. Microbiology Spectrum, 13(3), e01785-24. https://doi.org/10.1128/spectrum.01785-24

Wick, R. R., & Holt, K. E. (2019). Benchmarking of long-read assemblers for prokaryote whole genome sequencing (8:2138). F1000Research. https://doi.org/10.12688/f1000research.21782.1
Wick, R. R., Judd, L. M., Cerdeira, L. T., Hawkey, J., Méric, G., Vezina, B., Wyres, K. L., & Holt, K. E. (2021). Trycycler: Consensus long-read assemblies for bacterial genomes. Genome Biology, 22(1), 266. https://doi.org/10.1186/s13059-021-02483-z

Xiang, Y., Zhu, K., Min, K., Zhang, Y., Liu, J., Liu, K., Han, Y., Li, Xinge, Du, X., Wang, X., Huang, Y., Li, Xinping, Peng, Y., Yang, C., Liu, H., Liu, H., Li, Xiaoying, Wang, H., Wang, C., … Qiu, S. (2024). Characterization of a Salmonella enterica serovar Typhimurium lineage with rough colony morphology and multidrug resistance. Nature Communications, 15(1), 6123. https://doi.org/10.1038/s41467-024-50331-y

Zhang, T., Li, H., Ma, S., Cao, J., Liao, H., Huang, Q., & Chen, W. (2023). The newest Oxford Nanopore R10.4.1 full-length 16S rRNA sequencing enables the accurate resolution of species-level microbial community profiling. Applied and Environmental Microbiology, 89(10), e00605-23. https://doi.org/10.1128/aem.00605-23
