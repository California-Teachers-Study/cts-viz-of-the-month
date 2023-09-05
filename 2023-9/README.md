# September 2023 CTS Viz of the Month
Emma Spielfogel
2023-09-05

### Packages used

### Packages used

```r
library(readxl)
library(tidyverse)
library(hrbrthemes)
library(wesanderson)

# Optional steps if DM Sans font used in chart is not in your font library
library(showtext)
font_add_google("DM Sans")
showtext_auto(enable=TRUE)
```

### Description of inputs

* Data
    + A data frame called "response_age" containing the number of participant responses to each study questionnaire split into age groups

* Variables
    + questionnaire: a text column containing the questionnaire name, number and date range
    + age_cat: a numeric column (range: 1-7) representing the age category of questionnaire respondents at the time of questionnaire completion
    + age_group: a text label (e.g. "Under 40", "90+") corresponding to the age_cat
    + n: the number of participants

### Visualization code

```r
# Set color palette using one of the wesanderson package palettes
pal <- wes_palette("Zissou1",6, type="continuous")

# Create line chart
response_age %>% 
  ggplot(aes(x=fct_reorder(age_group,age_cat),
             y=n,
             group=questionnaire)) +
  geom_line(size = 1, aes(color=questionnaire,
                          linetype=questionnaire)) +
  geom_point(size = 3.5,
             aes(color = questionnaire,
                 shape=questionnaire)) +
  scale_color_manual(values = pal) +
  scale_linetype_manual(values = c("solid","solid","solid","solid","solid","solid")) +
  theme_linedraw(base_family = "DM Sans") +
  theme(panel.grid = element_blank(),
        legend.title = element_blank()) +
  labs(title = "CTS Questionnaire Responses by Age",
       x = "\nAge at questionnaire",
       y = "Number of participants\n")
```

##### Files in this folder:

- .png file: image of the viz of the month
- .Rmd file: the code used to create this document
- .html file: a downloadable version of this document
