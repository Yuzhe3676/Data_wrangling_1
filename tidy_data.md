Tidy data
================

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.3     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

## `pivot_longer`

Or say make the table from wide to long Load then PULSE data

``` r
pulse_data =
  haven::read_sas("data/public_pulse_data.sas7bdat") %>%
  janitor::clean_names() 
```

Write format to long format

``` r
pulse_data_tidy =
  pulse_data %>%
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    names_prefix = "bdi_score_", #这是为了省去名字前面的bdi_score_前缀让数据看起来更简洁
    values_to = "bdi"
  )
```

rewrite,combine, and extend (to add a mutate)

``` r
pulse_df =
  haven::read_sas("data/public_pulse_data.sas7bdat") %>% 
  janitor::clean_names() %>% 
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    names_prefix = "bdi_score_",
    values_to = "bdi_score",
  ) %>% 
  relocate(id,visit) %>% ##用relocate将id和visit两列放到最前边
  mutate(visit = recode(visit, "bl" = "00m")) #将visit列中的bl全部替换为00m,p8105.com上针对这一步有不同做法
```

## `pivot_wider`

``` r
analysis_result = 
  tibble(
    group = c("treatment", "treatment", "placebo", "placebo"),
    time = c("pre", "post", "pre", "post"),
    mean = c(4, 8, 3.5, 4)
  )
```

``` r
pivot_wider(
  analysis_result, 
  names_from = "time", 
  values_from = "mean")
```

    ## # A tibble: 2 × 3
    ##   group       pre  post
    ##   <chr>     <dbl> <dbl>
    ## 1 treatment   4       8
    ## 2 placebo     3.5     4

## Binding Rows

Using the LotR data.

First step: import each table

``` r
fellowship_ring = 
  readxl::read_excel("./data/LotR_Words.xlsx", range = "B3:D6") %>% 
  mutate(movie = "fellowship_ring")

two_towers = 
  readxl::read_excel("./data/LotR_Words.xlsx", range = "F3:H6") %>% 
  mutate(movie = "two_towers")

return_king = 
  readxl::read_excel("./data/LotR_Words.xlsx", range = "J3:L6") %>% 
  mutate(movie = "return_king")
```

``` r
lotr_tidy = 
  bind_rows(fellowship_ring, two_towers, return_king) %>% 
  janitor::clean_names() %>% 
  pivot_longer(
    female:male,
    names_to = "gender", 
    values_to = "words") %>% 
  mutate(race = str_to_lower(race)) %>% 
  select(movie, everything()) #或者用relocate() 将movie列提取至第一列
```

\##Joining datasets

Import and clean the FAS datasets

``` r
pup_data = 
  read_csv("./data/FAS_pups.csv") %>% 
  janitor::clean_names() %>% 
  mutate(
    sex = 
      case_match(
        sex, 
        1 ~ "male", 
        2 ~ "female"),
    sex = as.factor(sex)) 
```

    ## Rows: 313 Columns: 6
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): Litter Number
    ## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
##或者使用 mutate(sex = recode(sex, `1`= "male", `2` = "female"))

litter_data = 
  read_csv("./data/FAS_litters.csv") %>%  
  janitor::clean_names() %>% 
  separate(group, into = c("dose", "day_of_tx"), sep = 3) %>% 
  #这行代码的意思是将group这列variable分为两列，因为一般有下滑线的时候r会自动划分，但这里没有，我们需要使用sep指令表示在第三个character后进行划分
  relocate(litter_number) %>% 
  mutate(
    wt_gain = gd18_weight - gd0_weight,
    dose = str_to_lower(dose))
```

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

Next, time to join them! left join- merge whatever in littersdata to
pupdata

``` r
fas_data = 
  left_join(pup_data, litter_data, by = "litter_number") %>% 
  arrange(litter_number) %>% 
  relocate(litter_number, dose, day_of_tx)
```
