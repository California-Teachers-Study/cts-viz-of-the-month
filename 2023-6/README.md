# June 2023 CTS Viz of the Month
Emma Spielfogel
2023-06-26

### Packages used

```r
library(tidyverse)
library(waffle)
```

### Description of inputs

* Data
    + A dataframe of number of individuals by age group (or other categorical data)

* Variables
    + Age group category names
    + Number of individuals in each age group

### Visualization code

```r
# Input data
age_groups_at_baseline <- c("Under 40" = 22121,	"40-49" = 33492,	"50-59" = 31784,
                            "60-69" = 23073, "70-79" = 15949,	"80-89" = 6378,	"90+" = 680)

# Create waffle chart
waffle(age_groups_at_baseline/500, rows=11, size=0.6,
       title="Age of CTS participants at baseline (1995)", 
       xlab="1 square = 500 participants")
```

##### Files in this folder:

- .png file: image of the viz of the month
- .Rmd file: the code used to create this document
- .html file: a downloadable version of this document
