visualiztion_1
================
Ziqing Wang
2022-09-29

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
```

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2022-09-29 10:32:14 (8.401)

    ## file min/max dates: 1869-01-01 / 2022-09-30

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USC00519397.dly

    ## date created (size, mb): 2022-09-29 10:32:22 (1.699)

    ## file min/max dates: 1965-01-01 / 2020-03-31

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2022-09-29 10:32:26 (0.95)

    ## file min/max dates: 1999-09-01 / 2022-09-30

``` r
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

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) + geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](visualization_1_files/figure-gfm/basic%20scatterplot-1.png)<!-- -->
You can also use the %\>% pipe to process the data before making a plot
(if you don’t want to save the intermediate dataset).

ggplot objects can also be saved as variables in the environment.

Color the scatterplot by names of weather station:

``` r
ggplot(weather_df, aes(x=tmin, y=tmax)) + geom_point(aes(color=name))
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](visualization_1_files/figure-gfm/more%20advanced%20scatterplot-1.png)<!-- -->
Add smooth approximations scatterpoints categorized by names of weather
station, and make the points more transparent:

``` r
ggplot(weather_df, aes(x=tmin, y=tmax, color=name)) +
  geom_point(alpha=0.5) + 
  geom_smooth(se=FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](visualization_1_files/figure-gfm/unnamed-chunk-2-1.png)<!-- --> Add
one smooth approximation to all scatterpoints by changing the location
of color=name:

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](visualization_1_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

Use facet grid to create and arrange multiple panels:

``` r
ggplot(weather_df, aes(x=tmin, y=tmax, color=name)) + 
  geom_point(alpha=0.5) +
  geom_smooth(se=FALSE) +
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](visualization_1_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

Seasonal trend of the maximum temperature:

``` r
ggplot(weather_df, aes(x=date, y=tmax, color=name)) +
  geom_point(aes(size=prcp), alpha=0.5) +
  geom_smooth(se=F) + 
  facet_grid(. ~ name) + 
  theme(axis.text.x=element_text(angle=45, vjust=1, hjust=1))
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](visualization_1_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

When you’re making a scatterplot with lots of data, there’s a limit to
how much you can avoid overplotting using alpha levels and transparency.
In these cases geom_hex(), geom_bin2d(), or geom_density2d() can be
handy:

``` r
ggplot(weather_df, aes(x = tmax, y = tmin)) + 
  geom_hex() # hex as in hexagon
```

    ## Warning: Removed 15 rows containing non-finite values (stat_binhex).

![](visualization_1_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

Some univariate plots:

``` r
ggplot(weather_df, aes(x = tmax)) + 
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 3 rows containing non-finite values (stat_bin).

![](visualization_1_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
ggplot(weather_df, aes(x = tmax, fill = name)) + 
  geom_histogram(position = "dodge", binwidth = 2)
```

    ## Warning: Removed 3 rows containing non-finite values (stat_bin).

![](visualization_1_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

``` r
ggplot(weather_df, aes(x = tmax, fill = name)) + 
  #geom_density(alpha = .4, adjust = .5, color = "blue")
  geom_histogram() +
  facet_grid(. ~ name)
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 3 rows containing non-finite values (stat_bin).

![](visualization_1_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

The position = “dodge” places the bars for each group side-by-side, but
this gets sort of hard to understand. I often prefer density plots in
place of histograms.

``` r
ggplot(weather_df, aes(x = tmax, fill = name)) + 
  geom_density(alpha = .4, adjust = .5, color = "blue")
```

    ## Warning: Removed 3 rows containing non-finite values (stat_density).

![](visualization_1_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

Histograms and densities are one way of investigating univariate
distributions; boxplots are another.

``` r
ggplot(weather_df, aes(x = name, y = tmax)) + geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](visualization_1_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

Violin plots are sometimes nice, but folks complain that they don’t look
like violins. They can be useful if you are comparing a lot more tham 3
categories.

``` r
ggplot(weather_df, aes(x = name, y = tmax)) + 
  geom_violin(aes(fill = name), alpha = .5) + 
  stat_summary(fun = "median", color = "blue")
```

    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

    ## Warning: Removed 3 rows containing non-finite values (stat_summary).

    ## Warning: Removed 3 rows containing missing values (geom_segment).

![](visualization_1_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->
Ridge plots were the trendiest plot of 2017, and were a replacement for
both boxplots and violin plots. They’re implemented in the ggridges
package, and are nice if you have lots of categories in which the shape
of the distribution matters.

``` r
ggplot(weather_df, aes(x = tmax, y = name)) + 
  geom_density_ridges(scale = .85)
```

    ## Picking joint bandwidth of 1.84

    ## Warning: Removed 3 rows containing non-finite values (stat_density_ridges).

![](visualization_1_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

You will, on occasion, need to save a plot to a specific file. Don’t use
the built-in “Export” button! If you do, your figure is not reproducible
– no one will know how your plot was exported. Instead, use ggsave() by
explicitly creating the figure and exporting; ggsave will guess the file
type you prefer and has options for specifying features of the plot. In
this setting, it’s often helpful to save the ggplot object explicitly
and then export it (using relative paths!).

``` r
weather_plot = ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5) 

ggsave("weather_plot.pdf", weather_plot, width = 8, height = 5)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

You can also embad plots in the knitted file?

``` r
knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp = .6,
  out.width = "90%"
)
```

What makes embedding figures difficult at first is that things like the
font and point size in the figures generated by R are constant – that
is, they don’t scale with the overall size of the figure. As a result,
text in a figure with width 12 will look smaller than text in a figure
with width 6 after both have been embedded in a document. As an example,
the code chunk below has set fig.width = 12

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name))
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="visualization_1_files/figure-gfm/unnamed-chunk-16-1.png" width="90%" />
