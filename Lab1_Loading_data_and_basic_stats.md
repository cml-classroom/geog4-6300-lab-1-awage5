Geog4/6300: Lab 1
================

## Loading data into R, data transformation, and summary statistics

Your name: Alex Wages

**Overview:**

This lab is intended to assess your ability to use R to load data and to
generate basic descriptive statistics. You’ll be using monthly weather
data from the Daymet climate database (<http://daymet.ornl.gov>) for all
counties in the United States over a 11 year period (2010-2021). These
data are available on the Github repo for our course. The following
variables are provided:

-   cty\_txt: Code for joining to census data
-   year: Year of observation (with an initial “Y” to make it a
    character)
-   month: Month of observation (1=Jan, 2=Feb, etc.)
-   median\_tmax: Median maximum recorded temperature (Celsius)
-   median\_tmin: Median minimum recorded temperature (Celsius)
-   sum\_prcp: Total recorded prcpitation for the month (mm)
-   cty\_name: Name of the county
-   state: state of the county
-   region: Census region (map:
    <https://www2.census.gov/geo/pdfs/maps-data/maps/reference/us_regdiv.pdf>)
-   division: Census division
-   X: Longitude of the county centroid
-   Y: Latitude of the county centroid

These labs are meant to be done collaboratively, but your final
submission should demonstrate your own original thought (don’t just copy
your classmate’s work or turn in identical assignments). Your answers to
the lab questions should be typed in the provided RMarkdown template.
You’ll then “knit” this to an Github document and upload it to your
class Github repo.

**Procedure:**

Load the tidyverse package and import the data:

``` r
library(tidyverse)
daymet_data <- read_csv("data/daymet_monthly_median_2010-2021.csv")
```

We can look at the first few rows of the dataset using the *head*
function. We also use *kable* to format this nicely.

``` r
kable(head(daymet_data))
```

| cty\_txt | year  | month | median\_tmax | median\_tmin | sum\_prcp | cty\_name         | state  | region      | division         |         x |        y |
|:---------|:------|------:|-------------:|-------------:|----------:|:------------------|:-------|:------------|:-----------------|----------:|---------:|
| G02060   | Y2010 |     1 |        -4.27 |       -10.83 |     10.04 | Bristol Bay       | Alaska | West Region | Pacific Division | -156.7011 | 58.74213 |
| G02185   | Y2010 |     1 |       -20.73 |       -28.20 |      0.00 | North Slope       | Alaska | West Region | Pacific Division | -153.4411 | 69.30696 |
| G02180   | Y2010 |     1 |       -16.50 |       -23.72 |      5.75 | Nome              | Alaska | West Region | Pacific Division | -163.9703 | 64.89492 |
| G02050   | Y2010 |     1 |       -11.20 |       -18.90 |     24.55 | Bethel            | Alaska | West Region | Pacific Division | -159.7678 | 60.92187 |
| G02261   | Y2010 |     1 |       -13.93 |       -20.03 |     15.84 | Valdez-Cordova    | Alaska | West Region | Pacific Division | -144.4573 | 61.57080 |
| G02170   | Y2010 |     1 |        -5.10 |       -12.42 |     35.84 | Matanuska-Susitna | Alaska | West Region | Pacific Division | -149.5702 | 62.31653 |

**Question 1:** After loading the file into R, pick TWO variables and
determine whether they are nominal, ordinal, interval, and ratio data
using the course readings and lectures as reference. Justify each
classification in a sentence or two.

cty\_name would be nominal data because it does not have a natural order
or ranking. median\_tmax and median\_tmin are interval data because zero
is not the lowest possible temperature in Celsius.

There are a lot of observations here, 452,448, to be exact. To get a
better grasp on it, we can use group\_by and summarise in the tidyverse
package, which we covered in class. This will allow us to identify the
mean value for each year by county across the study period.

**Question 2:** Use group\_by and summarise to calculate the mean
minimum temperature for each year *by county* across all months, also
including State and Region as grouping variables. Your resulting dataset
should show the value of tmin for each county in each year. Use the
kable and head functions as shown above to call the resulting table.

``` r
mini_temp<-daymet_data %>%
  group_by(cty_name,month,year,state,region) %>%
  summarise(mini_temp=mean(median_tmin))
```

    ## `summarise()` has grouped output by 'cty_name', 'month', 'year', 'state'. You
    ## can override using the `.groups` argument.

``` r
 kable(head(mini_temp))
```

| cty\_name | month | year  | state          | region       | mini\_temp |
|:----------|------:|:------|:---------------|:-------------|-----------:|
| Abbeville |     1 | Y2010 | South Carolina | South Region |      -2.40 |
| Abbeville |     1 | Y2011 | South Carolina | South Region |      -1.80 |
| Abbeville |     1 | Y2012 | South Carolina | South Region |       1.12 |
| Abbeville |     1 | Y2013 | South Carolina | South Region |       0.38 |
| Abbeville |     1 | Y2014 | South Carolina | South Region |      -2.95 |
| Abbeville |     1 | Y2015 | South Carolina | South Region |      -0.15 |

**Question 3:** What if we were only interested in the South Region?
Filter the original data frame (daymet\_data) to just include counties
in this region. Then calculate the mean minimum temperature by year *for
each state.* For an optional extra challenge, use the round function to
include only 1 decimal point. You can type ?round in the console to find
documentation on this function. Use kable and head to call the first few
lines of the resulting table

``` r
south_mini_temp<-daymet_data %>%
  filter(region=="South Region") %>%
  group_by(state,year) %>%
  summarise(south_mini_temp=mean(median_tmin))
```

    ## `summarise()` has grouped output by 'state'. You can override using the
    ## `.groups` argument.

``` r
  kable(head(south_mini_temp))
```

| state   | year  | south\_mini\_temp |
|:--------|:------|------------------:|
| Alabama | Y2010 |          10.24551 |
| Alabama | Y2011 |          10.73490 |
| Alabama | Y2012 |          11.83369 |
| Alabama | Y2013 |          10.79009 |
| Alabama | Y2014 |          10.18975 |
| Alabama | Y2015 |          12.54363 |

**Question 4:** To visualize the trends, we could use ggplot to
visualize change in mean temperature over time. Create a line plot
(geom\_line) showing the state means you calculated in question 3. Use
the color parameter to show separate colors for each state. You may also
need to define the state as a group in the aesthetic parameter.

``` r
ggplot(south_mini_temp,aes(x=year,y=south_mini_temp,group=state, color=state))+
  geom_line()
```

![](Lab1_Loading_data_and_basic_stats_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

**Question 5:** If you wanted to look at these data as a table, you’d
need to have it in wide format. Use the pivot\_wider function to create
a wide format version of the data frame you created in question 3. In
this case, the rows should be states, the columns should be the years,
and the data in those columns should be mean minimum temperatures. Then
call the *whole table* using kable.

``` r
south_mini_temp %>%
  select(year,south_mini_temp) %>%
  pivot_wider(names_from =year, values_from =south_mini_temp)
```

    ## Adding missing grouping variables: `state`

    ## # A tibble: 17 x 13
    ## # Groups:   state [17]
    ##    state Y2010 Y2011 Y2012 Y2013 Y2014 Y2015 Y2016 Y2017 Y2018 Y2019 Y2020 Y2021
    ##    <chr> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
    ##  1 Alab~ 10.2  10.7  11.8  10.8  10.2  12.5  11.9  12.4  12.0  12.3  12.3  11.8 
    ##  2 Arka~ 10.1  10.2  11.1   9.17  9.11 10.8  10.9  11.0  10.3  10.6  10.5  10.7 
    ##  3 Dela~  8.71  8.84  9.18  8.28  7.56  8.66  8.83  9.27  8.85  9.27  9.57  8.87
    ##  4 Dist~  9.23  9.30  9.47  8.52  7.98  8.90  9.10  9.96  9.13  9.79  9.97  9.59
    ##  5 Flor~ 14.2  15.8  16.4  16.2  15.4  17.5  16.7  17.2  16.7  17.0  17.2  16.6 
    ##  6 Geor~ 10.3  11.2  12.4  11.2  10.8  12.8  12.1  12.7  12.2  12.7  12.7  12.0 
    ##  7 Kent~  7.31  7.89  8.21  6.75  6.66  8.02  8.39  8.48  8.08  8.36  8.21  8.08
    ##  8 Loui~ 13.2  13.9  14.7  13.2  12.7  14.8  15.0  15.4  14.5  14.4  14.8  14.7 
    ##  9 Mary~  8.33  8.62  8.73  7.79  7.01  8.11  8.39  8.86  8.40  8.91  9.22  8.68
    ## 10 Miss~ 11.0  11.4  12.2  11.0  10.5  12.8  12.4  12.9  12.2  12.5  12.6  12.4 
    ## 11 Nort~  8.68  9.12  9.73  8.71  8.57 10.2   9.84 10.0   9.85 10.5  10.1   9.62
    ## 12 Okla~  9.57  9.52 10.4   8.53  8.81  9.78 10.2   9.96  9.16  9.29  9.41  9.88
    ## 13 Sout~ 10.2  11.0  11.8  10.6  10.4  12.3  11.8  12.1  11.8  12.2  12.2  11.5 
    ## 14 Tenn~  8.28  8.57  9.36  7.86  7.58  9.36  9.15  9.33  9.18  9.57  9.26  9.06
    ## 15 Texas 11.5  12.2  12.8  11.5  11.5  12.4  12.9  12.9  12.1  12.0  12.4  12.5 
    ## 16 Virg~  7.41  7.88  8.22  7.33  6.84  8.21  8.17  8.38  8.16  8.69  8.60  8.05
    ## 17 West~  4.91  5.76  5.66  4.83  4.19  5.64  5.87  6.10  5.80  6.24  6.16  5.84

**Question 6:** Let’s assess the relationship of heat and precipitation
by region. Returning to the original dataset, create a data frame that
shows the mean *maximum* temperature and mean precipitation for *all*
states in 2015, also including region as a subgroup. Then use ggplot to
create a scatterplot (geom\_point) for these two variables, coloring the
points using the region variable.

``` r
max_temp<-daymet_data %>%
filter(year=="Y2015") 
```

    filter(year=="Y2015") %>%
    group_by(median_tmax,sum_prcp) %>%

filter(year==“Y2015”) %&gt;% summarise(max\_temp=m(median\_tmax),
max\_temp=mean(sum\_prcp))
ggplot(max\_temp,aes(x=median\_tmax,y=sum\_prcp,group=region,
color=region))+ geom\_point()
ggplot(max\_temp,aes(x=median\_tmax,y=sum\_prcp,group=region,
color=region))+ geom\_point() (some of the code I tried…)

**Question 7:** In the space below, explain what each function in your
code for question 6 does to the dataset in plain English.

I tried really hard on this question, but every time I summarized the
data all of the values in my max\_temp table disappeared. But
max\_temp&lt;-daymet\_data %&gt;% would create a new data table,
filter(year==“Y2015”) filtered the years to just 2015,
summarise(max\_temp=max(median\_tmax),max\_temp=mean(sum\_prcp)) would
calculate the maxium tempurature and mean preciptation,
ggplot(max\_temp,aes(x=median\_tmax,y=sum\_prcp,group=region,
color=region))+ geom\_point() would create a scatter plot.

**Bonus question:** In script 1-3, we covered ways of working with the
Daymet API. Create a script below that uses that daymetr package to
download data from Daymet for a place (or places) of your choosing, uses
a dplyr function (e.g., mutate, summarise, filter, etc.) to wrangle the
data, and then visualizes it using ggplot. In addition to this code,
write a short summary of a pattern that’s evident in the data you
visualized.

``` r
#Your code goes here
```

I thought this lab was very diffcult and I couldn’t complete Question 6.
I will come back to it and resubmit this lab if I have time!

**Lab reflection:** How do you feel about the work you did on this lab?
Was it easy, moderate, or hard? What were the biggest things you learned
by completing it?
