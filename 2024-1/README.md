# January 2024 CTS Viz of the Month
Emma Spielfogel
2024-01-02

### Packages used

```r
library(tidyverse)
library(jpeg)
# install.packages("ggchicklet", repos = "https://cinc.rud.is")
library(ggchicklet)
library(ggpubr)
library(ggimage)
library(scales)
```

### Description of inputs

* Data
    + A data frame called "birth_data" containing the count of births in each calendar month
    + An image to be used as the background of the chart, saved as "bg"
    + An image to be used as an icon over each bar, saved as "img"

* Variables
    + birth_month: the birth month as text
    + birth_month_n: the birth month as a number, which can be used for ordering the chart
    + count: the number of participants

### Visualization code

```r
# bring in background image
bg <- readJPEG("your-file-location/background.jpeg")

# bring in icon image
img <- "your-file-location/icon-image.png"

# add icon that will go over each bar to the data frame
birth_month_chart <- birth_data %>% 
  mutate(image = sample(img, size=1, replace = TRUE))

# create visualization
birth_month_chart %>%
  ggplot(aes(x=reorder(birth_month,birth_month_n),
             y=count,
             fill=birth_month)) +
  background_image(bg) +
  geom_chicklet(show.legend = FALSE) +
  geom_image(aes(image=image),
             size=.09,
             nudge_y = 750) +
  geom_text(data=birth_data,aes(label = scales::comma(count)),
            family = "Palatino",
            color = "#454545",
            vjust = 15,
            size = 4) +
  annotate("text",
           x = 7,
           y = 6000,
           hjust = .5,
           color = "#3D3C3A",
           family = "Palatino",
           fontface="bold.italic",
           label = "July is the\nmost common \nbirth month \nin the CTS",
           size = 3) +
  expand_limits(y=18000) +
  scale_y_discrete(expand = c(0,0)) +
  scale_x_discrete(expand = c(.08,.08)) +
  labs(title = "What are the most common birth months for CTS participants?",
       subtitle = "See the counts for each month below.",
       x = "",
       y = "") +
  theme_minimal(base_family = "Palatino") +
  theme(panel.grid = element_blank(),
        plot.title = element_text(size = 20,
                                  hjust=0.5),
        plot.subtitle = element_text(hjust = 0.5),
        axis.text.x = element_text(size = 10))
```

##### Files in this folder:

- .png file: image of the viz of the month
- .Rmd file: the code used to create this document
- .html file: a downloadable version of this document
