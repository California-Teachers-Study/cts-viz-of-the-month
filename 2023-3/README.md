# March 2023 CTS Viz of the Month
Emma Spielfogel
2023-03-10

### Packages used

```r
library(tidyverse)
library(readxl)
library(janitor)
library(lubridate)
library(ggalt)
library(ggtext)
library(scales)
library(patchwork)
library(stringr)
```

### Description of inputs

* Data
    + A data frame with cause and date of death information where each row is an individual

* Variables
    + cod: ICD code for cause of death
    + other_significant_conditions: other significant conditions listed at the time of death
    + covid_indication: indicator for if COVID was the primary cause of death or mentioned as a significant condition at death
      + coding: cod = U071; other_significant_conditions text contains COVID
    + primary_cod_covid: yes/no indicator for if COVID was the primary cause of death
      + coding: cod = U071
    + yod: year of death

### Visualization code

```r
# Prepare data for dumbbell chart
deaths_grouped_years_p <- code_deaths_in %>% 
  group_by(yod,covid_indication) %>% 
  summarize(count = n()) %>%
  mutate(percent_of_total_deaths = count/sum(count)) %>% 
  ungroup() %>%
  pivot_wider(id_cols = c(covid_indication),
              names_from = yod,
              values_from = percent_of_total_deaths)

# Create dumbbell chart with percents
dumbbell_percent <- deaths_grouped_years_p %>% 
  mutate(growth = `2021` - `2020`) %>%
  select(covid_indication,`2020`,`2021`,growth) %>%
  filter(covid_indication != "COVID not mentioned at the time of death") %>% 
  mutate(percent_display = percent(growth, accuracy = .01),
         display_2020 = percent(`2020`, accuracy = .01),
         display_2021 = percent(`2021`, accuracy = .01)) %>% 
  ggplot(aes(x = `2020`,
             xend = `2021`,
             y = str_wrap(covid_indication, width=20))) +
  geom_dumbbell(colour_x = start_color,
                colour = "#d9d9d9",
                size = 3,
                colour_xend = end_color,
                size_x = 3,
                size_xend = 3) +
  geom_text(aes(x=`2021`,
                label=display_2021),
            fontface="bold",
            size = 3,
            nudge_x = .004,
            nudge_y = 0,
            color = end_color) +
  geom_text(aes(x=`2020`,
                label=display_2020),
            fontface="bold",
            size = 3,
            nudge_x = -.004,
            nudge_y = 0,
            color = start_color) +
  annotate("text",
           x = 0.058,
           y = 1.82,
           hjust = 1,
           color = "#4e9c95",
           family = "Arial",
           fontface="italic",
           label = "COVID-19 accounted for \n5.59% of all deaths in the \nCTS population in 2021",
           size = 2.5) +
  scale_x_continuous(labels = scales::percent_format(scale=100)) +
  theme_minimal() +
  theme(text= element_text(family="Arial"),
        plot.title = element_markdown(),
        axis.title.x = element_markdown(color="#525252", margin = unit(c(8), "pt")),
        panel.grid.major = element_line(color="#f7f7f7"),
        panel.grid.minor = element_blank(),
        plot.caption = element_markdown(size=8, color="gray",hjust=0)) +
  labs(title=str_glue("Percent of all deaths in the CTS population mentioning <br>COVID-19 in 
                      <span style = 'color: {start_color};'><b>2020</span></b> and
                      <span style = 'color: {end_color};'><b>2021</span></b>"),
       y="",
       x="Percent of deaths",
       caption = "Emma Spielfogel")

# Calculate total deaths from any cause to use as a label on the bar chart
summary_year <-code_deaths_in %>% 
  mutate(year=as.character(yod)) %>% 
  group_by(year) %>% 
  summarize(count=n())

# Create bar chart for total number of deaths by year, marking covid vs. non-covid COD
barchart_total_deaths <- code_deaths_in %>% 
  mutate(year=as.character(yod),
         primary_cod_covid = as.character(primary_cod_covid)) %>% 
  group_by(year,primary_cod_covid) %>% 
  summarize(count=n()) %>% 
  ungroup() %>% 
  mutate(count_display = case_when(
    primary_cod_covid == 1 ~ paste(count,"COVID deaths"),
    TRUE ~ paste(count,"\nnon-COVID deaths")
  )) %>% 
  ggplot(aes(x=year,
             y=count,
             fill=year)) +
  geom_bar(aes(
    alpha=primary_cod_covid),
    stat="identity",
    show.legend = FALSE) +
  scale_fill_manual(values=c(start_color,end_color)) +
  geom_text(aes(y=count, label=count_display),
            position = position_dodge(width = .5),
            color = "gray30",
            vjust=-.5,
            size=3) +
  geom_text(data=summary_year, aes(x=year, y=count,label=year,color=year),
            vjust=24,
            hjust=.5,
            size=3.5,
            show.legend = FALSE) +
  scale_color_manual(values=c(start_color,end_color)) +
  scale_y_continuous(expand = c(.5, 1)) +
  theme_void()+
  theme(plot.margin = margin(0,0,0,0, "cm"))


# Create layout for putting both charts together
layout <- "
BBBBBAAA
BBBBBAAA
BBBBBAAA
BBBBBAAA
BBBBBAAA
"

# Combine charts
combined_for_output <- barchart_total_deaths + dumbbell_percent  +
  plot_layout(design=layout)
```

##### Files in this folder:

- .png file: image of the viz of the month
- .Rmd file: the code used to create this document
- .html file: a downloadable version of this document
