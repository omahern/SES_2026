---
title: "R notebook"
output: 
  html_document:
    keep_md: true
    toc: true
    toc_float: true
    toc_depth: 4
    code_folding: hide
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

### Color resources

Colors in plots are really important to understanding the data.

- [R package called khroma](https://cran.r-project.org/web/packages/khroma/vignettes/khroma.html)
- [same colors if you code in python](https://tol-colors.readthedocs.io/en/latest/colorsets.html)
- [some more r colorblind palettes](https://siegal.bio.nyu.edu/color-palette/)
- [some info on color theory](https://jfly.uni-koeln.de/color/)


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

`facet_wrap()` makes a separate panel for each sequence type.


``` r
ggplot(amplicon, aes(x = SampleID, y = Relative_Abundance, fill = Phylum)) +
  geom_col() +
  facet_wrap(~ Sequence_Type) +
  labs(x = "Sample", y = "Relative abundance")
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

**Questions:**

1. Which phylum is most abundant?
2. Do all the samples look the same?

### Just one cruise

`filter()` keeps only the rows that match a condition. `==` means "is equal to."


``` r
one_cruise <- filter(amplicon, cruise == "POTATOE")
```

Now make the same stacked bar plot with only that cruise:


``` r
ggplot(one_cruise, aes(x = SampleID, y = Relative_Abundance, fill = Phylum)) +
  geom_col() +
  facet_wrap(~ Sequence_Type) +
  labs(x = "Sample", y = "Relative abundance", title = "POTATOE cruise")
```

![](/Users/oliviaahern/Documents/GitHub/SES_2026/docs/index_files/figure-html/unnamed-chunk-14-1.png)<!-- -->

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

**3.** Remake the bar plot from section 8, but fill by `Class` instead of `Phylum`.


``` r
ggplot(amplicon, aes(x = SampleID, y = Relative_Abundance, fill = ___)) +
  geom_col() +
  facet_wrap(~ Sequence_Type) +
  labs(x = "Sample", y = "Relative abundance")
```

```
## Error in parse(text = input): <text>:1:68: unexpected input
## 1: ggplot(amplicon, aes(x = SampleID, y = Relative_Abundance, fill = __
##                                                                        ^
```


# Misc

Other cool microbiome resources include:

- [The Microbe Atlas](https://www.microbeatlas.org/taxon?taxon_id=90_10)
