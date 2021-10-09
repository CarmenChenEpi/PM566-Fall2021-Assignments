Assignment 2
================
Carmen Chen
10/4/2021

\#\#Step 1: Data Wrangling

\#Download the datasets

``` r
#individual dataset
ind <- "chs_individual.csv"
if (!file.exists(ind))
  download.file("https://raw.githubusercontent.com/USCbiostats/data-science-data/master/01_chs/chs_individual.csv", destfile = ind)

ind <- read.csv(ind)
ind <- as.tibble(ind)
```

    ## Warning: `as.tibble()` was deprecated in tibble 2.0.0.
    ## Please use `as_tibble()` instead.
    ## The signature and semantics have changed, see `?as_tibble`.

``` r
#regional dataset
reg <- "chs_regional.csv"
if (!file.exists(reg))
  download.file("https://raw.githubusercontent.com/USCbiostats/data-science-data/master/01_chs/chs_regional.csv", destfile = reg)

reg <- read.csv(reg)
reg <- as.tibble(reg)
```

\#Merge the data

``` r
data <- merge(
  x = ind,
  y = reg,
  all.x = TRUE,
  all.y = TRUE,
  by.x = "townname",
  by.y = "townname"
)
```

\#Check the dataset

``` r
data %>% nrow()
```

    ## [1] 1200

``` r
colSums(is.na(data))
```

    ##      townname           sid          male          race      hispanic 
    ##             0             0             0             0             0 
    ##        agepft        height        weight           bmi        asthma 
    ##            89            89            89            89            31 
    ## active_asthma father_asthma mother_asthma        wheeze      hayfever 
    ##             0           106            56            71           118 
    ##       allergy   educ_parent         smoke          pets      gasstove 
    ##            63            64            40             0            33 
    ##           fev           fvc          mmef     pm25_mass      pm25_so4 
    ##            95            97           106             0             0 
    ##      pm25_no3      pm25_nh4       pm25_oc       pm25_ec       pm25_om 
    ##             0             0             0             0             0 
    ##       pm10_oc       pm10_ec       pm10_tc        formic        acetic 
    ##             0             0             0             0             0 
    ##           hcl          hno3        o3_max         o3106         o3_24 
    ##             0             0             0             0             0 
    ##           no2          pm10       no_24hr      pm2_5_fr         iacid 
    ##             0             0           100           300             0 
    ##         oacid   total_acids           lon           lat 
    ##             0             0             0             0

``` r
data <- data %>% 
     group_by(male, hispanic) %>%
     mutate(height = replace(height, is.na(height), mean(height, na.rm = TRUE)))

data <- data %>% 
     group_by(male, hispanic) %>%
     mutate(agepft = replace(agepft, is.na(agepft), mean(agepft, na.rm = TRUE)))

data <- data %>% 
     group_by(male, hispanic) %>%
     mutate(weight = replace(weight, is.na(weight), mean(weight, na.rm = TRUE)))

data <- data %>% 
     group_by(male, hispanic) %>%
     mutate(bmi = replace(bmi, is.na(bmi), mean(bmi, na.rm = TRUE)))

data <- data %>% 
     group_by(male, hispanic) %>%
     mutate(asthma  = replace(asthma, is.na(asthma), median(asthma, na.rm = TRUE)))

data <- data %>% 
     group_by(male, hispanic) %>%
     mutate(father_asthma = replace(father_asthma, is.na(father_asthma), median(father_asthma, na.rm = TRUE)))

data <- data %>% 
     group_by(male, hispanic) %>%
     mutate(mother_asthma = replace(mother_asthma, is.na(mother_asthma), median(mother_asthma, na.rm = TRUE)))

data <- data %>% 
     group_by(male, hispanic) %>%
     mutate(wheeze = replace(wheeze, is.na(wheeze), median(wheeze, na.rm = TRUE)))

data <- data %>% 
     group_by(male, hispanic) %>%
     mutate(hayfever = replace(hayfever, is.na(hayfever), median(hayfever, na.rm = TRUE)))

data <- data %>% 
     group_by(male, hispanic) %>%
     mutate(allergy = replace(allergy, is.na(allergy), median(allergy, na.rm = TRUE)))

data <- data %>% 
     group_by(male, hispanic) %>%
     mutate(educ_parent = replace(educ_parent, is.na(educ_parent), median(educ_parent, na.rm = TRUE)))

data <- data %>% 
     group_by(male, hispanic) %>%
     mutate(smoke = replace(smoke, is.na(smoke), median(smoke, na.rm = TRUE)))

data <- data %>% 
     group_by(male, hispanic) %>%
     mutate(gasstove = replace(gasstove, is.na(gasstove), median(gasstove, na.rm = TRUE)))

data <- data %>% 
     group_by(male, hispanic) %>%
     mutate(fev = replace(fev, is.na(fev), mean(fev, na.rm = TRUE)))

data <- data %>% 
     group_by(male, hispanic) %>%
     mutate(fvc = replace(fvc, is.na(fvc), mean(fvc, na.rm = TRUE)))

data <- data %>% 
     group_by(male, hispanic) %>%
     mutate(mmef = replace(mmef, is.na(mmef), mean(mmef, na.rm = TRUE)))

data <- data %>% 
     group_by(male, hispanic) %>%
     mutate(no_24hr = replace(no_24hr, is.na(no_24hr), mean(no_24hr, na.rm = TRUE)))

data <- data %>% 
     group_by(male, hispanic) %>%
     mutate(pm2_5_fr = replace(pm2_5_fr, is.na(pm2_5_fr), mean(pm2_5_fr, na.rm = TRUE)))

colSums(is.na(data))
```

    ##      townname           sid          male          race      hispanic 
    ##             0             0             0             0             0 
    ##        agepft        height        weight           bmi        asthma 
    ##             0             0             0             0             0 
    ## active_asthma father_asthma mother_asthma        wheeze      hayfever 
    ##             0             0             0             0             0 
    ##       allergy   educ_parent         smoke          pets      gasstove 
    ##             0             0             0             0             0 
    ##           fev           fvc          mmef     pm25_mass      pm25_so4 
    ##             0             0             0             0             0 
    ##      pm25_no3      pm25_nh4       pm25_oc       pm25_ec       pm25_om 
    ##             0             0             0             0             0 
    ##       pm10_oc       pm10_ec       pm10_tc        formic        acetic 
    ##             0             0             0             0             0 
    ##           hcl          hno3        o3_max         o3106         o3_24 
    ##             0             0             0             0             0 
    ##           no2          pm10       no_24hr      pm2_5_fr         iacid 
    ##             0             0             0             0             0 
    ##         oacid   total_acids           lon           lat 
    ##             0             0             0             0

\#Creat categorical varialbes

``` r
data$obesity_level <- as.factor(ifelse(data$bmi<14, "underweight",
                        ifelse(data$bmi<=22, "normal",
                               ifelse(data$bmi<=24, "overweight", "obese"))))

tapply(data$bmi, data$obesity_level, summary)
```

    ## $normal
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   14.00   15.78   17.22   17.40   18.63   21.96 
    ## 
    ## $obese
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   24.01   25.07   26.07   26.97   27.77   41.27 
    ## 
    ## $overweight
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   22.02   22.45   22.82   22.94   23.55   24.00 
    ## 
    ## $underweight
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   11.30   13.00   13.45   13.32   13.75   13.99

``` r
data$smoke_gas_exposure <- as.factor(
  ifelse(data$smoke==1 & data$gasstove==1, "Both",           
  ifelse(data$smoke==1 & data$gasstove==0, "Only second hand smoke",
  ifelse(data$smoke==0 & data$gasstove==0, "Neither", "Only gas stove"))))

table(data$smoke_gas_exposure, data$smoke)
```

    ##                         
    ##                            0   1
    ##   Both                     0 154
    ##   Neither                219   0
    ##   Only gas stove         791   0
    ##   Only second hand smoke   0  36

``` r
table(data$smoke_gas_exposure, data$gasstove)
```

    ##                         
    ##                            0   1
    ##   Both                     0 154
    ##   Neither                219   0
    ##   Only gas stove           0 791
    ##   Only second hand smoke  36   0

\#Create summary tables

``` r
library(data.table)
```

    ## 
    ## Attaching package: 'data.table'

    ## The following objects are masked from 'package:dplyr':
    ## 
    ##     between, first, last

    ## The following object is masked from 'package:purrr':
    ## 
    ##     transpose

``` r
#by townname
data %>%
  group_by(townname,asthma) %>%
  summarise(mean_fev = mean(fev, na.rm = TRUE),
            sd_fev = sd(fev, na.rm =TRUE),
            n = n(),
            sd_asthma = sd(asthma, na.rm = TRUE)) %>%
  mutate(prop = prop.table(n)) %>%
  knitr::kable()
```

    ## `summarise()` has grouped output by 'townname'. You can override using the `.groups` argument.

| townname      | asthma | mean\_fev |  sd\_fev |   n | sd\_asthma | prop |
|:--------------|-------:|----------:|---------:|----:|-----------:|-----:|
| Alpine        |      0 |  2087.343 | 290.5699 |  89 |          0 | 0.89 |
| Alpine        |      1 |  2085.141 | 310.4269 |  11 |          0 | 0.11 |
| Atascadero    |      0 |  2052.690 | 307.4435 |  75 |          0 | 0.75 |
| Atascadero    |      1 |  2145.520 | 367.5628 |  25 |          0 | 0.25 |
| Lake Elsinore |      0 |  2041.507 | 302.2584 |  88 |          0 | 0.88 |
| Lake Elsinore |      1 |  2019.359 | 327.1585 |  12 |          0 | 0.12 |
| Lake Gregory  |      0 |  2076.169 | 333.0693 |  85 |          0 | 0.85 |
| Lake Gregory  |      1 |  2133.040 | 235.3165 |  15 |          0 | 0.15 |
| Lancaster     |      0 |  1994.316 | 311.5493 |  84 |          0 | 0.84 |
| Lancaster     |      1 |  2048.871 | 352.1679 |  16 |          0 | 0.16 |
| Lompoc        |      0 |  2019.553 | 336.4375 |  89 |          0 | 0.89 |
| Lompoc        |      1 |  2154.108 | 454.1000 |  11 |          0 | 0.11 |
| Long Beach    |      0 |  2009.417 | 314.1682 |  87 |          0 | 0.87 |
| Long Beach    |      1 |  1828.217 | 321.9581 |  13 |          0 | 0.13 |
| Mira Loma     |      0 |  1979.642 | 334.2006 |  85 |          0 | 0.85 |
| Mira Loma     |      1 |  2016.711 | 274.5174 |  15 |          0 | 0.15 |
| Riverside     |      0 |  1989.006 | 275.9060 |  89 |          0 | 0.89 |
| Riverside     |      1 |  1996.959 | 304.0447 |  11 |          0 | 0.11 |
| San Dimas     |      0 |  2031.537 | 318.2881 |  83 |          0 | 0.83 |
| San Dimas     |      1 |  2003.637 | 330.0140 |  17 |          0 | 0.17 |
| Santa Maria   |      0 |  2025.779 | 302.2363 |  87 |          0 | 0.87 |
| Santa Maria   |      1 |  2025.558 | 386.4252 |  13 |          0 | 0.13 |
| Upland        |      0 |  2044.133 | 350.3748 |  88 |          0 | 0.88 |
| Upland        |      1 |  1878.575 | 250.1978 |  12 |          0 | 0.12 |

``` r
#by sex
data %>%
  group_by(male,asthma) %>%
  summarise(mean_fev = mean(fev, na.rm = TRUE),
            sd_fev = sd(fev, na.rm =TRUE),
            n = n(),
            sd_asthma = sd(asthma, na.rm = TRUE)) %>%
  mutate(prop = prop.table(n)) %>%
  knitr::kable()
```

    ## `summarise()` has grouped output by 'male'. You can override using the `.groups` argument.

| male | asthma | mean\_fev |  sd\_fev |   n | sd\_asthma |      prop |
|-----:|-------:|----------:|---------:|----:|-----------:|----------:|
|    0 |      0 |  1953.544 | 306.4548 | 538 |          0 | 0.8819672 |
|    0 |      1 |  1999.015 | 349.8081 |  72 |          0 | 0.1180328 |
|    1 |      0 |  2111.941 | 304.0853 | 491 |          0 | 0.8322034 |
|    1 |      1 |  2063.348 | 322.5267 |  99 |          0 | 0.1677966 |

``` r
#by obesity level
data %>%
  group_by(obesity_level,asthma) %>%
  summarise(mean_fev = mean(fev, na.rm = TRUE),
            sd_fev = sd(fev, na.rm =TRUE),
            n = n(),
            sd_asthma = sd(asthma, na.rm = TRUE)) %>%
  mutate(prop = prop.table(n)) %>%
  knitr::kable()
```

    ## `summarise()` has grouped output by 'obesity_level'. You can override using the `.groups` argument.

| obesity\_level | asthma | mean\_fev |  sd\_fev |   n | sd\_asthma |      prop |
|:---------------|-------:|----------:|---------:|----:|-----------:|----------:|
| normal         |      0 |  2003.242 | 291.6703 | 842 |          0 | 0.8635897 |
| normal         |      1 |  1977.962 | 316.9062 | 133 |          0 | 0.1364103 |
| obese          |      0 |  2255.617 | 336.0641 |  82 |          0 | 0.7961165 |
| obese          |      1 |  2307.300 | 283.9257 |  21 |          0 | 0.2038835 |
| overweight     |      0 |  2226.277 | 313.9086 |  73 |          0 | 0.8390805 |
| overweight     |      1 |  2214.127 | 347.3845 |  14 |          0 | 0.1609195 |
| underweight    |      0 |  1680.030 | 305.1428 |  32 |          0 | 0.9142857 |
| underweight    |      1 |  1893.504 | 243.1512 |   3 |          0 | 0.0857143 |

``` r
#by smoke_gas_exposure
data %>%
  group_by(smoke_gas_exposure,asthma) %>%
  summarise(mean_fev = mean(fev, na.rm = TRUE),
            sd_fev = sd(fev, na.rm =TRUE),
            n = n(),
            sd_asthma = sd(asthma, na.rm = TRUE)) %>%
  mutate(prop = prop.table(n)) %>%
  knitr::kable()
```

    ## `summarise()` has grouped output by 'smoke_gas_exposure'. You can override using the `.groups` argument.

| smoke\_gas\_exposure   | asthma | mean\_fev |  sd\_fev |   n | sd\_asthma |      prop |
|:-----------------------|-------:|----------:|---------:|----:|-----------:|----------:|
| Both                   |      0 |  2024.646 | 302.1828 | 135 |          0 | 0.8766234 |
| Both                   |      1 |  2025.715 | 297.3824 |  19 |          0 | 0.1233766 |
| Neither                |      0 |  2065.470 | 325.1364 | 188 |          0 | 0.8584475 |
| Neither                |      1 |  2003.467 | 350.9435 |  31 |          0 | 0.1415525 |
| Only gas stove         |      0 |  2017.523 | 316.1165 | 676 |          0 | 0.8546144 |
| Only gas stove         |      1 |  2052.931 | 337.5528 | 115 |          0 | 0.1453856 |
| Only second hand smoke |      0 |  2082.940 | 281.3884 |  30 |          0 | 0.8333333 |
| Only second hand smoke |      1 |  1919.583 | 354.8603 |   6 |          0 | 0.1666667 |

\#\#Step 2: Looking at the Data (EDA)

``` r
head(data)
```

    ## # A tibble: 6 x 51
    ## # Groups:   male, hispanic [3]
    ##   townname   sid  male race  hispanic agepft height weight   bmi asthma
    ##   <chr>    <int> <int> <chr>    <int>  <dbl>  <dbl>  <dbl> <dbl>  <dbl>
    ## 1 Alpine     841     1 W            1  10.5     150     78  15.8      0
    ## 2 Alpine     835     0 W            0  10.1     143     69  15.3      0
    ## 3 Alpine     838     0 O            1   9.49    133     62  15.9      0
    ## 4 Alpine     840     0 W            0   9.97    146     78  16.6      0
    ## 5 Alpine     865     0 W            0  10.0     162    140  24.2      1
    ## 6 Alpine     867     0 W            1   9.96    141     94  21.5      0
    ## # ... with 41 more variables: active_asthma <int>, father_asthma <dbl>,
    ## #   mother_asthma <dbl>, wheeze <dbl>, hayfever <dbl>, allergy <dbl>,
    ## #   educ_parent <dbl>, smoke <dbl>, pets <int>, gasstove <dbl>, fev <dbl>,
    ## #   fvc <dbl>, mmef <dbl>, pm25_mass <dbl>, pm25_so4 <dbl>, pm25_no3 <dbl>,
    ## #   pm25_nh4 <dbl>, pm25_oc <dbl>, pm25_ec <dbl>, pm25_om <dbl>, pm10_oc <dbl>,
    ## #   pm10_ec <dbl>, pm10_tc <dbl>, formic <dbl>, acetic <dbl>, hcl <dbl>,
    ## #   hno3 <dbl>, o3_max <dbl>, o3106 <dbl>, o3_24 <dbl>, no2 <dbl>, pm10 <dbl>,
    ## #   no_24hr <dbl>, pm2_5_fr <dbl>, iacid <dbl>, oacid <dbl>, total_acids <dbl>,
    ## #   lon <dbl>, lat <dbl>, obesity_level <fct>, smoke_gas_exposure <fct>

``` r
tail(data)
```

    ## # A tibble: 6 x 51
    ## # Groups:   male, hispanic [3]
    ##   townname   sid  male race  hispanic agepft height weight   bmi asthma
    ##   <chr>    <int> <int> <chr>    <int>  <dbl>  <dbl>  <dbl> <dbl>  <dbl>
    ## 1 Upland    1866     0 O            1   9.81   139    60    14.1      0
    ## 2 Upland    1867     0 M            1   9.62   140    71    16.5      0
    ## 3 Upland    2033     0 M            0  10.1    130    67    18.0      0
    ## 4 Upland    2031     1 W            0   9.80   135    83    20.7      0
    ## 5 Upland    2032     1 W            0   9.55   137    59    14.3      0
    ## 6 Upland    2053     0 W            0   9.85   139.   77.4  18.1      0
    ## # ... with 41 more variables: active_asthma <int>, father_asthma <dbl>,
    ## #   mother_asthma <dbl>, wheeze <dbl>, hayfever <dbl>, allergy <dbl>,
    ## #   educ_parent <dbl>, smoke <dbl>, pets <int>, gasstove <dbl>, fev <dbl>,
    ## #   fvc <dbl>, mmef <dbl>, pm25_mass <dbl>, pm25_so4 <dbl>, pm25_no3 <dbl>,
    ## #   pm25_nh4 <dbl>, pm25_oc <dbl>, pm25_ec <dbl>, pm25_om <dbl>, pm10_oc <dbl>,
    ## #   pm10_ec <dbl>, pm10_tc <dbl>, formic <dbl>, acetic <dbl>, hcl <dbl>,
    ## #   hno3 <dbl>, o3_max <dbl>, o3106 <dbl>, o3_24 <dbl>, no2 <dbl>, pm10 <dbl>,
    ## #   no_24hr <dbl>, pm2_5_fr <dbl>, iacid <dbl>, oacid <dbl>, total_acids <dbl>,
    ## #   lon <dbl>, lat <dbl>, obesity_level <fct>, smoke_gas_exposure <fct>

``` r
str(data)
```

    ## grouped_df [1,200 x 51] (S3: grouped_df/tbl_df/tbl/data.frame)
    ##  $ townname          : chr [1:1200] "Alpine" "Alpine" "Alpine" "Alpine" ...
    ##  $ sid               : int [1:1200] 841 835 838 840 865 867 842 839 844 847 ...
    ##  $ male              : int [1:1200] 1 0 0 0 0 0 1 0 1 1 ...
    ##  $ race              : chr [1:1200] "W" "W" "O" "W" ...
    ##  $ hispanic          : int [1:1200] 1 0 1 0 0 1 1 1 1 0 ...
    ##  $ agepft            : num [1:1200] 10.55 10.1 9.49 9.97 10.04 ...
    ##  $ height            : num [1:1200] 150 143 133 146 162 141 139 142 143 137 ...
    ##  $ weight            : num [1:1200] 78 69 62 78 140 94 65 86 65 69 ...
    ##  $ bmi               : num [1:1200] 15.8 15.3 15.9 16.6 24.2 ...
    ##  $ asthma            : num [1:1200] 0 0 0 0 1 0 0 0 0 0 ...
    ##  $ active_asthma     : int [1:1200] 0 0 0 0 1 0 0 0 0 0 ...
    ##  $ father_asthma     : num [1:1200] 0 0 0 0 0 0 0 0 0 0 ...
    ##  $ mother_asthma     : num [1:1200] 0 0 0 0 0 0 0 1 0 0 ...
    ##  $ wheeze            : num [1:1200] 0 0 0 0 1 0 1 1 0 0 ...
    ##  $ hayfever          : num [1:1200] 0 0 0 0 0 0 0 1 0 0 ...
    ##  $ allergy           : num [1:1200] 0 1 0 0 1 0 0 1 0 0 ...
    ##  $ educ_parent       : num [1:1200] 5 3 4 3 3 5 1 3 3 5 ...
    ##  $ smoke             : num [1:1200] 0 0 0 0 0 0 1 1 0 0 ...
    ##  $ pets              : int [1:1200] 1 1 1 0 1 1 1 1 0 1 ...
    ##  $ gasstove          : num [1:1200] 0 0 0 1 1 1 0 0 1 1 ...
    ##  $ fev               : num [1:1200] 2252 2529 1738 2467 2584 ...
    ##  $ fvc               : num [1:1200] 2595 2826 1964 2638 3568 ...
    ##  $ mmef              : num [1:1200] 2445 3407 2133 3466 2071 ...
    ##  $ pm25_mass         : num [1:1200] 8.74 8.74 8.74 8.74 8.74 8.74 8.74 8.74 8.74 8.74 ...
    ##  $ pm25_so4          : num [1:1200] 1.73 1.73 1.73 1.73 1.73 1.73 1.73 1.73 1.73 1.73 ...
    ##  $ pm25_no3          : num [1:1200] 1.59 1.59 1.59 1.59 1.59 1.59 1.59 1.59 1.59 1.59 ...
    ##  $ pm25_nh4          : num [1:1200] 0.88 0.88 0.88 0.88 0.88 0.88 0.88 0.88 0.88 0.88 ...
    ##  $ pm25_oc           : num [1:1200] 2.54 2.54 2.54 2.54 2.54 2.54 2.54 2.54 2.54 2.54 ...
    ##  $ pm25_ec           : num [1:1200] 0.48 0.48 0.48 0.48 0.48 0.48 0.48 0.48 0.48 0.48 ...
    ##  $ pm25_om           : num [1:1200] 3.04 3.04 3.04 3.04 3.04 3.04 3.04 3.04 3.04 3.04 ...
    ##  $ pm10_oc           : num [1:1200] 3.25 3.25 3.25 3.25 3.25 3.25 3.25 3.25 3.25 3.25 ...
    ##  $ pm10_ec           : num [1:1200] 0.49 0.49 0.49 0.49 0.49 0.49 0.49 0.49 0.49 0.49 ...
    ##  $ pm10_tc           : num [1:1200] 3.75 3.75 3.75 3.75 3.75 3.75 3.75 3.75 3.75 3.75 ...
    ##  $ formic            : num [1:1200] 1.03 1.03 1.03 1.03 1.03 1.03 1.03 1.03 1.03 1.03 ...
    ##  $ acetic            : num [1:1200] 2.49 2.49 2.49 2.49 2.49 2.49 2.49 2.49 2.49 2.49 ...
    ##  $ hcl               : num [1:1200] 0.41 0.41 0.41 0.41 0.41 0.41 0.41 0.41 0.41 0.41 ...
    ##  $ hno3              : num [1:1200] 1.98 1.98 1.98 1.98 1.98 1.98 1.98 1.98 1.98 1.98 ...
    ##  $ o3_max            : num [1:1200] 65.8 65.8 65.8 65.8 65.8 ...
    ##  $ o3106             : num [1:1200] 55 55 55 55 55 ...
    ##  $ o3_24             : num [1:1200] 41.2 41.2 41.2 41.2 41.2 ...
    ##  $ no2               : num [1:1200] 12.2 12.2 12.2 12.2 12.2 ...
    ##  $ pm10              : num [1:1200] 24.7 24.7 24.7 24.7 24.7 ...
    ##  $ no_24hr           : num [1:1200] 2.48 2.48 2.48 2.48 2.48 2.48 2.48 2.48 2.48 2.48 ...
    ##  $ pm2_5_fr          : num [1:1200] 10.3 10.3 10.3 10.3 10.3 ...
    ##  $ iacid             : num [1:1200] 2.39 2.39 2.39 2.39 2.39 2.39 2.39 2.39 2.39 2.39 ...
    ##  $ oacid             : num [1:1200] 3.52 3.52 3.52 3.52 3.52 3.52 3.52 3.52 3.52 3.52 ...
    ##  $ total_acids       : num [1:1200] 5.5 5.5 5.5 5.5 5.5 5.5 5.5 5.5 5.5 5.5 ...
    ##  $ lon               : num [1:1200] -117 -117 -117 -117 -117 ...
    ##  $ lat               : num [1:1200] 32.8 32.8 32.8 32.8 32.8 ...
    ##  $ obesity_level     : Factor w/ 4 levels "normal","obese",..: 1 1 1 1 2 1 1 1 1 1 ...
    ##  $ smoke_gas_exposure: Factor w/ 4 levels "Both","Neither",..: 2 2 2 3 3 3 4 4 3 3 ...
    ##  - attr(*, "groups")= tibble [4 x 3] (S3: tbl_df/tbl/data.frame)
    ##   ..$ male    : int [1:4] 0 0 1 1
    ##   ..$ hispanic: int [1:4] 0 1 0 1
    ##   ..$ .rows   : list<int> [1:4] 
    ##   .. ..$ : int [1:355] 2 4 5 15 23 27 29 30 33 37 ...
    ##   .. ..$ : int [1:255] 3 6 8 31 43 48 49 51 52 57 ...
    ##   .. ..$ : int [1:324] 10 11 12 14 16 17 18 20 21 22 ...
    ##   .. ..$ : int [1:266] 1 7 9 13 19 26 32 34 35 36 ...
    ##   .. ..@ ptype: int(0) 
    ##   ..- attr(*, ".drop")= logi TRUE

``` r
summary(data$bmi)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   11.30   15.96   17.81   18.50   19.99   41.27

``` r
summary(data$fev)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   984.8  1827.6  2016.4  2030.1  2223.6  3323.7

``` r
summary(data$lat)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   32.84   33.93   34.10   34.20   34.65   35.49

``` r
summary(data$lon)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##  -120.7  -118.8  -117.7  -118.3  -117.4  -116.8

``` r
table(data$townname)
```

    ## 
    ##        Alpine    Atascadero Lake Elsinore  Lake Gregory     Lancaster 
    ##           100           100           100           100           100 
    ##        Lompoc    Long Beach     Mira Loma     Riverside     San Dimas 
    ##           100           100           100           100           100 
    ##   Santa Maria        Upland 
    ##           100           100

``` r
table(data$obesity_level)
```

    ## 
    ##      normal       obese  overweight underweight 
    ##         975         103          87          35

``` r
table(data$smoke_gas_exposure)
```

    ## 
    ##                   Both                Neither         Only gas stove 
    ##                    154                    219                    791 
    ## Only second hand smoke 
    ##                     36

1.  Facet plot showing scatterplots with regression lines of BMI vs FEV
    by “townname”.

``` r
library(ggplot2)
ggplot(data) + 
  geom_point(mapping = aes(x = bmi, y = fev, color = townname)) + 
  geom_smooth(mapping = aes(x = bmi, y = fev, linetype = townname)) + 
  facet_wrap(~townname) +
  ggtitle("Scatterplots of BMI vs FEV by “townname”")
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](README_files/figure-gfm/facet%20plot-1.png)<!-- -->

Overall, there is a positive association of BMI and FEV. However, in
Lompoc and San Dimas, we see a negative association of BMI and FEV when
the BMI value is over 25. Such observed association may be due to the
influence of outliers.

2.  Stacked histograms of FEV by BMI category and FEV by smoke/gas
    exposure. Use different color schemes than the ggplot default.

``` r
#by obesity level
ggplot(data, aes(x = fev)) +
  geom_histogram(aes(fill = obesity_level)) +
  ggtitle("Stacked histograms of FEV by BMI category")
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](README_files/figure-gfm/histograms-1.png)<!-- -->

``` r
#by smoke/gas exposure
ggplot(data, aes(x = fev)) +
  geom_histogram(aes(fill = smoke_gas_exposure)) +
  ggtitle("Stacked histograms of FEV by smoke/gas exposure")
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](README_files/figure-gfm/histograms-2.png)<!-- -->

In nomal weight group, the FEV value is normally distributed. However we
see a right skewed pattern of FEV value among obese and overweight
people, as well as a left skewed pattern of FEV value among underweight
people.

The FEV value is normally distributed across people with different smoke
and gas exposure groups.

3.  Barchart of BMI by smoke/gas exposure.

``` r
ggplot(data) +
  geom_bar(mapping = aes(x = obesity_level, fill = smoke_gas_exposure)) +
  ggtitle("Barchart of BMI by smoke/gas exposure")
```

![](README_files/figure-gfm/barchart-1.png)<!-- -->

Across people with different obesity level, those with both smoke and
gas exposure have the largest counts, followed by neither smoke or gas
exposure, only gas exposure, and only second hand smoke exposure.

4.  Statistical summary graphs of FEV by BMI and FEV by smoke/gas
    exposure category.

``` r
#boxplot of FEV by obesity level
ggplot(data) +
  geom_boxplot(mapping = aes(x = obesity_level, y = fev)) +
  ggtitle("boxplot of FEV by obesity level")
```

![](README_files/figure-gfm/boxplot-1.png)<!-- -->

``` r
#boxplot of FEV by smoke/gas exposure category
ggplot(data) +
  geom_boxplot(mapping = aes(x = smoke_gas_exposure, y = fev)) +
  ggtitle("boxplot of FEV by smoke/gas exposure category")
```

![](README_files/figure-gfm/boxplot-2.png)<!-- -->

The obese people have the largest mean FEV values, while the underweight
people have the smallest mean FEV values. The FEV values do not differ
across different smoke and gas exposure groups.

5.  A leaflet map showing the concentrations of PM2.5 mass in each of
    the CHS communities.

``` r
library(leaflet)

pm25.pal <- colorNumeric(c('darkgreen', 'goldenrod', 'brown'), domain=data$pm25_mass)

leaflet(data) %>%
  addProviderTiles('CartoDB.Positron') %>%
  addCircles(
    lat = ~lat, lng = ~lon,
    label = ~paste0(round(pm25_mass, 2), '  PM2.5 mass'), color = ~ pm25.pal(pm25_mass),
    opacity = 1, fillOpacity = 1, radius = 500
    ) %>%
  addLegend('bottomleft', pal=pm25.pal, values=data$pm25_mass,
          title='PM2.5 mass', opacity=1) %>%
  addTitle("Leaflet map of the concentrations of PM2.5 mass in each of the CHS")
```

The cities in the Los Angeles county, such as Long Beach, Pasadena, and
Riverside, have the largest PM2.5 mass.

6.  Choose a visualization to examine whether PM2.5 mass is associated
    with FEV.

``` r
#general
ggplot(data, mapping = aes(x = pm25_mass, y = fev)) + 
  geom_point() + 
  geom_smooth() +
  ggtitle("Scatter and line lot of PM2.5 and FEV")
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

![](README_files/figure-gfm/PM2.5%20mass%20&%20FEV-1.png)<!-- -->

``` r
#by sex
data$sex <- ifelse(data$male == 0, "female", "male")
ggplot(data, mapping = aes(x = pm25_mass, y = fev, color = sex, linetype = sex)) + 
  geom_point() + 
  geom_smooth() +
  ggtitle("Scatter and line lot of PM2.5 and FEV by sex")
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](README_files/figure-gfm/PM2.5%20mass%20&%20FEV-2.png)<!-- -->

There is no significant association of PM2.5 mass and FEV.
