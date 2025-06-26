---
layout: default
title: R processing for workshop
nav_order: 6
---
# Demo of how to subset and save data for GIS
We did other pre-processing for you that is not covered in the workshop but can be viewed on this page.

**Download the reformatted files on GitHub here.**

## Part 4: Combining the census and geography data in R

**This section is where the workshop starts.**

### Set up R by loading in packages and reading in the data

```r
## load packages
library(sf)
library(tidyverse)

## load data
mvrd_census = read.csv("MVRD_Census_Subset.csv")
mvrd_geography = st_read("MVRD_Geography_Subset.shp")
```

### Practice subsetting the data

To practice sub-setting the data, we will filter out the areas with data quality flags that indicate supressed data and/or areas with few responses and use that to subset the geography information

```r
## remove rows where the data quality flag is not 0 (no low quality or supression flag)
mvrd_high_quality = subset(mvrd_census, mvrd_census$DATA_QUALITY_FLAG==0)
```

#### Combine the census and geography data
We will join the data and while we do this it will remove the times where the DGUID is no longer in the mvrd_high_quality

```r
mvrd_dat_geo = left_join(mvrd_high_quality, mvrd_geography)
```


## Part 5: Format for export from R and import GIS

```r
## save file as geojason
sf::st_write(mvrd_dat_geo, dsn = "mvrd_census_for_GIS.geojson", layer = "mvrd.geojson")
```
