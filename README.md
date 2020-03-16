Intro to the googlesheets4 package
================

## Lab meeting code club - 04 Mar. 2020

Kelly & Will

Documentation: <https://googlesheets4.tidyverse.org/>

``` r
library(tidyverse)
library(googlesheets4)
# have to run this interactively the first time to grant acess
sheet <- read_sheet('https://docs.google.com/spreadsheets/d/16equFGApqiclh7cZhtSeMRvSObYCBIRDWCkaFBEtA38/edit#gid=1648029123', 
                    sheet = '2018', skip = 3)
head(sheet)
```

### Task

1.  Read in the 2018 page.
2.  Extract the names of the presenters & the type of presentation they
    gave.
3.  Create a new tab in the schloss\_code\_club gsheet.

<!-- end list -->

``` r
sheet_1 <- sheet %>%
    select("Code Review / Journal Club", "Presenter CR/JC") %>%
    rename(name = "Presenter CR/JC", 
           presentation_type = "Code Review / Journal Club")
sheet_2 <- sheet %>%
    select("Research Talk / Chalk talk", "Presenter RT/CT") %>%
    rename(name = "Presenter RT/CT", 
           presentation_type = "Research Talk / Chalk talk")
non_names <- c("HOLIDAYS", "4th of July Holiday", "NA")
sheet_combined <-
    bind_rows(sheet_1, sheet_2) %>% 
    filter(!(name %in% non_names)) %>% 
    filter(!is.na(name))
head(sheet_combined)
```

``` r
sheet_tally <- sheet_combined %>% 
    group_by(name, presentation_type) %>%
    tally()
head(sheet_tally)
```

``` r
sheets_write(sheet_tally, 
             ss = "https://docs.google.com/spreadsheets/d/1s5xHWu2Ikc6snXFER4YiXzEE5ukfe6_fv59EQAuPSrg/edit#gid=0", 
             sheet = "Kelly_Will")
```

## Lab meeting code club - 16 Mar. 2020

Kelly, Nick, & Ari

``` r
library(tidyverse)
library(googlesheets4)
sheet <- read_sheet('https://docs.google.com/spreadsheets/d/1_quMjJRBHDLQSmWQouzzyi1DOejAtCZnAeesdVyRWiQ/edit#gid=1467293328',
    range = 'A1:J281',
    col_types="Dicccccccc",
    na="NA")
# plot volume of animals sold
sheet %>%
    ggplot(aes(x=Date, y=Total)) + # generate a line plot of the number of animals sold over time
        geom_line() +    # plot data as a line
        geom_smooth()  
# plot smoothed line through data
# plot low and high as lines for Feeder lambs
sheet %>%
    select(Date, `Feeder lambs`) %>%
    separate(`Feeder lambs`, sep='-', convert=TRUE, into=c("low", "high")) %>%
    pivot_longer(c(low, high),
        names_to="level",
        values_to="price_per_hund_weight",
        values_drop_na=TRUE) %>%
    ggplot(aes(x=Date, y=price_per_hund_weight, color=level)) +
        geom_line() +
        coord_cartesian(ylim=c(0,300))
# plot low and high boundaries on polygon for Feeder lambs
sheet %>%
    select(Date, `Feeder lambs`) %>%
    separate(`Feeder lambs`, sep='-', convert=TRUE, into=c("low", "high")) %>%
    ggplot(aes(x=Date, ymin=low, ymax=high)) +
        geom_ribbon() +
        coord_cartesian(ylim=c(0,300))
```
