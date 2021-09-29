Homework 2 (Week 3)
================
Jake Fenley
9/21/2021

### [Link to Github](https://github.com/jpFenley/stat433-hw2)

### Part 1: Missing dep\_times

**How many flights have a missing dep\_time? What other variables are
missing? What might these rows represent?**

We filter those flights that have a NA as dep\_time. We can then count
how many rows this is true for, as well as the proportion of rows this
is the case for.

``` r
filter(flights, is.na(dep_time))
```

    ## # A tibble: 8,255 x 19
    ##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
    ##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
    ##  1  2013     1     1       NA           1630        NA       NA           1815
    ##  2  2013     1     1       NA           1935        NA       NA           2240
    ##  3  2013     1     1       NA           1500        NA       NA           1825
    ##  4  2013     1     1       NA            600        NA       NA            901
    ##  5  2013     1     2       NA           1540        NA       NA           1747
    ##  6  2013     1     2       NA           1620        NA       NA           1746
    ##  7  2013     1     2       NA           1355        NA       NA           1459
    ##  8  2013     1     2       NA           1420        NA       NA           1644
    ##  9  2013     1     2       NA           1321        NA       NA           1536
    ## 10  2013     1     2       NA           1545        NA       NA           1910
    ## # ... with 8,245 more rows, and 11 more variables: arr_delay <dbl>,
    ## #   carrier <chr>, flight <int>, tailnum <chr>, origin <chr>, dest <chr>,
    ## #   air_time <dbl>, distance <dbl>, hour <dbl>, minute <dbl>, time_hour <dttm>

``` r
filter(flights, is.na(dep_time)) %>% nrow
```

    ## [1] 8255

``` r
flights$dep_time %>% is.na %>% mean
```

    ## [1] 0.02451184

It appears that when dep\_time is missing so are dep\_delay, arr\_time,
arr\_delay, air\_time. It is very likely that the missing values
indicate a cancelled flight, as it would not have any time related info
if that were the case.

### Part 2: Converting time to minutes

**Currently dep\_time and sched\_dep\_time are convenient to look at,
but hard to compute with because they’re not really continuous numbers.
Convert them to a more convenient representation of number of minutes
since midnight.**

We can add two new fields using mutate. dep\_time\_min and
arr\_time\_min indicate the departure and arrival time in minutes since
midnight, respectively. We can do this by getting the hours and minutes
(using modulus and integer division) and converting this to minutes. We
output a couple of the rows to verify it worked as expected.

``` r
flights <- flights %>% mutate(
  dep_time_min = (dep_time %/% 100) * 60 + (dep_time %% 100),
  sched_dep_time_min = (sched_dep_time %/% 100) * 60 + (sched_dep_time %% 100))
            
flights %>% select(dep_time, dep_time_min, sched_dep_time, sched_dep_time_min) %>% head
```

    ## # A tibble: 6 x 4
    ##   dep_time dep_time_min sched_dep_time sched_dep_time_min
    ##      <int>        <dbl>          <int>              <dbl>
    ## 1      517          317            515                315
    ## 2      533          333            529                329
    ## 3      542          342            540                340
    ## 4      544          344            545                345
    ## 5      554          354            600                360
    ## 6      554          354            558                358

### Part 3: Look at the number of canceled flights per day. Is there a pattern? Is the proportion of canceled flights related to the average delay? Use multiple dyplr operations, all on one line, concluding with ggplot(aes(x= ,y=)) + geom\_point()

The following plots the cancellation rate and average delay time. Each
point corresponds to a day of the year. There does appear to be a
positive relationship between the cancellation rate and the average
delay time.

``` r
flights %>% 
  group_by(yday(time_hour)) %>% 
  summarize(cancel = mean(is.na(dep_time)),
            avg_delay = mean(dep_delay, na.rm = TRUE)) %>% 
  ggplot(aes(x= avg_delay ,y=cancel)) + geom_point()
```

![](README_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->
