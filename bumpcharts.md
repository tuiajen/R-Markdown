Bump Chart Example
================
J. Tuia
October 14, 2018

``` r
#Load the libraries
library(RColorBrewer)
library(reshape2)
library(ggplot2)
library(grid)

#Get Colorbrewer info and the appropriate colors
par(mar=c(1,5,1,1))
#display.brewer.all(colorblindFriendly = TRUE)
custom_palette <- c(
  brewer.pal(n = 11, name = 'RdYlBu')[11:8], # These are the blue colors
  "lightgoldenrod1", # This is the mustard color
  brewer.pal(n = 11, name = 'RdYlBu')[5:1], # These are the warm colors
  "gray", "darkgray"
)
#Build/import the data

input_df <- read.table(
  header = TRUE, stringsAsFactors = FALSE,
  text = '
  "Topping"           2015    2016    2017  2018  2019  label_color
  "Lettuce"           1       6       7     5     6     "white"
  "Sour cream"        2       1       2     4     3     "white"
  "Tomato"            3       5       6     9     9     "white"
  "Pico de gallo"     4       2       3     2     1     "black"
  "Avocado"           5       4       1     1     2     "black"
  "Cojita cheese"     6       3       4     3     4     "black"
  "Lime crema"        7       8       10    7     8     "white"
  "Shrimp"            8       7       5     6     5     "white"
  "Fish"              9       9       8     NA    NA    "white"
  "Pulled chicken"    10      10      NA    NA    10    "white"
  "Queso"             NA      NA      9     10    NA    "white"
  "Scrambled egg"     NA      NA      NA    8     7     "white"
  '
)
#convert our Topping column to factors. 
#The levels of a factor column essentially provide order to arbitrary string values. 
input_df$Topping <- factor(input_df$Topping, levels = input_df$Topping)

# turn it from wide to long. Keep the topping label and label color and pivot on year for each value
plot_df = melt(input_df, id.vars = c("Topping", "label_color"), variable.name = "year")
plot_df$year = gsub("X", "", plot_df$year)

#build the ggplot
plot = ggplot(
  plot_df, aes(x = year, y = value, group = Topping, color = factor(Topping), label = value)
) +
  geom_line(size = 1.5) +
  geom_point(size = 6) +
  geom_text(color = plot_df$label_color, fontface = "bold", size = 3) +
  scale_y_reverse(limits=c(10,1)) +
  scale_x_discrete(position = "top") +
  theme_minimal() +
  theme(
    legend.position = "none",
    panel.grid = element_blank(),
    axis.title = element_blank(),
    axis.text.y = element_blank()
    
  ) + 
  scale_color_manual(values = custom_palette)

#plot

plot = plot +
  annotate(
    "text", x = rep(0.8, 10), y = 1:10, label = input_df$Topping[1:10], color = custom_palette[1:10], 
    hjust = 1, fontface = "bold"
  ) +
  annotate("text", x = 3, y = 8.5, label = "Queso", fontface = "bold", color = "darkgray") +
  annotate("text", x = 5, y = 6.5, label = "Scrambled egg", hjust = 1, fontface = "bold", color = "darkgray") +
  theme(plot.margin = unit(c(1, 1, 1, 3), "lines"))

# Code to prevent clipping
gt <- ggplot_gtable(ggplot_build(plot))
```

    ## Warning: Removed 8 rows containing missing values (geom_path).

    ## Warning: Removed 10 rows containing missing values (geom_point).

    ## Warning: Removed 10 rows containing missing values (geom_text).

``` r
gt$layout$clip[gt$layout$name == "panel"] <- "off"
grid.draw(gt)
```

![](bumpcharts_files/figure-markdown_github/unnamed-chunk-1-1.png)
