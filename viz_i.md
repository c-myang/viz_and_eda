Visualization
================
2022-09-29

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

## Scatterplots!!

Create my first scatterplot ever. We first specify the data, then the
aesthetic mappings.

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) + #ggplot sets up axis ranges based on aes()
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_i_files/figure-gfm/scatterplot-1.png)<!-- -->

New approach (using pipes), same plot. Using pipes, we can mutate,
filter, select, before making our plot.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_i_files/figure-gfm/pipeplot-1.png)<!-- -->

Save and edit plot object (Less common workflow). You can create a
ggplot object, and then add `geom` functions, print or save later, etc.

``` r
weather_plot = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax))

weather_plot + geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_i_files/figure-gfm/plotobject-1.png)<!-- -->

## Advanced scatterplot…

Start with the same one and make it fancy! We are going to assign the
`color` of the points to the `name` of the weather station using `aes`.
We will also add another geometry, `geom_smooth`.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point() + 
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_i_files/figure-gfm/advanced-1.png)<!-- -->

What about the `aes` placement? When we move aesthetics inside specific
geometries, the color applies only to the scatterplot, not the smooth
line plot! If you put aesthetics in ggplot, it is applied universally.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name)) + # Move color aes into the specific geometry (points)
  geom_smooth()
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

![](viz_i_files/figure-gfm/aes-1.png)<!-- -->

Let’s facet some things!! (And adjust transparency)

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = 0.5, size = 0.3) + #Apply 50% transparency to geom_point
  geom_smooth(se = FALSE) + 
  facet_grid(. ~ name) # "Nothing" defines rows (don't make rows), name defines columns
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](viz_i_files/figure-gfm/facet-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, alpha = tmin, color = name)) + #Apply transparency according to tmin
  geom_point(size = 0.3) + 
  geom_smooth(se = FALSE) + 
  facet_grid(. ~ name) # "Nothing" defines rows (don't make rows), name defines columns
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](viz_i_files/figure-gfm/facet-2.png)<!-- -->

Let’s combine some elements and try a new plot.

``` r
weather_df %>% 
  ggplot(aes(x = date, y = tmax, color = name)) + 
  geom_point(aes(size = prcp), alpha = 0.5) + 
  geom_smooth(se = FALSE) + 
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](viz_i_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->
