---
layout: default
title: Pre-processing in R (not covered in workshop)
nav_order: 5
---
# Pre-processing in R (not covered in workshop)

Note, there are ways to do <a href="https://mountainmath.github.io/cancensus/" target="_blank">the entire analysis in R</a>, but for this workshop we are explicitly using GIS because it is preferred. 
GIS allows more intuitive customization and allows you to work with any compatible dataset with comparative ease. 

## Part 1: Download the data

<em>The file for the associated workshop is the output of this section. 
We provide a subset of the total data to accommodate various computer capacities and internet download speeds for the workshop. These steps will be required when you conduct your own research.</em>

### Spatial data

Go to the <a href="https://www12.statcan.gc.ca/census-recensement/2021/geo/sip-pis/boundary-limites/index2021-eng.cfm?year=21" target="_blank">Statistics Canada website</a>.

Select the Type "Cartographic Boundary Files (CBF)" -\> Administrative Boundary "Census Divisions" -\> Format "Shapefile (.shp)"

This file is very large and will download as a zip file. This file needs to be unzipped before reading into R.

### Census data

<em>Note: if you are UBC affiliated, you may be able to use <a href="https://resources.library.ubc.ca/page.php?id=521" target="_blank">this link</a> to download only the census data you are interested in. This will remove the need for a lot of the subsetting that occurs early in the tutorial.</em>

Go to <a href="https://www12.statcan.gc.ca/census-recensement/2021/dp-pd/prof/index.cfm?Lang=E" target="_blank">data download page</a> and click the "Download Data" button

There are two main download options: with and without confidence intervals. select the without confidence intervals option

Select the British Columbia .csv file to download. This will download a zip file on your computer.

Unzip the file. You will find a:

1.  README file. Read this file to know what the other files are.

2.  A metadata file.

3.  Two csv files. One with the census data and one with the geographic codes.

You will notice if you look closely that none of the columns or column contents actually match perfectly between the files. This is a problem that we will return to later.

For now, we will read in the csv files and subset them to make the workshop files we will use for the rest of the time. We will do the additional formatting and combining of data together in a interactive manner.

## Part 2: Format the downloaded data in R

```r
## load in required packages for pre-processing
library(tidyverse)
library(sf)

## read in the two csv files into R
census = read.csv("98-401-X2021025_English_CSV_data.csv")

## only keep the part of the data we actually want for the workshop today (Names with Vancouver in it)

## subsetting the data can be done may ways. Here, we are using the associated codes with each census subdivision of the Metro Vancouver Regional District. We will keep all codes in the list.

## While subsetting the regions, we will also only keep the variables in the CHARACTERISTIC_NAME column we are interested in
mvrd_census = subset(census, 
                     census$ALT_GEO_CODE %in% c("5915038", "5915036", "5915062", "5915025", "5915034", "5915039", "5915804", "5915805", "5915011", "5909856", "5915001", "5915002", "5915065", "5915075", "5915020", "5915022", "5915046", "5915051", "5915055", "5915029", "5915070", "5915039", "5915043", "5915015", "5915004", "5915802", "5915007") &
                       census$CHARACTERISTIC_NAME %in% c("Population, 2021", "  Average after-tax income in 2020 ($)", "    Average COVID-19 emergency and recovery benefits in 2020 among recipients ($)"))

## lastly, let's only keep the columns we need for the workshop
mvrd_census = mvrd_census |> select(DGUID, GEO_NAME, DATA_QUALITY_FLAG, C1_COUNT_TOTAL, CHARACTERISTIC_NAME)

```

#### A note on data quality flags

Can find the [full list online](https://www12.statcan.gc.ca/census-recensement/2021/ref/98-26-0006/982600062021001-eng.cfm)

Langley 2, Indian reserve (IRI): 10

-   Indicates "Data quality index showing a short-form total non-response rate higher than or equal to 10%, but lower than 20%."

Coquitlam 1, Indian reserve (IRI): 2929

-   Data quality index showing a short-form total non-response rate higher than or equal to 20%, but lower than 30%.

-   Data suppressed to meet the confidentiality requirements of the *Statistics Act* 

Coquitlam 2, Indian reserve (IRI): 9999

-   Data suppressed to meet the confidentiality requirements of the *Statistics Act*

### Read in the geography information

```r
all_can_geo = st_read("lcsd000b21a_e.shp")
```

We can use the cleaned up census data to subset the geography information

```r
mvrd_geo = subset(all_can_geo, all_can_geo$DGUID %in% c(mvrd_census$DGUID))
```

## Part 3: Save the workshop files.

```r
write.csv(mvrd_census, "MVRD_Census_Subset.csv", row.names = F)
st_write(mvrd_geo, "MVRD_Geography_Subset.shp")
```

**These are the files the workshop is run with.**



