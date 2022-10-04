Visualization
================
2022-10-04

## Load the weather data

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USC00519397", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2017-01-01",
    date_max = "2017-12-31") %>%
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USC00519397 = "Waikiki_HA",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())

weather_df
```

    ## # A tibble: 1,095 × 6
    ##    name           id          date        prcp  tmax  tmin
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 CentralPark_NY USW00094728 2017-01-01     0   8.9   4.4
    ##  2 CentralPark_NY USW00094728 2017-01-02    53   5     2.8
    ##  3 CentralPark_NY USW00094728 2017-01-03   147   6.1   3.9
    ##  4 CentralPark_NY USW00094728 2017-01-04     0  11.1   1.1
    ##  5 CentralPark_NY USW00094728 2017-01-05     0   1.1  -2.7
    ##  6 CentralPark_NY USW00094728 2017-01-06    13   0.6  -3.8
    ##  7 CentralPark_NY USW00094728 2017-01-07    81  -3.2  -6.6
    ##  8 CentralPark_NY USW00094728 2017-01-08     0  -3.8  -8.8
    ##  9 CentralPark_NY USW00094728 2017-01-09     0  -4.9  -9.9
    ## 10 CentralPark_NY USW00094728 2017-01-10     0   7.8  -6  
    ## # … with 1,085 more rows

As a starting point, let’s revisit the scatterplot of `tmax` against
`tmin` made in Visualization Pt 1.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5)
```

![](viz_ii_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

## Labels

We can add labels using the `labs()` function.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5) +
  labs(
    x = "Minimum Daily Temp (C)",
    y = "Maximum Daily Temp (C)",
    title = "Scatterplot of daily temp extremes",
    caption = "Data come from the rnoaa package",
    color = "Location"
  )
```

![](viz_ii_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

## Scales

You can adjust the scales and add labels to the scales. We can also
transform the scales using `trans()` (e.g. square root, etc.)

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5) +
  labs(
    x = "Minimum Daily Temp (C)",
    y = "Maximum Daily Temp (C)",
    title = "Scatterplot of daily temp extremes",
    caption = "Data come from the rnoaa package",
    color = "Location"
  ) +
  scale_x_continuous(breaks = c(-10, 0, 15),
                     labels = c("-10C", "0", "15C")) +
  scale_y_continuous(
    trans = "sqrt"
  )
```

![](viz_ii_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

## Colours

We can manually adjust colours using `scale_colour_hue()`, or use the
`viridis` package. In general, encourage using the viridis colour
palette.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5) +
  labs(
    x = "Minimum Daily Temp (C)",
    y = "Maximum Daily Temp (C)",
    title = "Scatterplot of daily temp extremes",
    caption = "Data come from the rnoaa package",
    color = "Location"
  ) +
  scale_colour_hue(
    name = "Location",
    h = c(100, 300))
```

![](viz_ii_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5) +
  labs(
    x = "Minimum Daily Temp (C)",
    y = "Maximum Daily Temp (C)",
    title = "Scatterplot of daily temp extremes",
    caption = "Data come from the rnoaa package",
    color = "Location"
  ) +
  viridis::scale_color_viridis(
    name = "Location", 
    discrete = TRUE #Makes it a discrete rather than continuous colour palette
  )
```

![](viz_ii_files/figure-gfm/unnamed-chunk-5-2.png)<!-- -->

## Themes

``` r
ggp_weather = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5) + 
  labs(
    title = "Temperature plot",
    x = "Minimum daily temperature (C)",
    y = "Maxiumum daily temperature (C)",
    caption = "Data from the rnoaa package"
  ) + 
  viridis::scale_color_viridis(
    name = "Location", 
    discrete = TRUE
  )

ggp_weather
```

![](viz_ii_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

Move the legend to the bottom of the plot. We can get adjust the grey
background, grid bars, etc.

``` r
ggp_weather + 
  theme_minimal() +
  theme(legend.position = "bottom")
```

![](viz_ii_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

Make sure your theme functions in the correct order! Applying an overall
theme overrides individual adjustments to the plot element. Here, we can
see the legend is no longer at the bottom.

``` r
ggp_weather + 
  theme(legend.position = "bottom") +
  theme_minimal()
```

![](viz_ii_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

## Setting options

You can set global options to control all ggplots you make in a R
markdown. (see P8105 website)
