Lab05
================
VY
2022-09-21

``` r
webshot::install_phantomjs()
```

    ## It seems that the version of `phantomjs` installed is greater than or equal to the requested version.To install the requested version or downgrade to another version, use `force = TRUE`.

``` r
library(leaflet)
library(webshot)
library(data.table)
library(dtplyr)
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:data.table':
    ## 
    ##     between, first, last

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
if (!file.exists("met_all.gz"))
  download.file(
    url = "https://raw.githubusercontent.com/USCbiostats/data-science-data/master/02_met/met_all.gz",
    destfile = "met_all.gz",
    method   = "libcurl",
    timeout  = 60
    )
met <- data.table::fread("../Lab04/met_all.gz")
met <- met[temp>-17][elev == 9999.0, elev := NA]
```

``` r
# Download the data
stations <- fread("ftp://ftp.ncdc.noaa.gov/pub/data/noaa/isd-history.csv")
stations[, USAF := as.integer(USAF)]
```

    ## Warning in eval(jsub, SDenv, parent.frame()): NAs introduced by coercion

``` r
# Dealing with NAs and 999999
stations[, USAF   := fifelse(USAF == 999999, NA_integer_, USAF)]
stations[, CTRY   := fifelse(CTRY == "", NA_character_, CTRY)]
stations[, STATE  := fifelse(STATE == "", NA_character_, STATE)]

# Selecting the three relevant columns, and keeping unique records
stations <- unique(stations[, list(USAF, CTRY, STATE)])

# Dropping NAs
stations <- stations[!is.na(USAF)]

# Removing duplicates
stations[, n := 1:.N, by = .(USAF)]
stations <- stations[n == 1,][, n := NULL]
```

``` r
met <- merge(
  x = met,
  y = stations,
  by.x = "USAFID",
  by.y = "USAF",
  all.x = TRUE,
  all.y = FALSE
)
```

\#What is the median station in terms of temperature, wind speed, and
atmospheric pressure? Look for the three weather stations that best
represent continental US using the quantile() function. Do these three
coincide?

``` r
station_averages <- 
  met[ , .(
    temp=mean(temp, na.rm=T),
    wind.sp=mean(wind.sp, na.rm=T),
    atm.press=mean(atm.press, na.rm=T)
  ), by = "USAFID"]
stat_medians <- station_averages[ , .(
    temp50 =median(temp, na.rm=T),
    wind.sp50 =median(wind.sp, na.rm=T),
    atm.press50 =median(atm.press, na.rm=T)
  )]
station_averages[ , temp_dist50 := abs(temp - stat_medians$temp50)][order(temp_dist50)]
```

    ##       USAFID      temp   wind.sp atm.press  temp_dist50
    ##    1: 720458 23.681730  1.209682       NaN  0.002328907
    ##    2: 725515 23.686388  2.709164       NaN  0.002328907
    ##    3: 725835 23.678347  2.652381       NaN  0.005712423
    ##    4: 724509 23.675100  4.066833  1013.863  0.008959632
    ##    5: 720538 23.665932  1.907897       NaN  0.018127186
    ##   ---                                                  
    ## 1584: 722788 36.852459  3.393852       NaN 13.168399783
    ## 1585: 722787 37.258907  2.847381       NaN 13.574848130
    ## 1586: 723805 37.625391  3.532935  1005.207 13.941331392
    ## 1587: 726130  9.189602 12.239908       NaN 14.494456787
    ## 1588: 720385  8.044959  7.298963       NaN 15.639100105
