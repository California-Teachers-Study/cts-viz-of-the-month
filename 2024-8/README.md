# August 2024 CTS Viz of the Month
Kristen Savage
2024-08-08

### Packages used

```r
library(tidyverse)
library(ggplot2)
library(ggbeeswarm)
```

### Description of inputs

* Data
    + A data frame called "age_df" where each row represents a study participant and captures their age at baseline, age at first full term pregnancy, and whether they ever used fertility drugs

* Variables
    + age_at_baseline: the age the participant was when they joined the study, stored as a number
    + fullterm_age1st: the age the participant was when they had their first full term pregnancy, stored as a number
    + ever_used_fertdrug: a yes/no variable that captures whether or not the participant reported ever having used a fertility drug to try to become pregnant

### Visualization code

```r
# create groupings to use in viz
age_df <- age_df %>% 
  mutate(age_1st_preg_grp = case_when(fullterm_age1st < 20 ~ "<20",
                                      fullterm_age1st >= 20 & fullterm_age1st <=24 ~ "20 - 24",
                                      fullterm_age1st >= 25 & fullterm_age1st <=29 ~ "25 - 29",
                                      fullterm_age1st >= 30 & fullterm_age1st <=34 ~ "30 - 34",
                                      fullterm_age1st >= 35 & fullterm_age1st <=39 ~ "35 - 39",
                                      fullterm_age1st >= 40 & fullterm_age1st <=44 ~ "40 - 44",
                                      fullterm_age1st >= 45 ~ "45+"))

# create viz
age_df %>% 
  ggplot(aes(x = age_1st_preg_grp, y = age_at_baseline, color = factor(ever_used_fertdrug))) + 
  geom_quasirandom(width = 0.3, varwidth = TRUE) +
  scale_color_brewer(palette = "PuBu")+
  theme_minimal()+
  theme(axis.title=element_text(size = 11, face = "bold"),
        plot.title = element_text(size = 14, face = "bold"),
        legend.title = element_text(size = 11, face = "bold")) +
  guides(colour = guide_legend(override.aes = list(size = 4)))+
  ggtitle("Age at Baseline by Age at First Full Term Pregnancy")+
  xlab("How old were you when you had your first full term pregnancy?")+
  ylab("Age at Study Enrollment")+
  labs(colour = "Ever Used Fertility Drug")
```

##### Files in this folder:

- .png file: image of the viz of the month
- .Rmd file: the code used to create this document
- .html file: a downloadable version of this document
