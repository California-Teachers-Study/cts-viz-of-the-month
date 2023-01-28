January 2023 CTS Viz of the Month
================
Emma Spielfogel
2023-01-28

### Packages used

``` r
library(tidyverse)
```

### Description of inputs

- Data
  - A data frame called “publications” where each unique combination of
    publication ID & publication topic area is a row
- Variables
  - year_of_publication: year publication was published
  - topic_area: the topic area of the publication; may be more than 1
    per publication

### Visualization code

``` r
publications %>%
  group_by(topic_area,year_of_publication) %>%
  summarise(counts_topic_in_year=n()) %>% 
  ungroup() %>%
  ggplot(aes(y = reorder(topic_area,counts_topic_in_year),
             x = year_of_publication,
             group=topic_area,
             fill=counts_topic_in_year)) +
  geom_tile(color="white") +
  scale_fill_gradient2(low = "#7fbf7b", 
                       high = "#af8dc3", 
                       mid = "#eff3ff", 
                       midpoint = 8, 
                       limit = c(0,17),
                       name="Number of\nPublications") +
  theme_minimal() +
  theme(text= element_text(family="Arial"),
        plot.subtitle = element_markdown(size=10),
        panel.grid.major = element_blank(),
        panel.grid.minor = element_blank(),
        panel.border = element_blank(),
        panel.background = element_blank(),
        axis.ticks = element_blank(),
        plot.caption = element_markdown(size=8, color="gray",hjust=-.3)) +
  labs(title = "CTS Publication Topic Areas Over Time",
       subtitle = str_glue("<span style = 'color: #af8dc3;'>
                           Breast cancer and genetics are the topics most commonly published on.</span>"),
       caption = "Emma Spielfogel",
       x = "\nYear of Publication",
       y = "")
```
