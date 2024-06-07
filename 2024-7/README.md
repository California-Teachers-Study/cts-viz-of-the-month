# July 2024 CTS Viz of the Month
Emma Spielfogel
2024-07-07

### Packages used


```r
library(tidyverse)
library(sf)
library(janitor)
library(gganimate)
```

### Description of inputs

-   Data
    -   A dataframe called geocodes of participant addresses (in this case latitude and longitude) plus the move in date (year) associated with each address. Each row represents a participant address.
    -   A shape file called county_shapes of the boundaries of CA counties. Here, a shape file was used to determine the county of each participant address based on participant latitude and longitude, but if county is already available for each participant address, this step is not necessary. The shape file is available here in a subfolder.
-   Variables
    -   latitude: the latitude of each participant address
    -   longitude: the longitude of each participant address
    -   year: the year associated with the date moved in to the address
    -   county: called "NAME" in the shape file, the county variable was created via a spatial join between the participant lat/longs and the shape file county boundaries

### Visualization code


```r
# Get outline of CA counties for background map
which_state <- "california"
county_outline <- map_data("county", region=which_state)

# Create and format background map
base_map <- ggplot(data = county_outline, mapping = aes(x = long, y = lat, group = group)) +
  geom_polygon(color = "black", fill = "white") +
  coord_quickmap() +
  theme_void() 

# Read in shapefile of CA county boundaries
county_shapes<-st_read("2024-7/county_boundaries/co06_d90.shp", quiet=T)

# Assign county from latitude and longitude
latlong_sf <- geocodes %>%
  filter(!is.na(latitude), !is.na(longitude)) %>%
  st_as_sf(coords = c("longitude", "latitude"), crs=st_crs(county_shapes))

intersected <- st_intersects(latlong_sf, county_shapes)

county_df <- latlong_sf %>%
  mutate(intersection = as.integer(intersected),
         county = if_else(is.na(intersection),"", county_shapes$NAME[intersection]))

# Create statistics for visualization
county_count <- county_df %>% 
  group_by(year,county) %>% 
  summarise(N=n()) %>% 
  filter(!(county == "")) %>% # removing those without a valid county assignment
  ungroup() %>% 
  group_by(year) %>%
  mutate(percent =  round(N/sum(N),2)) %>% 
  ungroup() %>%
  as.data.frame() %>% # converting to a dataframe instead of sf object to
  select(-(geometry)) ## remove detailed geolocations

# Merge shape file and CTS data
county_pop_sf <- county_shapes  %>%
  left_join(county_count, by=c("NAME" = "county"))

# Get unique list of counties in order to label the visualization
label_counties <- county_pop_sf %>% 
  select(-year) %>% 
  group_by(NAME) %>% 
  filter(row_number()==1) %>% 
  ungroup()

# Create the map
cts_county_map_all <- county_pop_sf %>% 
  ggplot() +
  geom_polygon(data = county_info, mapping = aes(x = long, y = lat, group = group),
               fill="white", color = "darkgrey") +
  geom_sf(mapping=aes(fill=percent, group=year), color="darkgrey") +
  geom_sf_text(data = subset(label_counties, percent > .02),
               aes(label = NAME),
               # max.overlaps = 40,
               fontface = "bold",
               color = "black",
               # segment.color="#009ED8",
               # min.segment.length=0,
               size = 2.5) +
  scale_fill_viridis_c(trans = 'reverse',
                       option = "mako",
                       name="% of all CA moves \nfor that year\n",
                       labels=scales::label_percent()) +
  theme(axis.text = element_blank(), 
        axis.title =element_blank(),
        plot.title = element_text(hjust = 0.5),
        plot.subtitle = element_text(hjust = 0.5),
        plot.tag = element_text(size=14),
        plot.tag.position = "top",
        axis.ticks=element_blank(), 
        panel.grid.major = element_blank(), 
        panel.grid.minor = element_blank(), 
        panel.background = element_blank(), 
        plot.caption = element_text(hjust = 0),
        plot.margin = unit(c(0.5, 0.5, 0.5, 0.5), 
                           "inches"),
        legend.position = c(0.9,0.67))+
  labs(tag = "Where did participants move within California over follow-up?\n\n")

# Create the yearly transitions
cts_county_animation <- cts_county_map_all +
  transition_manual(year) +
  labs(title = bquote("Year of move:" ~bold("{current_frame}")),
       subtitle = '\nYear {frame} of {nframes}')

# Animate the visualization
animate(cts_county_animation, nframes = 24, fps = 1,
        height = 600, width = 644)

# Save animation as a gif
anim_save("2024-7/CA_moves_by_county_over_followup.gif")
```

### Additional Resources

[How to Plot and Animate Data on Maps Using the R Programming Language](https://conservancy.umn.edu/server/api/core/bitstreams/ba6ea265-c865-48d2-bcf4-e287542efd9a/content)

##### Files in this folder:

-   .png file: image of the viz of the month
-   .Rmd file: the code used to create this document
-   .html file: a downloadable version of this document
