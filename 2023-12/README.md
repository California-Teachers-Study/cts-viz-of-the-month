# December 2023 CTS Viz of the Month
Kristen Savage
2023-12-15

### Packages used

```r
library(tidyverse)
library(igraph)
library(arcdiagram)
```

### Description of inputs

Two data frames, one which contains data at the visit level and another which contains data at the participant level. 

These dataframes were constructed using hospitalization data. Conditions for this vz were captured using CCS codes and categories. For this viz, we have excluded the CCS category "Ill Defined Conditions". 

* Visits Data
    + A dataframe called "visits" where each row captures a unique combination of two health conditions and the count of how often those two conditions were diagnosed during the same hospital visit. 

* Visits Variables
    + source: a health condition category from CCS
    + target: another health condition category from CCS that was diagnosed concurrently
    + value: sum of how often the source and target conditions were diagnosed together
    
* Ppts Data
    + A dataframe called "ppts" where each row captures a unique combination of two health conditions and the count of how often those two conditions were ever diagnosed for a participant over the course of study follow-up. 

* Ppts Variables
    + source: a health condition category from CCS 
    + target: another health condition category from CCS that was diagnosed concurrently
    + value: sum of how often the source and target conditions were diagnosed together at the ppt level (e.g., ever diagnosed with both conditions)

### Visualization code

```r
# load your visit level data into a dataframe called "visits"
# load your participant level data into a dataframe called "ppts"

# transform each dataframe into a graph

mygraphvisits <- graph_from_data_frame(visits)

mygraphppts <- graph_from_data_frame(ppts)


# get edgelist for each dataframe

edgelistvisits = get.edgelist(mygraphvisits)

edgelistppts = get.edgelist(mygraphppts)

# get correspondending values for each dataframe 

visitvalues = igraph:::get.edge.attribute(mygraphvisits, "value")

pptvalues = igraph:::get.edge.attribute(mygraphppts, "value")

# set order of conditions on the x axis

order <- c(
          "Circulatory system",
          "Congenital abnormalities",
          "Digestive system",
          "Diseases of the blood",
          "Endocrine & Metabolic",
          "Eye disorders",
          "Genitourinary system",
          "Hereditary",
          "Infection and Parasitic",
          "Injury and Poisoning",
          "Mental illness",
          "Musculosckeletal",
          "Neoplasms",
          "Nervous system",
          "Pregnancy & childbirth",
          "Perinatal conditions",
          "Respiratory system",
          "Skin & subcutaneous"
          )

# plot visit level data 
arcplot(edgelistvisits,
ordering = order,
show.nodes=TRUE, 
        cex.nodes = 1, pch.nodes=21,
        bg.nodes = "#2c4c7b",
        lwd.nodes = 2, 
        line=0,
        col.nodes = "#2c4c7b",
        col.arcs = "#5998ff77",
        lwd.arcs = .0001 * visitvalues,
        lty.arcs = 1,
  show.labels = TRUE, col.labels = "gray50",
  cex.labels = 0.55) %>% 
    title(main = "Comorbidities diagnosed during hospital visits", 
        cex.main = 1, 
        font.main = 2, 
        col.main = "#2c4c7b",
        adj = 0)

# plot participant level data
arcplot(edgelistppts, ordering = order,
                show.nodes=TRUE,
                cex.nodes = 1, 
                pch.nodes=21,
                bg.nodes = "#163A3D",
                lwd.nodes = 1.8, 
        lty.arcs = 1,
        lend = 1,
        line=0,
        col.nodes = "#163A3D",
        col.arcs = "#2C7477BB",
        lwd.arcs = .0001 * pptvalues,
        show.labels = TRUE, 
        col.labels = "gray50",
  cex.labels = 0.55) %>% 
  title(main = "Comorbidities diagnosed in CTS participants", 
        cex.main = 1, 
        font.main = 2, 
        col.main = "#163A3D",
        adj = 0) 
```

##### Files in this folder:

- .png file: image of the viz of the month
- .Rmd file: the code used to create this document
- .html file: a downloadable version of this document
