# February 2023 CTS Viz of the Month
Danielle Medgyesi 
2023-02-07


### Packages used

```r
library(tidyverse)
library(ggplot2)
library(sf)
library(viridis)
library(Hmisc)
```
### Description of inputs

* Data
  + CTS dataframe of County FIPS codes for 124,685 participants living in California at baseline. *Must request access to the data, which can be stored and viewed only in the CTS Data Commons.*
  + Shapefile of California county boundaries, Census 1990. *See subfolder "county_boundaries".*
  
* Variables
  + CensusCountyFips: county FIP code of participant's address at baseline
  + N: Number of participants living in each county 


### Visualization code

```r

# number of participants categorized 
county_pop_sf<- county_pop_sf %>% 
  mutate(N_cat= cut2(N, cuts = c(100,300,500, 700, 1500, 3000, 7000, 10000)))

ggplot(county_pop_sf)+geom_sf(mapping=aes(fill=N_cat), color=NA)+ 
  scale_fill_viridis(discrete = TRUE, option = "plasma", direction = -1, 
                     labels=c("<100", "100-<300", "300-<500", "500-<700", "700-<1500",
                              "1500-<3000", "3000-<7000", ">10000"), 
                     name="Participants")+
  theme(axis.text = element_blank(), 
        axis.title =element_blank(),
        plot.title = element_text(hjust = 0.5),
        plot.subtitle = element_text(hjust = 0.5),
        axis.ticks=element_blank(), 
        panel.grid.major = element_blank(), 
        panel.grid.minor = element_blank(), 
        panel.background = element_blank(), 
        plot.caption = element_text(hjust = 0), 
        legend.position = c(0.9,0.67), 
        legend.background = element_blank())+
  labs(title="CTS county population at baseline (1995-1996)", 
       caption = "Total participants= 124,685\n\nDesign: Danielle Medgyesi | dm3688@cumc.columbia.edu")

```

##### Files in this folder:

- .png file: image of the viz of the month
- .Rmd file: the code used to create this document
- .html file: a downloadable version of this document
