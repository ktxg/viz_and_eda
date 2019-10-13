‘ggplot’ 1
================
Katie
10/12/2019

## Create the weather data

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(c("USW00094728", "USC00519397", "USS0023B17S"),
                      var = c("PRCP", "TMIN", "TMAX"), 
                      date_min = "2017-01-01",
                      date_max = "2017-12-31") %>%
  mutate(
    name = recode(id, USW00094728 = "CentralPark_NY", 
                      USC00519397 = "Waikiki_HA",
                      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'crul':
    ##   method                 from
    ##   as.character.form_file httr

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## file path:          /Users/katiegao/Library/Caches/rnoaa/ghcnd/USW00094728.dly

    ## file last updated:  2019-10-12 11:31:05

    ## file min/max dates: 1869-01-01 / 2019-10-31

    ## file path:          /Users/katiegao/Library/Caches/rnoaa/ghcnd/USC00519397.dly

    ## file last updated:  2019-10-12 11:31:20

    ## file min/max dates: 1965-01-01 / 2019-10-31

    ## file path:          /Users/katiegao/Library/Caches/rnoaa/ghcnd/USS0023B17S.dly

    ## file last updated:  2019-10-12 11:31:25

    ## file min/max dates: 1999-09-01 / 2019-10-31

``` r
weather_df
```

    ## # A tibble: 1,095 x 6
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

## Create a ggplot

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point() 
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_i_files/figure-gfm/unnamed-chunk-1-1.png)<!-- -->

## Alternate way of making this plot

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point() 
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_i_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

## Saving initial plots (Jeff mostly doesn’t do it this way)

``` r
scatterplot = 
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point() 

scatterplot 
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_i_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

## Adding color

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .4)  
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_i_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

## Why do ‘aes’ positions matter?

Here’s the first
plot:

``` r
## this makes one smooth line without the gray uncertainty band around it
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .4) + 
  geom_smooth(se = FALSE)  
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_i_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

vs.

The second
plot:

``` r
## this makes 3 different colored smooth lines for each of the locations 
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .4) + 
  geom_smooth(se = FALSE) 
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_i_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

## Time to facet\!

``` r
## creates 3 smooth lines that are the same color as the locations so is kinda hard to see
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .4) + 
  geom_smooth(se = FALSE) + 
  facet_grid(~name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_i_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
## creates 3 smooths lines that stand out better amongst the locations and is easier to see  
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .4) + 
  geom_smooth(se = FALSE) + 
  facet_grid(~name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).
    
    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_i_files/figure-gfm/unnamed-chunk-7-2.png)<!-- -->

## This is fine. But not interesting.

``` r
weather_df %>% 
  ggplot(aes(x= date, y = tmax, color = name)) + 
  geom_point(aes(size = prcp), alpha = .35) + 
  geom_smooth(size = 2, se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](viz_i_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

Learning Assessment: Write a code chain that starts with weather\_df;
focuses only on Central Park, converts temperatures to Fahrenheit, makes
a scatterplot of min vs. max temperature, and overlays a linear
regression line (using options in geom\_smooth()).

``` r
weather_df %>% 
  filter(name == "CentralPark_NY") %>% 
  mutate(
    tmax = (tmax * 9 / 5) + 32, 
    tmin= (tmin * 9 / 5) + 32) %>% 
ggplot(aes(x = tmin ,y = tmax)) +
  geom_point(alpha = .5) +
  geom_smooth(method = "lm", se = FALSE) 
```

![](viz_i_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

``` r
## Answer Key Solution  
weather_df %>% 
  filter(name == "CentralPark_NY") %>% 
  mutate(tmax_fahr = tmax * (9 / 5) + 32,
         tmin_fahr = tmin * (9 / 5) + 32) %>% 
  ggplot(aes(x = tmin_fahr, y = tmax_fahr)) +
  geom_point(alpha = .5) + 
  geom_smooth(method = "lm", se = FALSE)
```

![](viz_i_files/figure-gfm/unnamed-chunk-9-2.png)<!-- -->

## Some extra stuff
