# BDS491-Capstone

The project was completed in the Biological Data Sciences Captsone course. 

Source data derived from this study https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9935790/.

I hypothesize that the sampling months will have a significant difference in the abundances of Bacillus, Candidatus, Udaeobacter, Conexibacter, and Acidothermus because of the transitions of the seasons and the variation in temperature.  

Methods:
In order to work with phyloseq objects, we need to load the “phyloseq” package, using the “library()” function.  For future graphs and figures, we will also load the “ggplot2” package with the same function for loading the phyloseq object. Our group was provided with an RDS file that contained the raw data from the soil. We wanted to save this data into a phyloseq object named “soil.ps,”  using the “readRDS()” function.  To visualize the distribution of the sample sequencing depth, we first need to create a dataframe with a column for the read counts of each sample. We created and saved this data frame into a variable by using “data.frame(sum = sample_sums(soil.ps).” With the “ggplot()” function we created a histogram of the sample read counts. The histogram uses the created data frame with the read counts. Along the x axis would be the sum of the read counts and the y axis would be the frequencies. 

Figure 3.1.1. Sample depth prior to normalization. The figure displays the distribution of the sample sequencing depth, and there is a presence of two extreme values on the positive end at 121,972 and 126,342 reads.

 From this distribution, there were not any reads that were less than 5,000. By using the “mean()” function, it was calculated that there were 44032.48 reads per sample on average. Using the “min()” and “max()  functions, it was identified that the minimum number of reads is 21677 and the maximum number of reads is 126342. From our histogram of the sample read counts, we knew that there were two extreme values greater than 100,000 reads, so we used the “which()” function to identify these counts. To save the samples that we want to resample, we want to include the OTU that has a sample sum of less than 100,000. To do this we saved the OTU table from the phyloseq object we created prior, “soil.ps,” using the”otu_table()” function. We saved the OTU table to a variable called  “soil_otu.” When then created a variable named “saved_samples_to_resample” and setted the variable equal to the OTUs that had sample sums that were greater than 100,000 reads with “soil_otu[which(sample_sums(soil_phyllo)>100000),].” It was identified that the two samples with reads higher than 100,000 were lane1-s010-indexSA710-SA501-GTCTGCTA-ATCGTACG-10-JN and lane1-s012-indexSA712-SA501-TATAGCGA-ATCGTACG-12-JN with reads of 126,342 and 121,972 respectively.To extract the extreme values, we used the same call except we switched the sign to a less than sign: “soil_otu[which(sample_sums(soil_phyllo)<100000),]” and saved this to a variable,  “saved_samples_less_than.” We wanted to resample from the top two extreme values with the sample size being the median of the read depth. In order to resample, the “rarefy_even_depth()” function is needed, and the median is identified with the “median()” function. We then stored the resampled data of the extreme values to the variable “top_two_phyllo_rarefied,” using the function call, “rarefy_even_depth(saved_samples_to_resample, sample.size = median(rowSums(saved_samples_less_than))).”  We then merged the OTUs together to the “merged_otu” variable, using the “merge_phyloseq()” function, which was called as “merge_phyloseq(top_two_phyllo_rarefied,saved_samples_less_than).” After merging the OTU tables, we setted the OTU table in the “soil.ps” directly by calling “soil_phyllo@otu_table<-merges_otu.” In order to ensure the two extreme values were correctly resampled, we repeat the procedure earlier to create a data frame with the sample depths and the histogram of the sample read counts.

Figure 3.1.2. Sample summary after resampling the top two outliers. The figure displays the distribution of the sample sequencing depth without the two extreme outliers.

After verifying there were no longer extreme values above 100,000 reads, we normalized our pruned data with refraction and relative abundance. For rarefaction, also referred to as resampling, we wanted to randomly draw from the same number of reads per sample, and this is done in r with the “rarefy_even_depth()” function. We created and setted the variable, “qc_resample,” to “rarefy_even_depth(soil.ps, sample.size=min(sample_sums(soil.ps))).” This reduced the number of reads for each sample down to 21677. To normalize with relative abundance, we needed to determine the percentage of each OTU in the sample with the “transform_sample_counts()” function. This transformation was done by creating and setting a variable called “qc_relative_abundance” to “transform_sample_counts(soil.ps, function(x)x/sum(x)).”

Since the interest of the hypothesis is based on the abundances between the sampling month, the alpha and beta diversity of the quality controlled resampled data was analyzed. 
For beta diversity,  a between class analysis and PERMANOVA test was performed. An ordination plot of the quality controlled resample data was produced with the “ordianate(),” using  the PCoA method, and “plot_ordination()” functions. To perform the ordination on the phyloseq object, “ordinate(qc_resample, "PCoA")” was saved to a variable called “GP.ord.” The figure was then made with the call, “plot_ordination(qc_resample, GP.ord, color = "SamplingMonth", title = "Sample").” To maximize the variance between the groups, another ordination was performed but with the CAP method, bray distance, and using the sampling months. The ordination plot for both ordinations were created. Next, to perform a  PERMANOVA test, the bray curtis distance from the ordination was calculated with the phyloseq::distance() function and a data frame is created from the sample using the “data.frame()” function. Lastly, the “adonis2()” test is used to perform the permutation.  
For alpha diversity, “plot_richness()” was used to view the observed, Chao1, and Shannon indices with the x axis set equal to the sampling months. A Kruskal-Wallis test and pairwise comparison was applied to these plots with the “stat_compare_means()” function with comparison between each month. 
Ordination plots for the first eight unique genera from the phyloseq object. From these ordination plots, I selected three genera with interesting ordination plots. Bacillus was chosen to be included because two of the plant growth-promoting rhizobacteria were  Bacillus subtilis and Bacillus amyloliquefaciens. Thus, Bacillus, Candidatus Udaebacter, Conexibacter, and Acidothermus were chosen to study the alpha and beta diversity.  From the quality controlled resampled data, a subset was created for each genus with “subset_taxa()” function and then resampled again with the “rarefy_even_depth()” function. The process to study the alpha and beta diversity of the quality controlled resampled data was repeated on each of the resampled subsets. 
