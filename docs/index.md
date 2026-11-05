---
title: "R notebook"
output: 
  html_document:
    keep_md: true
    toc: true
    toc_float: true
    toc_depth: 4
   # code_folding: hide
    number_sections: true
    theme: cosmo

knit: (function(input_file, encoding) {
  out_dir <- 'docs';
  rmarkdown::render(input_file,
 encoding=encoding,
 output_file=file.path(dirname(input_file), out_dir, 'index.html'))})
---





## How to use this file

- Gray boxes are **code chunks**. Click the green ▶ arrow in the top-right corner of a chunk to run it.
- The results appear right under the chunk.
- Text after a `#` is a **comment**. R ignores it; it's a note for you.
- R is case sensitive: `Temperature` and `temperature` are different.
- Run the chunks in order, top to bottom.

##  Install and load packages

First job is to install and load the packages that we are going to use today. A very useful package that many people use in R is tidyverse. It contains many useful tools such as dyplr for data wrangling, ggplot for making figures etc. If you'd like to read more about what is available in this package, please see: https://www.tidyverse.org/


``` r
install.packages("tidyverse")
```

Load the package every time you open R:


``` r
library(tidyverse)
```

# Data loading 
### data
This amplicon data came from the Fuhrman lab. Here is the [paper describing it](https://www.nature.com/articles/s41597-025-05423-9). 


This is the path to my data. PLEASE EDIT 


``` r
amplicon <- read_csv("/Users/oliviaahern/Documents/GitHub/SES_2026/data/amplicon_data.csv")
```

You should now see `amplicon` in the **Environment** panel (top right).

### Look at the data

The first 6 rows:


``` r
head(amplicon)
```

```
## # A tibble: 6 × 54
##    ...1 time                  lat   lon depth cruise SampleID  Domain Supergroup
##   <dbl> <dttm>              <dbl> <dbl> <dbl> <chr>  <chr>     <chr>  <chr>     
## 1     1 2009-10-20 04:38:00  38.3 -24.7     0 JC039  AMT.19.1… Bacte… <NA>      
## 2     2 2009-10-20 04:38:00  38.3 -24.7     0 JC039  AMT.19.1… Bacte… <NA>      
## 3     3 2009-10-20 04:38:00  38.3 -24.7     0 JC039  AMT.19.1… Bacte… <NA>      
## 4     4 2009-10-20 04:38:00  38.3 -24.7     0 JC039  AMT.19.1… Bacte… <NA>      
## 5     5 2009-10-20 04:38:00  38.3 -24.7     0 JC039  AMT.19.1… Bacte… <NA>      
## 6     6 2009-10-20 04:38:00  38.3 -24.7     0 JC039  AMT.19.1… Bacte… <NA>      
## # ℹ 45 more variables: Division <chr>, Phylum <chr>, Class <chr>, Order <chr>,
## #   Family <chr>, Genus <chr>, Species <chr>, ProPortal_ASV_Ecotype <chr>,
## #   Sequence_Type <chr>, ASV <chr>, ASV_hash <chr>, Level_1 <chr>,
## #   Level_1_1 <chr>, Level_2 <chr>, Corrected_dada2_Sequence_Counts <dbl>,
## #   Corrected_Sequence_Counts <dbl>, Relative_Abundance <dbl>, Cruise_ID <chr>,
## #   Station <chr>, Date <chr>, Day <dbl>, Month <dbl>, Year <dbl>,
## #   Time_Sampled <dttm>, Latitude <dbl>, Longitude <dbl>, Bottom_Depth <dbl>, …
```

How many rows and columns?


``` r
nrow(amplicon)
```

```
## [1] 45417
```

``` r
ncol(amplicon)
```

```
## [1] 54
```

The column names:


``` r
names(amplicon)
```

```
##  [1] "...1"                            "time"                           
##  [3] "lat"                             "lon"                            
##  [5] "depth"                           "cruise"                         
##  [7] "SampleID"                        "Domain"                         
##  [9] "Supergroup"                      "Division"                       
## [11] "Phylum"                          "Class"                          
## [13] "Order"                           "Family"                         
## [15] "Genus"                           "Species"                        
## [17] "ProPortal_ASV_Ecotype"           "Sequence_Type"                  
## [19] "ASV"                             "ASV_hash"                       
## [21] "Level_1"                         "Level_1_1"                      
## [23] "Level_2"                         "Corrected_dada2_Sequence_Counts"
## [25] "Corrected_Sequence_Counts"       "Relative_Abundance"             
## [27] "Cruise_ID"                       "Station"                        
## [29] "Date"                            "Day"                            
## [31] "Month"                           "Year"                           
## [33] "Time_Sampled"                    "Latitude"                       
## [35] "Longitude"                       "Bottom_Depth"                   
## [37] "Temperature"                     "Salinity"                       
## [39] "Oxygen"                          "Silicate"                       
## [41] "NO2"                             "NO3"                            
## [43] "NH3"                             "PO4"                            
## [45] "DOC"                             "TOC"                            
## [47] "PAR"                             "Chlorophyll"                    
## [49] "DCM"                             "Season"                         
## [51] "Longhurst_Short"                 "Longhurst_Long"                 
## [53] "Ocean_Basin"                     "Depth_Category"
```

Use `$` to grab one column. `unique()` shows each value once:


``` r
unique(amplicon$cruise)
```

```
## [1] "JC039" "JC053"
```

**Each row is one ASV (one 16S/18S sequence) in one sample.** So each sample has many rows.

**Questions:**

1. How many rows are in the dataset?
2. What cruises are there?

## Get distinct info

To plot sample locations, we only want one row per sample. `distinct()` keeps unique combinations of the columns we list.


``` r
samples <- distinct(amplicon, SampleID, cruise, lat, lon, depth, Temperature, Salinity, Season)
samples
```

```
## # A tibble: 52 × 8
##    SampleID        cruise   lat   lon depth Temperature Salinity Season
##    <chr>           <chr>  <dbl> <dbl> <dbl>       <dbl>    <dbl> <chr> 
##  1 AMT.19.1-60231  JC039   38.3 -24.7     0        21.1     36.2 Autumn
##  2 AMT.19.2-60232  JC039   35.5 -28.6     0        23.2     36.7 Autumn
##  3 AMT.19.3-60233  JC039   33.4 -31.3     0        24.4     36.8 Autumn
##  4 AMT.19.4-60234  JC039   31.4 -33.7     0        25.0     37.2 Autumn
##  5 AMT.19.5-60235  JC039   29.2 -36.0     0        26.0     37.3 Autumn
##  6 AMT.19.6-60236  JC039   27.2 -37.9     0        26.5     37.5 Autumn
##  7 AMT.19.7-60237  JC039   25.4 -39.6     0        27.1     37.5 Autumn
##  8 AMT.19.8-60238  JC039   23.2 -40.7     0        27.3     37.2 Autumn
##  9 AMT.19.9-60239  JC039   21.1 -39.2     0        27.4     36.8 Autumn
## 10 AMT.19.10-60240 JC039   21.1 -39.3     6        27.5     36.8 Autumn
## # ℹ 42 more rows
```

# Your first plot

Every ggplot has the same 3 parts:

1. `ggplot(data, aes(x = ..., y = ...))`: which data, and which columns go on each axis.
2. `+ geom_...()`: what kind of plot (points, bars, lines).
3. `+ labs()` (optional): axis labels.

Where were the samples collected?


``` r
ggplot(samples, aes(x = lon, y = lat)) +
  geom_point()
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

Now color the points by cruise and add labels:


``` r
ggplot(samples, aes(x = lon, y = lat, color = cruise)) +
  geom_point() +
  labs(x = "Longitude", y = "Latitude", color = "Cruise")
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-10-1.png)<!-- -->


### Temperature vs. depth

`scale_y_reverse()` flips the y-axis so the surface is at the top, like the ocean.


``` r
ggplot(samples, aes(x = Temperature, y = depth)) +
  geom_point() +
  scale_y_reverse() +
  labs(x = "Temperature (°C)", y = "Depth (m)")
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

### Who is there?

`geom_col()` makes bars. Each bar is one sample, and the colors show which phyla are in it.



``` r
ggplot(amplicon, aes(x = SampleID, y = Relative_Abundance, fill = Domain)) +
  geom_col() +
  labs(x = "Sample", y = "Relative abundance")
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

change the legend position

``` r
ggplot(amplicon, aes(x = SampleID, y = Relative_Abundance, fill = Domain)) +
  geom_col() +
  labs(x = "Sample", y = "Relative abundance") + 
  theme(legend.position = 'bottom')
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-13-1.png)<!-- -->

`facet_wrap()` makes a separate panel for each sequence type.
change the legend position

``` r
ggplot(amplicon, aes(x = SampleID, y = Relative_Abundance, fill = Domain)) +
  geom_col() +
  labs(x = "Sample", y = "Relative abundance") + 
  facet_wrap (~Cruise_ID, scales='free') +
  theme(legend.position = 'bottom')
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-14-1.png)<!-- -->



**Questions:**

1. Which Domain is most abundant?
2. Do all the samples look the same?

### Just one cruise

`filter()` keeps only the rows that match a condition. `==` means "is equal to."


``` r
one_cruise <- filter(amplicon, cruise == "JC053")
```

Now make the same stacked bar plot with only that cruise:


``` r
ggplot(one_cruise, aes(x = SampleID, y = Relative_Abundance, fill = Domain)) +
  geom_col() +
  facet_wrap(~ Sequence_Type) +
  labs(x = "Sample", y = "Relative abundance", title = "JC053 cruise")
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-16-1.png)<!-- -->

## Activities

Replace each `___` with a column name, then click ▶.

**1.** Plot salinity (x) vs. temperature (y) using the `samples` data.


``` r
ggplot(samples, aes(x = ___, y = ___)) +
  geom_point()
```

```
## Error in parse(text = input): <text>:1:26: unexpected input
## 1: ggplot(samples, aes(x = __
##                              ^
```

**2.** Take your plot from #1 and color the points by `Season`.


``` r
ggplot(samples, aes(x = Salinity, y = Temperature, color = ___)) +
  geom_point()
```

```
## Error in parse(text = input): <text>:1:61: unexpected input
## 1: ggplot(samples, aes(x = Salinity, y = Temperature, color = __
##                                                                 ^
```

**3.** Remake the bar plot from section 8, but fill by `Class` instead of `Domain`.


``` r
ggplot(amplicon, aes(x = SampleID, y = Relative_Abundance, fill = ___)) +
  geom_col() +
  facet_wrap(~ Sequence_Type) +
  labs(x = "Sample", y = "Relative abundance") +
  theme(legend.position='none')
```

```
## Error in parse(text = input): <text>:1:68: unexpected input
## 1: ggplot(amplicon, aes(x = SampleID, y = Relative_Abundance, fill = __
##                                                                        ^
```




# looking at diversity metrics 

### alpha diversity
Alpha diversity looks at how many species are in a sample. different metrics include

- species number - the number of species per sample. 
- evenness - how even is the community? Is it dominated by a single species? Ranges from 1 (every species is in equal proportion) to 0 (dominated by a single species)

#### number of species per sample


``` r
species_per_sample <- amplicon %>%
  group_by(SampleID) %>%
  summarise(n_species = n_distinct(Species, na.rm = TRUE))

species_per_sample
```

```
## # A tibble: 52 × 2
##    SampleID          n_species
##    <chr>                 <int>
##  1 AMT.19.1-60231          101
##  2 AMT.19.10-60240          81
##  3 AMT.19.11-60241          54
##  4 AMT.19.12-60242         137
##  5 AMT.19.13-60243          76
##  6 AMT.19.14-60244          89
##  7 AMT.19.15-60245         149
##  8 AMT.19.16.1-60246       135
##  9 AMT.19.16.2-60247       104
## 10 AMT.19.17.1-60248       108
## # ℹ 42 more rows
```

``` r
# you can add this to your amplicon dataset
# remember the column name we added though is n_species
amplicon <- left_join(amplicon, species_per_sample, by = "SampleID")
```

lets look at a barplot of the number of species per sample 



``` r
ggplot(amplicon, aes(x = SampleID, y = n_species)) +
  geom_col() +
  labs(x = "SampleID", y = "Number of ASVs")
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-21-1.png)<!-- -->


and we can again facet it, lets try with season


``` r
ggplot(amplicon, aes(x = SampleID, y = n_species)) +
  geom_col() +
  labs(x = "SampleID", y = "Number of ASVs") +
  facet_wrap (~Season, scales='free')
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-22-1.png)<!-- -->

##### plotting against environmental factors



``` r
ggplot(amplicon, aes(x = Temperature, y = n_species)) +
  geom_point() +
  labs(x = "Temperature (°C)", y = "Number of ASVs")
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-23-1.png)<!-- -->


and color it by season 


``` r
color_seasons = c("Autumn" = 'orange', 
                  "Spring" = "forestgreen", 
                  "Summer" = 'red',
                  "Winter" = 'navy')
ggplot(amplicon, aes(x = Temperature, y = n_species, fill=Season)) +
  geom_point(pch=21, size=4) +
  labs(x = "Temperature (°C)", y = "Number of ASVs") +
  theme(legend.position ='bottom') +
  scale_fill_manual(values=color_seasons)
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-24-1.png)<!-- -->


#### species evenness

We are calculating Pielou's evenness. It ranges from 0 (uneven/dominated by a single species) to 1 (completely even/all species in equal proportions)


``` r
# Pielou's evenness (J) = Shannon diversity / ln(number of species)
# assumes your counts column is called Abundance -- change it if yours is named differently
evenness_per_sample <- amplicon %>%
  filter(!is.na(Species)) %>%
  group_by(SampleID, Species) %>%
  summarise(abund = sum(Relative_Abundance), .groups = "drop") %>%
  filter(abund > 0) %>%
  group_by(SampleID) %>%
  mutate(p = abund / sum(abund)) %>%
  summarise(evenness = -sum(p * log(p)) / log(n()))

evenness_per_sample
```

```
## # A tibble: 52 × 2
##    SampleID          evenness
##    <chr>                <dbl>
##  1 AMT.19.1-60231       0.727
##  2 AMT.19.10-60240      0.562
##  3 AMT.19.11-60241      0.573
##  4 AMT.19.12-60242      0.494
##  5 AMT.19.13-60243      0.656
##  6 AMT.19.14-60244      0.605
##  7 AMT.19.15-60245      0.690
##  8 AMT.19.16.1-60246    0.688
##  9 AMT.19.16.2-60247    0.703
## 10 AMT.19.17.1-60248    0.686
## # ℹ 42 more rows
```

``` r
# you can add this to your amplicon dataset
# remember the column name we added though is evenness
amplicon <- left_join(amplicon, evenness_per_sample, by = "SampleID")
```

lets look at a barplot of the evenness per sample 



``` r
ggplot(amplicon, aes(x = SampleID, y = evenness)) +
  geom_col() +
  labs(x = "SampleID", y = "Number of ASVs")
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-26-1.png)<!-- -->



# Misc

Other cool microbiome resources include:

- [The Microbe Atlas](https://www.microbeatlas.org/taxon?taxon_id=90_10)


## Color resources

Colors in plots are really important to understanding the data.

- [R package called khroma](https://cran.r-project.org/web/packages/khroma/vignettes/khroma.html)
- [same colors if you code in python](https://tol-colors.readthedocs.io/en/latest/colorsets.html)
- [some more r colorblind palettes](https://siegal.bio.nyu.edu/color-palette/)
- [some info on color theory](https://jfly.uni-koeln.de/color/)
- [I want hue](https://medialab.github.io/iwanthue/)

### plotting with different colors

If I want to plot the cruises sampled by color, how many colors do I need?

``` r
(unique(samples$Season))
```

```
## [1] "Autumn" "Spring"
```


4 seasons = 4 colors



``` r
install.packages("khroma")
```


``` r
library(khroma)
# pick 4 colors
color_seasons=color("vibrant")
```


``` r
ggplot(samples, aes(x = lon, y = lat, fill = Season)) +
  geom_point(pch=21,size=4) +
  labs(x = "Longitude", y = "Latitude", color = "Season") +
  scale_fill_manual(values=color_seasons(4))
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-30-1.png)<!-- -->


look at this vignette for more colors

``` r
vignette("tol")
```

### making your own color palette 

say you don't like those colors and have specific in mind. you can do two things.


``` r
color_seasons = c("orange", 'forestgreen', 'red', 'navy')
ggplot(samples, aes(x = lon, y = lat, fill = Season)) +
  geom_point(pch=21,size=4) +
  labs(x = "Longitude", y = "Latitude", color = "Season") +
  scale_fill_manual(values=color_seasons)
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-32-1.png)<!-- -->

or you can manually assign colors to each season

``` r
color_seasons = c("Autumn" = 'orange', 
                  "Spring" = "forestgreen", 
                  "Summer" = 'red',
                  "Winter" = 'navy')
ggplot(samples, aes(x = lon, y = lat, fill = Season)) +
  geom_point(pch=21,size=4) +
  labs(x = "Longitude", y = "Latitude", color = "Season") +
  scale_fill_manual(values=color_seasons)
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-33-1.png)<!-- -->


## ggplot themes

you can make your plots look different by changing the "theme". Look at this [webpage](https://ggplot2.tidyverse.org/reference/ggtheme.html) for options.


``` r
ggplot(samples, aes(x = lon, y = lat, fill = Season)) +
  geom_point(pch=21,size=4) +
  labs(x = "Longitude", y = "Latitude", color = "Season") +
  scale_fill_manual(values=color_seasons) + 
  theme_classic()
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-34-1.png)<!-- -->

you can also increase the font all aorund using this theme option 

``` r
ggplot(samples, aes(x = lon, y = lat, fill = Season)) +
  geom_point(pch=21,size=4) +
  labs(x = "Longitude", y = "Latitude", color = "Season") +
  scale_fill_manual(values=color_seasons) + 
  theme_classic(base_size = 14)
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-35-1.png)<!-- -->
