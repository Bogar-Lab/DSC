pH_datasheet_creation_sept
================

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.2.1     ✔ readr     2.2.0
    ## ✔ forcats   1.0.1     ✔ stringr   1.6.0
    ## ✔ ggplot2   4.0.3     ✔ tibble    3.3.1
    ## ✔ lubridate 1.9.5     ✔ tidyr     1.3.2
    ## ✔ purrr     1.2.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
psm_sept = read_csv("../../../TidyData/Metadata/make_per_sample_metadata_september/Results/psm_sept_tidied-20261014.csv")
```

    ## New names:
    ## Rows: 96 Columns: 48
    ## ── Column specification
    ## ──────────────────────────────────────────────────────── Delimiter: "," chr
    ## (32): Sample_ID, Scribe_initials, Siever_1_initials, Siever_2_initials,... dbl
    ## (7): ...1, S_pH_mass_g, large_tin_mass_full_g_1, large_tin_mass_full_g... lgl
    ## (3): Siever_3_initials, large_tin_mass_full_g_3, large_tin_mass_full_g_4 time
    ## (6): subsampling_start_time, sieving_start_time, sieving_end_time, all...
    ## ℹ Use `spec()` to retrieve the full column specification for this data. ℹ
    ## Specify the column types or set `show_col_types = FALSE` to quiet this message.
    ## • `` -> `...1`

# Pull masses of soil for pH from per-sample metadata

``` r
# make a copy of the per-sample metadata which only has the Sample_IDs and masses of soil set aside for pH
pH_tube_info = select(psm_sept, c(Sample_ID, S_pH_mass_g, pH_mass_g_soil_only)) %>%
  
# compute and add the following columns: 
  mutate(
    # the average mass of empty, labeled 15mL tubes, which I measured in June
         avg_mass_15mL_tube_g = 7.32,  
    # the approximate or measured mass of soil set aside for pH
         approx_soil_mass_g = case_when(
           # for YZ samples, we didn't tare the tubes. the soil + labeled 15mL tube mass was recorded as S_pH_mass_g, so approx soil mass is S_pH_mass_g minus the avg tube mass
           !(is.na(S_pH_mass_g)) ~ (S_pH_mass_g - avg_mass_15mL_tube_g),
           # for OX samples, (I think? I wasn't there) the recorded mass is soil only (tubes were tared, I assume). So no computation
           is.na(S_pH_mass_g) ~ pH_mass_g_soil_only),
    # the ratio of 0.01M CaCl2 mass (approximated as vol, density similar to water) to soil mass planned for the pH measurement
         calcl2_to_soil_mass_ratio = case_when(
           approx_soil_mass_g > 2.5 ~ 2, # 2:1 ratio
           2.5 >= approx_soil_mass_g ~ 5 # 5:1 ratio
           ),
    # the volume of CaCl2 to use for pH measurement
          vol_CaCl2 = approx_soil_mass_g * calcl2_to_soil_mass_ratio
          )

head(pH_tube_info)
```

    ## # A tibble: 6 × 7
    ##   Sample_ID S_pH_mass_g pH_mass_g_soil_only avg_mass_15mL_tube_g
    ##   <chr>           <dbl>               <dbl>                <dbl>
    ## 1 DSC_352          12.2                  NA                 7.32
    ## 2 DSC_329          11.2                  NA                 7.32
    ## 3 DSC_335          11.8                  NA                 7.32
    ## 4 DSC_337          11.3                  NA                 7.32
    ## 5 DSC_259          11.5                  NA                 7.32
    ## 6 DSC_331          10.4                  NA                 7.32
    ## # ℹ 3 more variables: approx_soil_mass_g <dbl>,
    ## #   calcl2_to_soil_mass_ratio <dbl>, vol_CaCl2 <dbl>

## Just curious, what’s the standard deviation of the tube masses?

``` r
# from my notes in google drive file empty_vessel_masses_AG.pdf, from 10 June 2026

g15mL = c(7.350, 7.307, 7.3025, 7.35209, 7.296, 7.325)

mean(g15mL)
```

    ## [1] 7.322098

``` r
sd(g15mL)
```

    ## [1] 0.02441147

``` r
max(g15mL) - mean(g15mL)
```

    ## [1] 0.02999167

``` r
mean(g15mL) - min(g15mL)
```

    ## [1] 0.02609833

``` r
biggestVar = max(g15mL) - mean(g15mL)
```

If all our tubes had that biggest variation, how different would our
ratios be?

``` r
hypothetical = pH_tube_info %>%
  mutate(
    avg_mass_15mL_tube_g = max(g15mL),
    approx_soil_mass_g = case_when(
           # recalculate these approximate masses
           !(is.na(S_pH_mass_g)) ~ (S_pH_mass_g - avg_mass_15mL_tube_g),
           .default = approx_soil_mass_g #no recalc for pre-tared values
    ),
    hypothetical_enacted_ratio = vol_CaCl2/approx_soil_mass_g,
    ratio_off_by = hypothetical_enacted_ratio - calcl2_to_soil_mass_ratio
  )

head(hypothetical %>% select(calcl2_to_soil_mass_ratio, hypothetical_enacted_ratio, ratio_off_by))
```

    ## # A tibble: 6 × 3
    ##   calcl2_to_soil_mass_ratio hypothetical_enacted_ratio ratio_off_by
    ##                       <dbl>                      <dbl>        <dbl>
    ## 1                         2                       2.01       0.0132
    ## 2                         2                       2.02       0.0165
    ## 3                         2                       2.01       0.0143
    ## 4                         2                       2.02       0.0161
    ## 5                         2                       2.02       0.0154
    ## 6                         2                       2.02       0.0214

``` r
hypothetical$ratio_off_by
```

    ##  [1] 0.01318430 0.01650758 0.01433258 0.01609364 0.01539861 0.02140825
    ##  [7] 0.01642310 0.01547285 0.01380836 0.01759364 0.01788785 0.01952000
    ## [13] 0.01613410 0.01294497 0.01204600 0.01911308 0.01536175 0.01629799
    ## [19] 0.01642310 0.01423720 0.02423798 0.02285686 0.01569995 0.01472724
    ## [25] 0.01861417 0.01449442 0.01554782 0.01465996 0.01112708 0.01256483
    ## [31] 0.01976040 0.02019566 0.01493284 0.01759364 0.01543564 0.01667918
    ## [37] 0.01417431 0.01496766 0.01482933 0.01672264 0.01459330 0.01496766
    ## [43] 0.01532507 0.01171615 0.01659294 0.01408102 0.01452723 0.01346082
    ## [49] 0.01500265 0.01759364 0.01472724 0.01369054 0.01291891 0.01759364
    ## [55] 0.00000000 0.00000000 0.00000000 0.00000000 0.00000000 0.00000000
    ## [61] 0.00000000 0.00000000 0.00000000 0.00000000 0.00000000 0.00000000
    ## [67] 0.00000000 0.00000000 0.00000000 0.00000000 0.00000000 0.00000000
    ## [73] 0.00000000 0.00000000 0.00000000 0.00000000 0.00000000 0.00000000
    ## [79] 0.00000000 0.00000000 0.00000000 0.00000000 0.00000000 0.00000000
    ## [85] 0.00000000 0.00000000 0.00000000 0.00000000 0.00000000 0.00000000
    ## [91] 0.00000000 0.00000000 0.00000000 0.00000000 0.00000000 0.00000000

this seems not too bad! Like it’s within the margins of pipetting error
anyway

check if this is within the margins of pipetting error

``` r
pipetting = hypothetical %>% 
  mutate(
    #if the tube masses were all = the average, and we created this same ammount of error through pipetting, we would need to over- or under-pipette by what volume?
    hypo_vol_added = hypothetical_enacted_ratio * approx_soil_mass_g,
    #and how different is that than what what we're planning?
    hypo_pipetting_error = (hypo_vol_added - vol_CaCl2)/1000 # put it in uL not mL
  )

pipetting$hypo_pipetting_error 
```

    ##  [1]  0.000000e+00 -8.881784e-19  0.000000e+00  0.000000e+00  0.000000e+00
    ##  [6]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00  8.881784e-19
    ## [11]  8.881784e-19 -8.881784e-19  0.000000e+00  0.000000e+00  0.000000e+00
    ## [16] -8.881784e-19  0.000000e+00  8.881784e-19  0.000000e+00  0.000000e+00
    ## [21]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00
    ## [26]  0.000000e+00  0.000000e+00  0.000000e+00  1.776357e-18  1.776357e-18
    ## [31]  0.000000e+00  0.000000e+00  0.000000e+00  8.881784e-19  0.000000e+00
    ## [36] -8.881784e-19  0.000000e+00  0.000000e+00  0.000000e+00 -8.881784e-19
    ## [41]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00 -8.881784e-19
    ## [46]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00  8.881784e-19
    ## [51]  0.000000e+00  0.000000e+00  0.000000e+00  8.881784e-19  0.000000e+00
    ## [56]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00
    ## [61]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00
    ## [66]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00
    ## [71]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00
    ## [76]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00
    ## [81]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00
    ## [86]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00
    ## [91]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00
    ## [96]  0.000000e+00

ok that’s soooo tiny. We are definatly creating waaaay more error
through pipetting than we are creating by using this average tube mass.
our seriological pipette is only accurate to the nearest 100uL

# Ok let’s make the datasheet draft

``` r
colnames(pH_tube_info)
```

    ## [1] "Sample_ID"                 "S_pH_mass_g"              
    ## [3] "pH_mass_g_soil_only"       "avg_mass_15mL_tube_g"     
    ## [5] "approx_soil_mass_g"        "calcl2_to_soil_mass_ratio"
    ## [7] "vol_CaCl2"

``` r
draft_datasheet = pH_tube_info %>%
  # clarify column names
  rename(mass_soil_plus_tube_g = S_pH_mass_g,
         mass_soil_original_measurement_g = pH_mass_g_soil_only,
         calculated_CaCl2_mL = vol_CaCl2,
         soil_mass_g = approx_soil_mass_g) %>%
  # add blank columns
  mutate(pipetteable_CaCl2_mL = round(calculated_CaCl2_mL, 1),
         pH_first_measurement = NA,
         pH_second_measurement = NA,
         date = NA,
         time = NA,
         notes = NA) %>%
  select(Sample_ID, date, time, everything()) # put these columns first
```

Considering ordering this sheet by amount leftover and then Sample_ID,
but currently it’s set up as YZ, X, O and maybe that’s good enough

``` r
write_csv(draft_datasheet, "Results/sept_pH_blank_datasheet.csv")
```
