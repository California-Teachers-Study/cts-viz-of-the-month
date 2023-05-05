# May 2023 CTS Viz of the Month
Kristen Savage
2023-05-05

### Packages used

```r
library(tidyverse)
library(gt)
library(gtExtras)
library(webshot2)
library(scales)
```

### Description of inputs

* Data
    + A data frame called "cancer_by_stage" where the row is the cancer group (as defined by seercode) and the columns represent the cancer stage at diagnosis

* Variables
    + cancer_group: the cancer type/group associated with the SEER code or cancer grouping in your data
    + in_situ: the percent that were diagnosed as "In situ" for each cancer group; this is a row percentage
    + stage_1: the percent that were diagnosed as "Stage 1" for each cancer group; this is a row percentage
    + stage_2: the percent that were diagnosed as "Stage 2" for each cancer group; this is a row percentage
    + stage_3: the percent that were diagnosed as "Stage 3" for each cancer group; this is a row percentage
    + stage_4: the percent that were diagnosed as "Stage 4" for each cancer group; this is a row percentage
    

### Visualization code

```r
# create color palette
min_pct <- 0
max_pct <- 100
staging_palette <- col_numeric(c("#DEF7F2", "#1C7362"),
                                domain = c(min_pct, max_pct),
                                alpha = 0.25)

# create visualization
cancer_by_stage  %>% 
  filter(stage 4 < 100) %>% 
  gt(rowname_col = "cancer_group") %>% 
  cols_label(
    cancer_group = "Cancer",
      in_situ = "In situ",
      stage_1 = "Stage 1",
      stage_2 = "Stage 2",
      stage_3 = "Stage 3",
      stage_4 = "Stage 4") %>% 
  fmt_pct_extra(
    columns = c(in_situ, stage_1, stage_2, stage_3, stage_4), 
    decimals = 0,
    suffixing = TRUE) %>% 
  cols_align(align = "center") %>% 
  cols_align(align = "left",
             columns = "cancer_group")  %>%
gt_theme_nytimes() %>% 
  tab_header(
    title = "Stage at Diagnosis by Cancer Group",
    subtitle = "Did the stage at which CTS participants were diagnosed vary by cancer?") %>% 
  tab_source_note(source_note = "Note: This chart highlights only those cancers for which 
                  staging data was available. Cancers diagnosed as stage 'unknown' are excluded.") %>% 
  tab_stubhead(label = "Cancer Type") %>% 
  tab_style(locations = cells_title(groups = 'title'),
                style = list (
                  cell_text(
                    font = google_font (name = 'Bebas Neue'),
                    size='xx-large',
                    color = '#3D5F66'
                  )
                )) %>%
  tab_style(style = cell_text(weight = "bold", size = 'large',
                               font = google_font (name = 'Bebas Neue')),
              locations = cells_stubhead()) %>% 
  data_color(rows = ,
             fn = staging_palette) 
```

##### Files in this folder:

- .png file: image of the viz of the month
- .Rmd file: the code used to create this document
- .html file: a downloadable version of this document
