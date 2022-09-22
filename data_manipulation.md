Data Manipulation with `dplyr`
================

Once you’ve imported data, you’re going to need to do some cleaning up.

``` r
library(tidyverse)
## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
## ✔ ggplot2 3.3.6      ✔ purrr   0.3.4 
## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
## ✔ tidyr   1.2.0      ✔ stringr 1.4.1 
## ✔ readr   2.1.2      ✔ forcats 0.5.2 
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()

options(tibble.print_min = 3)

litters_data = 
  read_csv("./data/FAS_litters.csv")
## Rows: 49 Columns: 8
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (2): Group, Litter Number
## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
litters_data =
  janitor::clean_names(litters_data)

pups_data = read_csv("./data/FAS_pups.csv")
## Rows: 313 Columns: 6
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (1): Litter Number
## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
pups_data = janitor::clean_names(pups_data)
```

### select

Select is useful when you want to select certain columns from your
dataset for your analysis.

Ex: selecting 4 variables from dataset `litters_data`:

``` r
select(litters_data, group, litter_number, gd0_weight, pups_born_alive)
## # A tibble: 49 × 4
##   group litter_number gd0_weight pups_born_alive
##   <chr> <chr>              <dbl>           <dbl>
## 1 Con7  #85                 19.7               3
## 2 Con7  #1/2/95/2           27                 8
## 3 Con7  #5/5/3/83/3-3       26                 6
## # … with 46 more rows
```

Can specify a range of variables to include:

Ex: select all variables from dataset `litters_data` ranging from
`group` variable to `gd_of_birth` variable To do this, you need to know
how your variables are arranged in your dataset

``` r
select(litters_data, group:gd_of_birth)
## # A tibble: 49 × 5
##   group litter_number gd0_weight gd18_weight gd_of_birth
##   <chr> <chr>              <dbl>       <dbl>       <dbl>
## 1 Con7  #85                 19.7        34.7          20
## 2 Con7  #1/2/95/2           27          42            19
## 3 Con7  #5/5/3/83/3-3       26          41.4          19
## # … with 46 more rows
```

You can also specify columns you’d like to remove:

Ex: we want everything except for the variable `pups_survive`

``` r
select(litters_data, -pups_survive)
## # A tibble: 49 × 7
##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive pups_…¹
##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>   <dbl>
## 1 Con7  #85                 19.7        34.7          20               3       4
## 2 Con7  #1/2/95/2           27          42            19               8       0
## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6       0
## # … with 46 more rows, and abbreviated variable name ¹​pups_dead_birth
```

Or we want to remmove multiple variables:

``` r
select(litters_data, -pups_survive, -group)
## # A tibble: 49 × 6
##   litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive pups_dead_b…¹
##   <chr>              <dbl>       <dbl>       <dbl>           <dbl>         <dbl>
## 1 #85                 19.7        34.7          20               3             4
## 2 #1/2/95/2           27          42            19               8             0
## 3 #5/5/3/83/3-3       26          41.4          19               6             0
## # … with 46 more rows, and abbreviated variable name ¹​pups_dead_birth
```

You can rename variables as part of this process: Ex: want to select two
columns - `group` and `litter_number` and rename them GROUP and
LiTtEr_NuMbEr respectively

``` r
select(litters_data, GROUP = group, LiTtEr_NuMbEr = litter_number)
## # A tibble: 49 × 2
##   GROUP LiTtEr_NuMbEr
##   <chr> <chr>        
## 1 Con7  #85          
## 2 Con7  #1/2/95/2    
## 3 Con7  #5/5/3/83/3-3
## # … with 46 more rows
```

If all you want to do is rename something, you can use `rename` instead
of `select`. This will rename the variables you care about, and keep
everything else:

``` r
rename(litters_data, GROUP = group, LiTtEr_NuMbEr = litter_number)
## # A tibble: 49 × 8
##   GROUP LiTtEr_NuMbEr gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² pups_…³
##   <chr> <chr>              <dbl>       <dbl>       <dbl>   <dbl>   <dbl>   <dbl>
## 1 Con7  #85                 19.7        34.7          20       3       4       3
## 2 Con7  #1/2/95/2           27          42            19       8       0       7
## 3 Con7  #5/5/3/83/3-3       26          41.4          19       6       0       5
## # … with 46 more rows, and abbreviated variable names ¹​pups_born_alive,
## #   ²​pups_dead_birth, ³​pups_survive
```

Therea re some handy helper functions for `select`; read about all of
them using `?select_helpers`. I use `starts_with()`, `ends_with()`, and
`contain()` often, especially when there are variables named with
suffixes or other standard patterns:
