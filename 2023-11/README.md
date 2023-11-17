# November 2023 CTS Viz of the Month
Kristen Savage
2023-11-01

### Packages used

```r
library(tidyverse)
library(dplyr)
library(ggplot2)
```

### Description of inputs

* Data
    + A dataframe called "deceased" where each row is the unique combination of cause of death, count of deaths from that cause, and cause of death group.

* Variables
    + cause_of_death: the cause of death
    + count: sum of participants who died from that cause of death
    + group: the type of cause of death; this variable determines which causes of death are grouped together in the viz

### Visualization code

```r
# Load data to a dataframe called "deceased"

# Data preparation code: store group as a factor
deceased <- deceased %>% 
  mutate(group = as.factor(group))

# Add an empty bar below each grouping to create spacing in the visualization. For dataframes with more
#   rows (40+), you may want to add additional empty bars to emphasize spacing between groups. Because
#   this viz only has 28 rows, we used empty_bar <- 1. 
empty_bar <- 1
to_add <- data.frame( matrix(NA, empty_bar * nlevels(deceased$group), ncol(deceased)) )
colnames(to_add) <- colnames(deceased)
to_add$group <- rep(levels(deceased$group), each=empty_bar)
deceased <- rbind(deceased, to_add)
deceased <- deceased %>% arrange(group, count)
deceased$id <- seq(1, nrow(deceased))

# Create data labels and calculate the appropriate angle for each label
label_data <- deceased
number_of_bar <-nrow(label_data)
angle <- 90 - 360 * (label_data$id-0.5) /number_of_bar
label_data$hjust<-ifelse(angle < -90, 1, 0)
label_data$angle<-ifelse(angle < - 90, angle+180, angle)

# Visualization code to create circular barchart
deceased %>% 
  ggplot(aes(x=as.factor(id), y=count, fill=group)) +
  geom_bar(stat="identity", alpha=0.5)+
  ylim (-800,4700)+
  theme_minimal()+
  theme(
    plot.title = element_text(family = "Helvetica", face = "bold", size = (12)),
    axis.text = element_blank(),
    axis.title = element_blank(),
    panel.grid = element_blank(),
    plot.margin = unit(c(0, 0, 0, 0), 
                                "inches"),
    legend.position = "right",
    legend.direction = "vertical"
    
  ) +
  coord_polar()+
  geom_text(data=label_data, 
            aes(x=id, y=count+20, label=cause_of_death,
                hjust=hjust), color="black",
            fontface="bold", alpha=0.4, size=2.5,
            angle=label_data$angle, inherit.aes = FALSE) +
  scale_fill_manual(values = c("#251101", "#470024","#5B1865","#443875","#2C5784","#5688C7"),
                    name = "Type")+
   ggtitle("Common Causes of Death in the California Teachers Study")+
  theme(plot.title = element_text(vjust = -6)) 
```

##### Files in this folder:

- .png file: image of the viz of the month
- .Rmd file: the code used to create this document
- .html file: a downloadable version of this document
