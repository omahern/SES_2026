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
amplicon <- read_csv("/Users/oliviaahern/Downloads/amplicon-plotting/data/amplicon_data.csv")
```

You should now see `amplicon` in the **Environment** panel (top right).

### Look at the data

The first 6 rows:


``` r
head(amplicon)
```

```
## # A tibble: 6 × 59
##   time                  lat   lon depth cruise  SampleID Domain   Supergroup
##   <dttm>              <dbl> <dbl> <dbl> <chr>   <chr>    <chr>    <chr>     
## 1 2003-08-22 06:03:00  70.4 -163.     0 POTATOE P-1      Bacteria <NA>      
## 2 2003-08-22 06:03:00  70.4 -163.     0 POTATOE P-1      Bacteria <NA>      
## 3 2003-08-22 06:03:00  70.4 -163.     0 POTATOE P-1      Bacteria <NA>      
## 4 2003-08-22 06:03:00  70.4 -163.     0 POTATOE P-1      Bacteria <NA>      
## 5 2003-08-22 06:03:00  70.4 -163.     0 POTATOE P-1      Bacteria <NA>      
## 6 2003-08-22 06:03:00  70.4 -163.     0 POTATOE P-1      Bacteria <NA>      
## # ℹ 51 more variables: Division <chr>, Phylum <chr>, Class <chr>, Order <chr>,
## #   Family <chr>, Genus <chr>, Species <chr>, ProPortal_ASV_Ecotype <chr>,
## #   Sequence_Type <chr>, ASV <chr>, ASV_hash <chr>, Level_1 <chr>,
## #   Level_1_1 <chr>, Level_2 <chr>, Eco_relevant_plank_groups <chr>,
## #   Raw_Sequence_Counts <dbl>, Corrected_dada2_Sequence_Counts <dbl>,
## #   Corrected_Sequence_Counts <dbl>, Relative_Abundance <dbl>,
## #   Source_database <chr>, Cruise_ID <chr>, Station <chr>, Date <chr>, …
```

How many rows and columns?


``` r
nrow(amplicon)
```

```
## [1] 1273418
```

``` r
ncol(amplicon)
```

```
## [1] 59
```

The column names:


``` r
names(amplicon)
```

```
##  [1] "time"                            "lat"                            
##  [3] "lon"                             "depth"                          
##  [5] "cruise"                          "SampleID"                       
##  [7] "Domain"                          "Supergroup"                     
##  [9] "Division"                        "Phylum"                         
## [11] "Class"                           "Order"                          
## [13] "Family"                          "Genus"                          
## [15] "Species"                         "ProPortal_ASV_Ecotype"          
## [17] "Sequence_Type"                   "ASV"                            
## [19] "ASV_hash"                        "Level_1"                        
## [21] "Level_1_1"                       "Level_2"                        
## [23] "Eco_relevant_plank_groups"       "Raw_Sequence_Counts"            
## [25] "Corrected_dada2_Sequence_Counts" "Corrected_Sequence_Counts"      
## [27] "Relative_Abundance"              "Source_database"                
## [29] "Cruise_ID"                       "Station"                        
## [31] "Date"                            "Day"                            
## [33] "Month"                           "Year"                           
## [35] "Time_Sampled"                    "Latitude"                       
## [37] "Longitude"                       "Bottom_Depth"                   
## [39] "Temperature"                     "Salinity"                       
## [41] "Oxygen"                          "Silicate"                       
## [43] "NO2"                             "NO3"                            
## [45] "NH3"                             "PO4"                            
## [47] "DOC"                             "TOC"                            
## [49] "PAR"                             "Chlorophyll"                    
## [51] "DCM"                             "Season"                         
## [53] "Longhurst_Short"                 "Longhurst_Long"                 
## [55] "Predicted_Euphotic_Depth"        "Ocean_Basin"                    
## [57] "Depth_Category"                  "Sequencing_platform"            
## [59] "Extraction_Method"
```

Use `$` to grab one column. `unique()` shows each value once:


``` r
unique(amplicon$cruise)
```

```
##  [1] "POTATOE"                    "ZHNG02RR"                  
##  [3] "CLIVAR P16N Leg-1"          "CLIVAR IO9N"               
##  [5] "JC039"                      "PE319, PE321, JC057, PE358"
##  [7] "JC053"                      "D357,JC068"                
##  [9] "KN204, KN199"               "SS2011, TAN1109"           
## [11] "FRAM"                       "AU1603"                    
## [13] "Glpr05"                     "IN2016_V03"                
## [15] "IND2017_V01"                "MGL1704"                   
## [17] "KM1906"
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
## # A tibble: 1,105 × 8
##    SampleID cruise    lat   lon depth Temperature Salinity Season
##    <chr>    <chr>   <dbl> <dbl> <dbl>       <dbl>    <dbl> <chr> 
##  1 P-1      POTATOE  70.4 -163.     0        6.61     31.2 Summer
##  2 P-2      POTATOE  66.8 -168.     0        7.71     31.0 Summer
##  3 P-4      POTATOE  58.8 -168.     0        9.39     31.5 Summer
##  4 P-5      POTATOE  54.9 -167.     0        9.11     32.6 Summer
##  5 P-6      POTATOE  51.7 -166.     0       12        32.5 Summer
##  6 P-7      POTATOE  47.6 -167.     0       12.2      32.6 Autumn
##  7 P-8      POTATOE  43.9 -167.     0       14.7      32.8 Autumn
##  8 P-9      POTATOE  39.7 -165.     0       19.3      33.4 Autumn
##  9 P-10     POTATOE  35.9 -164.     0       24.3      34.3 Autumn
## 10 P-11     POTATOE  31.8 -163.     0       25.8      34.7 Autumn
## # ℹ 1,095 more rows
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
  facet_wrap (~Ocean_Basin, scales='free') +
  theme(legend.position = 'bottom')
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-14-1.png)<!-- -->

turn off legend for large datasets


`facet_grid()` makes a separate panel for each sequence type.
change the legend position

``` r
ggplot(amplicon, aes(x = SampleID, y = Relative_Abundance, fill = Domain)) +
  geom_col() +
  labs(x = "Sample", y = "Relative abundance") + 
  facet_grid (~Ocean_Basin, scales='free') +
  theme(legend.position = 'none')
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-15-1.png)<!-- -->



**Questions:**

1. Which Domain is most abundant?
2. Do all the samples look the same?

### Just one cruise

`filter()` keeps only the rows that match a condition. `==` means "is equal to."


``` r
one_cruise <- filter(amplicon, cruise == "POTATOE")
```

Now make the same stacked bar plot with only that cruise:


``` r
ggplot(one_cruise, aes(x = SampleID, y = Relative_Abundance, fill = Domain)) +
  geom_col() +
  facet_wrap(~ Sequence_Type) +
  labs(x = "Sample", y = "Relative abundance", title = "POTATOE cruise")
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-17-1.png)<!-- -->

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


``` r
species_per_sample <- amplicon %>%
  group_by(SampleID) %>%
  summarise(n_species = n_distinct(Species, na.rm = TRUE))

species_per_sample
```

```
## # A tibble: 1,105 × 2
##    SampleID                              n_species
##    <chr>                                     <int>
##  1 3335-FRAM-bactV4V5-EGC-3-1plus2-S1           69
##  2 3336-FRAM-bactV4V5-EGC-3-3plus4-S2           93
##  3 3337-FRAM-bactV4V5-EGC-3-5plus6-S3           59
##  4 3338-FRAM-bactV4V5-EGC-3-7plus8-S4           66
##  5 3339-FRAM-bactV4V5-EGC-3-9plus10-S5          84
##  6 3340-FRAM-bactV4V5-EGC-3-11plus12-S6         68
##  7 3341-FRAM-bactV4V5-EGC-3-13plus14-S7         99
##  8 3342-FRAM-bactV4V5-EGC-3-15plus16-S8         78
##  9 3343-FRAM-bactV4V5-EGC-3-17plus18-S9         86
## 10 3344-FRAM-bactV4V5-EGC-3-19plus20-S10        91
## # ℹ 1,095 more rows
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

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-22-1.png)<!-- -->


and we can again facet it, lets try with season


``` r
ggplot(amplicon, aes(x = SampleID, y = n_species)) +
  geom_col() +
  labs(x = "SampleID", y = "Number of ASVs") +
  facet_wrap (~Season, scales='free')
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-23-1.png)<!-- -->

#### plotting against environmental factors



``` r
ggplot(amplicon, aes(x = Temperature, y = n_species)) +
  geom_point() +
  labs(x = "Temperature (°C)", y = "Number of ASVs")
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-24-1.png)<!-- -->


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

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-25-1.png)<!-- -->


We can also look at how the number of species changes with DOC, or dissolved organic carbon.


``` r
ggplot(amplicon, aes(x = DOC, y = n_species)) +
  geom_point(pch=21, size=4) +
  labs(x = "Dissolved organic carbon", y = "Number of ASVs") +
  theme(legend.position ='bottom') 
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-26-1.png)<!-- -->



We can also look at how the number of species changes with light or PAR


``` r
ggplot(amplicon, aes(x = PAR, y = n_species)) +
  geom_point(pch=21, size=4) +
  labs(x = "PAR", y = "Number of ASVs") +
  theme(legend.position ='bottom') 
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-27-1.png)<!-- -->


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
## [1] "Summer" "Autumn" "Spring" "Winter"
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

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-31-1.png)<!-- -->


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

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-33-1.png)<!-- -->

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

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-34-1.png)<!-- -->


## ggplot themes

you can make your plots look different by changing the "theme". Look at this [webpage](https://ggplot2.tidyverse.org/reference/ggtheme.html) for options.


``` r
ggplot(samples, aes(x = lon, y = lat, fill = Season)) +
  geom_point(pch=21,size=4) +
  labs(x = "Longitude", y = "Latitude", color = "Season") +
  scale_fill_manual(values=color_seasons) + 
  theme_classic()
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-35-1.png)<!-- -->

you can also increase the font all aorund using this theme option 

``` r
ggplot(samples, aes(x = lon, y = lat, fill = Season)) +
  geom_point(pch=21,size=4) +
  labs(x = "Longitude", y = "Latitude", color = "Season") +
  scale_fill_manual(values=color_seasons) + 
  theme_classic(base_size = 14)
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-36-1.png)<!-- -->
