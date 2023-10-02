Data Manipulation
================

本节主要讲解使用tidyvers中的dplyr package来进行data manipulation

``` r
library(tidyverse)#tidyverse本身并不是一个package，而是一个collection of package
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

## Load in the FAS Litters Data

``` r
litters_df = read_csv("./data/FAS_litters.csv")
```

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
litters_df = janitor::clean_names(litters_df)
```

## `select`

Choose some columns and not others.

``` r
select(litters_df, group, gd0_weight)
```

    ## # A tibble: 49 × 2
    ##    group gd0_weight
    ##    <chr>      <dbl>
    ##  1 Con7        19.7
    ##  2 Con7        27  
    ##  3 Con7        26  
    ##  4 Con7        28.5
    ##  5 Con7        NA  
    ##  6 Con7        NA  
    ##  7 Con7        NA  
    ##  8 Con8        NA  
    ##  9 Con8        NA  
    ## 10 Con8        28.5
    ## # ℹ 39 more rows

冒号表示选这两列之间的所有列

``` r
select(litters_df, group, gd0_weight:gd_of_birth)
```

    ## # A tibble: 49 × 4
    ##    group gd0_weight gd18_weight gd_of_birth
    ##    <chr>      <dbl>       <dbl>       <dbl>
    ##  1 Con7        19.7        34.7          20
    ##  2 Con7        27          42            19
    ##  3 Con7        26          41.4          19
    ##  4 Con7        28.5        44.1          19
    ##  5 Con7        NA          NA            20
    ##  6 Con7        NA          NA            20
    ##  7 Con7        NA          NA            20
    ##  8 Con8        NA          NA            20
    ##  9 Con8        NA          NA            20
    ## 10 Con8        28.5        NA            20
    ## # ℹ 39 more rows

减号表示删除某列

``` r
select(litters_df, -litter_number)
```

    ## # A tibble: 49 × 7
    ##    group gd0_weight gd18_weight gd_of_birth pups_born_alive pups_dead_birth
    ##    <chr>      <dbl>       <dbl>       <dbl>           <dbl>           <dbl>
    ##  1 Con7        19.7        34.7          20               3               4
    ##  2 Con7        27          42            19               8               0
    ##  3 Con7        26          41.4          19               6               0
    ##  4 Con7        28.5        44.1          19               5               1
    ##  5 Con7        NA          NA            20               6               0
    ##  6 Con7        NA          NA            20               6               0
    ##  7 Con7        NA          NA            20               9               0
    ##  8 Con8        NA          NA            20               9               1
    ##  9 Con8        NA          NA            20               8               0
    ## 10 Con8        28.5        NA            20               8               0
    ## # ℹ 39 more rows
    ## # ℹ 1 more variable: pups_survive <dbl>

Renaming columns

下面这个表示提取这两列，同时将这两列另外命名

``` r
select(litters_df, GROUP = group, LITTER_NUMBER = litter_number)
```

    ## # A tibble: 49 × 2
    ##    GROUP LITTER_NUMBER  
    ##    <chr> <chr>          
    ##  1 Con7  #85            
    ##  2 Con7  #1/2/95/2      
    ##  3 Con7  #5/5/3/83/3-3  
    ##  4 Con7  #5/4/2/95/2    
    ##  5 Con7  #4/2/95/3-3    
    ##  6 Con7  #2/2/95/3-2    
    ##  7 Con7  #1/5/3/83/3-3/2
    ##  8 Con8  #3/83/3-3      
    ##  9 Con8  #2/95/3        
    ## 10 Con8  #3/5/2/2/95    
    ## # ℹ 39 more rows

如果不想单独提出来列，只想改变某些列的名字

``` r
rename(litters_df, GROUP = group, LITTER_NUMBER = litter_number)
```

    ## # A tibble: 49 × 8
    ##    GROUP LITTER_NUMBER   gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>                <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Con7  #85                   19.7        34.7          20               3
    ##  2 Con7  #1/2/95/2             27          42            19               8
    ##  3 Con7  #5/5/3/83/3-3         26          41.4          19               6
    ##  4 Con7  #5/4/2/95/2           28.5        44.1          19               5
    ##  5 Con7  #4/2/95/3-3           NA          NA            20               6
    ##  6 Con7  #2/2/95/3-2           NA          NA            20               6
    ##  7 Con7  #1/5/3/83/3-3/2       NA          NA            20               9
    ##  8 Con8  #3/83/3-3             NA          NA            20               9
    ##  9 Con8  #2/95/3               NA          NA            20               8
    ## 10 Con8  #3/5/2/2/95           28.5        NA            20               8
    ## # ℹ 39 more rows
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

Select Helpers

``` r
select(litters_df, starts_with("gd"))#选取以gd开头的列
```

    ## # A tibble: 49 × 3
    ##    gd0_weight gd18_weight gd_of_birth
    ##         <dbl>       <dbl>       <dbl>
    ##  1       19.7        34.7          20
    ##  2       27          42            19
    ##  3       26          41.4          19
    ##  4       28.5        44.1          19
    ##  5       NA          NA            20
    ##  6       NA          NA            20
    ##  7       NA          NA            20
    ##  8       NA          NA            20
    ##  9       NA          NA            20
    ## 10       28.5        NA            20
    ## # ℹ 39 more rows

``` r
select(litters_df,  litter_number, everything())# 将litternumber提到第一列，其他列跟在后边
```

    ## # A tibble: 49 × 8
    ##    litter_number   group gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr>           <chr>      <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 #85             Con7        19.7        34.7          20               3
    ##  2 #1/2/95/2       Con7        27          42            19               8
    ##  3 #5/5/3/83/3-3   Con7        26          41.4          19               6
    ##  4 #5/4/2/95/2     Con7        28.5        44.1          19               5
    ##  5 #4/2/95/3-3     Con7        NA          NA            20               6
    ##  6 #2/2/95/3-2     Con7        NA          NA            20               6
    ##  7 #1/5/3/83/3-3/2 Con7        NA          NA            20               9
    ##  8 #3/83/3-3       Con8        NA          NA            20               9
    ##  9 #2/95/3         Con8        NA          NA            20               8
    ## 10 #3/5/2/2/95     Con8        28.5        NA            20               8
    ## # ℹ 39 more rows
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

``` r
select(litters_df,  litter_number)#这个命令也可以将litternumber提到第一列，默认放在第一列，也可以指定
```

    ## # A tibble: 49 × 1
    ##    litter_number  
    ##    <chr>          
    ##  1 #85            
    ##  2 #1/2/95/2      
    ##  3 #5/5/3/83/3-3  
    ##  4 #5/4/2/95/2    
    ##  5 #4/2/95/3-3    
    ##  6 #2/2/95/3-2    
    ##  7 #1/5/3/83/3-3/2
    ##  8 #3/83/3-3      
    ##  9 #2/95/3        
    ## 10 #3/5/2/2/95    
    ## # ℹ 39 more rows

## `filter`

select是针对列的选择，filter是针对行的选择

``` r
filter(litters_df, gd0_weight < 22) 
```

    ## # A tibble: 8 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Mod7  #59                 17          33.4          19               8
    ## 3 Mod7  #103                21.4        42.1          19               9
    ## 4 Mod7  #106                21.7        37.8          20               5
    ## 5 Mod7  #62                 19.5        35.9          19               7
    ## 6 Low8  #53                 21.8        37.2          20               8
    ## 7 Low8  #100                20          39.2          20               8
    ## 8 Low8  #4/84               21.8        35.2          20               4
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

``` r
filter(litters_df, gd0_weight >= 22) 
```

    ## # A tibble: 26 × 8
    ##    group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Con7  #1/2/95/2           27          42            19               8
    ##  2 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ##  3 Con7  #5/4/2/95/2         28.5        44.1          19               5
    ##  4 Con8  #3/5/2/2/95         28.5        NA            20               8
    ##  5 Con8  #5/4/3/83/3         28          NA            19               9
    ##  6 Mod7  #3/82/3-2           28          45.9          20               5
    ##  7 Mod7  #4/2/95/2           23.5        NA            19               9
    ##  8 Mod7  #5/3/83/5-2         22.6        37            19               5
    ##  9 Mod7  #94/2               24.4        42.9          19               7
    ## 10 Low7  #84/2               24.3        40.8          20               8
    ## # ℹ 16 more rows
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

``` r
filter(litters_df, gd_of_birth == 20) #这里用两个等号表示询问和筛选，如果只有一个等号表示赋值
```

    ## # A tibble: 32 × 8
    ##    group litter_number   gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>                <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Con7  #85                   19.7        34.7          20               3
    ##  2 Con7  #4/2/95/3-3           NA          NA            20               6
    ##  3 Con7  #2/2/95/3-2           NA          NA            20               6
    ##  4 Con7  #1/5/3/83/3-3/2       NA          NA            20               9
    ##  5 Con8  #3/83/3-3             NA          NA            20               9
    ##  6 Con8  #2/95/3               NA          NA            20               8
    ##  7 Con8  #3/5/2/2/95           28.5        NA            20               8
    ##  8 Con8  #1/6/2/2/95-2         NA          NA            20               7
    ##  9 Con8  #3/5/3/83/3-3-2       NA          NA            20               8
    ## 10 Con8  #3/6/2/2/95-3         NA          NA            20               7
    ## # ℹ 22 more rows
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

``` r
filter(litters_df, !(gd_of_birth == 20) ) #叹号表示选出不等于这个数的这些行
```

    ## # A tibble: 17 × 8
    ##    group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Con7  #1/2/95/2           27          42            19               8
    ##  2 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ##  3 Con7  #5/4/2/95/2         28.5        44.1          19               5
    ##  4 Con8  #5/4/3/83/3         28          NA            19               9
    ##  5 Con8  #2/2/95/2           NA          NA            19               5
    ##  6 Mod7  #59                 17          33.4          19               8
    ##  7 Mod7  #103                21.4        42.1          19               9
    ##  8 Mod7  #1/82/3-2           NA          NA            19               6
    ##  9 Mod7  #3/83/3-2           NA          NA            19               8
    ## 10 Mod7  #4/2/95/2           23.5        NA            19               9
    ## 11 Mod7  #5/3/83/5-2         22.6        37            19               5
    ## 12 Mod7  #94/2               24.4        42.9          19               7
    ## 13 Mod7  #62                 19.5        35.9          19               7
    ## 14 Low7  #112                23.9        40.5          19               6
    ## 15 Mod8  #5/93/2             NA          NA            19               8
    ## 16 Mod8  #7/110/3-2          27.5        46            19               8
    ## 17 Low8  #79                 25.4        43.8          19               8
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

``` r
filter(litters_df, gd_of_birth != 20) #也可以用这种方式进行选择
```

    ## # A tibble: 17 × 8
    ##    group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Con7  #1/2/95/2           27          42            19               8
    ##  2 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ##  3 Con7  #5/4/2/95/2         28.5        44.1          19               5
    ##  4 Con8  #5/4/3/83/3         28          NA            19               9
    ##  5 Con8  #2/2/95/2           NA          NA            19               5
    ##  6 Mod7  #59                 17          33.4          19               8
    ##  7 Mod7  #103                21.4        42.1          19               9
    ##  8 Mod7  #1/82/3-2           NA          NA            19               6
    ##  9 Mod7  #3/83/3-2           NA          NA            19               8
    ## 10 Mod7  #4/2/95/2           23.5        NA            19               9
    ## 11 Mod7  #5/3/83/5-2         22.6        37            19               5
    ## 12 Mod7  #94/2               24.4        42.9          19               7
    ## 13 Mod7  #62                 19.5        35.9          19               7
    ## 14 Low7  #112                23.9        40.5          19               6
    ## 15 Mod8  #5/93/2             NA          NA            19               8
    ## 16 Mod8  #7/110/3-2          27.5        46            19               8
    ## 17 Low8  #79                 25.4        43.8          19               8
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

``` r
filter(litters_df, gd0_weight >= 22, gd_of_birth == 20) 
```

    ## # A tibble: 16 × 8
    ##    group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Con8  #3/5/2/2/95         28.5        NA            20               8
    ##  2 Mod7  #3/82/3-2           28          45.9          20               5
    ##  3 Low7  #84/2               24.3        40.8          20               8
    ##  4 Low7  #107                22.6        42.4          20               9
    ##  5 Low7  #85/2               22.2        38.5          20               8
    ##  6 Low7  #98                 23.8        43.8          20               9
    ##  7 Low7  #102                22.6        43.3          20              11
    ##  8 Low7  #101                23.8        42.7          20               9
    ##  9 Low7  #111                25.5        44.6          20               3
    ## 10 Mod8  #97                 24.5        42.8          20               8
    ## 11 Mod8  #7/82-3-2           26.9        43.2          20               7
    ## 12 Mod8  #2/95/2             28.5        44.5          20               9
    ## 13 Mod8  #82/4               33.4        52.7          20               8
    ## 14 Low8  #108                25.6        47.5          20               8
    ## 15 Low8  #99                 23.5        39            20               6
    ## 16 Low8  #110                25.5        42.7          20               7
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

``` r
filter(litters_df, group == "Mod8")
```

    ## # A tibble: 7 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Mod8  #97                 24.5        42.8          20               8
    ## 2 Mod8  #5/93               NA          41.1          20              11
    ## 3 Mod8  #5/93/2             NA          NA            19               8
    ## 4 Mod8  #7/82-3-2           26.9        43.2          20               7
    ## 5 Mod8  #7/110/3-2          27.5        46            19               8
    ## 6 Mod8  #2/95/2             28.5        44.5          20               9
    ## 7 Mod8  #82/4               33.4        52.7          20               8
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

同时选con7和mod8的数据

``` r
filter(litters_df, group %in% c("Con7","Mod8"))
```

    ## # A tibble: 14 × 8
    ##    group litter_number   gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>                <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Con7  #85                   19.7        34.7          20               3
    ##  2 Con7  #1/2/95/2             27          42            19               8
    ##  3 Con7  #5/5/3/83/3-3         26          41.4          19               6
    ##  4 Con7  #5/4/2/95/2           28.5        44.1          19               5
    ##  5 Con7  #4/2/95/3-3           NA          NA            20               6
    ##  6 Con7  #2/2/95/3-2           NA          NA            20               6
    ##  7 Con7  #1/5/3/83/3-3/2       NA          NA            20               9
    ##  8 Mod8  #97                   24.5        42.8          20               8
    ##  9 Mod8  #5/93                 NA          41.1          20              11
    ## 10 Mod8  #5/93/2               NA          NA            19               8
    ## 11 Mod8  #7/82-3-2             26.9        43.2          20               7
    ## 12 Mod8  #7/110/3-2            27.5        46            19               8
    ## 13 Mod8  #2/95/2               28.5        44.5          20               9
    ## 14 Mod8  #82/4                 33.4        52.7          20               8
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>
