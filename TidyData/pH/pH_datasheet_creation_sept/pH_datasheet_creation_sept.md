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
           approx_soil_mass_g >= 2 ~ 2.5, # 2:1 ratio
           2 > approx_soil_mass_g ~ 5 # 5:1 ratio
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
    ## 1                       2.5                       2.52       0.0165
    ## 2                       2.5                       2.52       0.0206
    ## 3                       2.5                       2.52       0.0179
    ## 4                       2.5                       2.52       0.0201
    ## 5                       2.5                       2.52       0.0192
    ## 6                       2.5                       2.53       0.0268

``` r
hypothetical$ratio_off_by
```

    ##  [1] 0.01648038 0.02063448 0.01791572 0.02011705 0.01924826 0.02676031
    ##  [7] 0.02052888 0.01934107 0.01726045 0.02199204 0.02235981 0.02440000
    ## [13] 0.02016763 0.01618121 0.01505750 0.02389135 0.01920218 0.02037248
    ## [19] 0.02052888 0.01779650 0.03029748 0.02857107 0.01962494 0.01840905
    ## [25] 0.02326772 0.01811803 0.01943477 0.01832495 0.01390885 0.01570603
    ## [31] 0.02470050 0.02524458 0.01866605 0.02199204 0.01929455 0.02084898
    ## [37] 0.01771789 0.01870958 0.01853666 0.02090330 0.01824162 0.01870958
    ## [43] 0.01915633 0.01464518 0.02074118 0.01760127 0.01815904 0.01682603
    ## [49] 0.01875332 0.02199204 0.01840905 0.01711317 0.01614864 0.02199204
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

    ##  [1]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00
    ##  [6]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00
    ## [11]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00
    ## [16]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00
    ## [21]  8.881784e-19  0.000000e+00  0.000000e+00 -1.776357e-18  0.000000e+00
    ## [26]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00
    ## [31]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00
    ## [36]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00
    ## [41]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00
    ## [46]  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00
    ## [51] -1.776357e-18  0.000000e+00  0.000000e+00  0.000000e+00  0.000000e+00
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

how many can I do with 100mL of solution

``` r
x = draft_datasheet$pipetteable_CaCl2_mL[1]

for (i in 1:length(draft_datasheet$pipetteable_CaCl2_mL)) {
  if (x < 100) {
    x = x+ draft_datasheet$pipetteable_CaCl2_mL[i]
  } else if (x >= 100){
    print(i-1)
    print(draft_datasheet$Sample_ID[i-1])
    print(x - draft_datasheet$pipetteable_CaCl2_mL[i])
      }
  }
```

    ## [1] 9
    ## [1] "DSC_359"
    ## [1] 96.3
    ## [1] 10
    ## [1] "DSC_349"
    ## [1] 96.5
    ## [1] 11
    ## [1] "DSC_340"
    ## [1] 97.2
    ## [1] 12
    ## [1] "DSC_333"
    ## [1] 95.5
    ## [1] 13
    ## [1] "DSC_350"
    ## [1] 93
    ## [1] 14
    ## [1] "DSC_334"
    ## [1] 92.1
    ## [1] 15
    ## [1] "DSC_261"
    ## [1] 97
    ## [1] 16
    ## [1] "DSC_355"
    ## [1] 95
    ## [1] 17
    ## [1] "DSC_356"
    ## [1] 95.6
    ## [1] 18
    ## [1] "DSC_343"
    ## [1] 95.7
    ## [1] 19
    ## [1] "DSC_348"
    ## [1] 94.2
    ## [1] 20
    ## [1] "DSC_252"
    ## [1] 98.8
    ## [1] 21
    ## [1] "DSC_240"
    ## [1] 98.4
    ## [1] 22
    ## [1] "DSC_251"
    ## [1] 95.2
    ## [1] 23
    ## [1] "DSC_245"
    ## [1] 94.5
    ## [1] 24
    ## [1] "DSC_353"
    ## [1] 96.8
    ## [1] 25
    ## [1] "DSC_250"
    ## [1] 94.4
    ## [1] 26
    ## [1] "DSC_336"
    ## [1] 95.1
    ## [1] 27
    ## [1] "DSC_358"
    ## [1] 94.5
    ## [1] 28
    ## [1] "DSC_338"
    ## [1] 91
    ## [1] 29
    ## [1] "DSC_258"
    ## [1] 92.6
    ## [1] 30
    ## [1] "DSC_254"
    ## [1] 97.3
    ## [1] 31
    ## [1] "DSC_332A"
    ## [1] 97.5
    ## [1] 32
    ## [1] "DSC_332B"
    ## [1] 94.7
    ## [1] 33
    ## [1] "DSC_256"
    ## [1] 96.3
    ## [1] 34
    ## [1] "DSC_242"
    ## [1] 95
    ## [1] 35
    ## [1] "DSC_249"
    ## [1] 95.8
    ## [1] 36
    ## [1] "DSC_357"
    ## [1] 94.1
    ## [1] 37
    ## [1] "DSC_255"
    ## [1] 94.7
    ## [1] 38
    ## [1] "DSC_339"
    ## [1] 94.6
    ## [1] 39
    ## [1] "DSC_260B"
    ## [1] 95.8
    ## [1] 40
    ## [1] "DSC_351"
    ## [1] 94.4
    ## [1] 41
    ## [1] "DSC_241"
    ## [1] 94.7
    ## [1] 42
    ## [1] "DSC_262"
    ## [1] 95
    ## [1] 43
    ## [1] "DSC_243"
    ## [1] 91.7
    ## [1] 44
    ## [1] "DSC_239"
    ## [1] 95.7
    ## [1] 45
    ## [1] "DSC_342"
    ## [1] 94
    ## [1] 46
    ## [1] "DSC_238"
    ## [1] 94.4
    ## [1] 47
    ## [1] "DSC_244"
    ## [1] 93.5
    ## [1] 48
    ## [1] "DSC_260A"
    ## [1] 94.7
    ## [1] 49
    ## [1] "DSC_247"
    ## [1] 96.3
    ## [1] 50
    ## [1] "DSC_248"
    ## [1] 94.5
    ## [1] 51
    ## [1] "DSC_330"
    ## [1] 93.7
    ## [1] 52
    ## [1] "DSC_257"
    ## [1] 93
    ## [1] 53
    ## [1] "DSC_253"
    ## [1] 96.3
    ## [1] 54
    ## [1] "DSC_328"
    ## [1] 95.5
    ## [1] 55
    ## [1] "DSC_209"
    ## [1] 95.7
    ## [1] 56
    ## [1] "DSC_215"
    ## [1] 95.5
    ## [1] 57
    ## [1] "DSC_206"
    ## [1] 95.5
    ## [1] 58
    ## [1] "DSC_214"
    ## [1] 95.1
    ## [1] 59
    ## [1] "DSC_203"
    ## [1] 94.7
    ## [1] 60
    ## [1] "DSC_218"
    ## [1] 95.4
    ## [1] 61
    ## [1] "DSC_223"
    ## [1] 95.3
    ## [1] 62
    ## [1] "DSC_301"
    ## [1] 95.5
    ## [1] 63
    ## [1] "DSC_315"
    ## [1] 95.4
    ## [1] 64
    ## [1] "DSC_311"
    ## [1] 95.3
    ## [1] 65
    ## [1] "DSC_308"
    ## [1] 95
    ## [1] 66
    ## [1] "DSC_224"
    ## [1] 95.4
    ## [1] 67
    ## [1] "DSC_205"
    ## [1] 95.5
    ## [1] 68
    ## [1] "DSC_318"
    ## [1] 95.3
    ## [1] 69
    ## [1] "DSC_210"
    ## [1] 95.4
    ## [1] 70
    ## [1] "DSC_304"
    ## [1] 95.4
    ## [1] 71
    ## [1] "DSC_207"
    ## [1] 95.4
    ## [1] 72
    ## [1] "DSC_305"
    ## [1] 95.5
    ## [1] 73
    ## [1] "DSC_201"
    ## [1] 95.5
    ## [1] 74
    ## [1] "DSC_216"
    ## [1] 95.5
    ## [1] 75
    ## [1] "DSC_312"
    ## [1] 95.4
    ## [1] 76
    ## [1] "DSC_211"
    ## [1] 95.4
    ## [1] 77
    ## [1] "DSC_222"
    ## [1] 95.5
    ## [1] 78
    ## [1] "DSC_302"
    ## [1] 95.5
    ## [1] 79
    ## [1] "DSC_220"
    ## [1] 95.3
    ## [1] 80
    ## [1] "DSC_307"
    ## [1] 95.3
    ## [1] 81
    ## [1] "DSC_313"
    ## [1] 95.4
    ## [1] 82
    ## [1] "DSC_317"
    ## [1] 95.6
    ## [1] 83
    ## [1] "DSC_306"
    ## [1] 95.5
    ## [1] 84
    ## [1] "DSC_202"
    ## [1] 95.4
    ## [1] 85
    ## [1] "DSC_303"
    ## [1] 95.3
    ## [1] 86
    ## [1] "DSC_300"
    ## [1] 95.6
    ## [1] 87
    ## [1] "DSC_310"
    ## [1] 95.4
    ## [1] 88
    ## [1] "DSC_314"
    ## [1] 95.4
    ## [1] 89
    ## [1] "DSC_219"
    ## [1] 95.4
    ## [1] 90
    ## [1] "DSC_316"
    ## [1] 100.3
    ## [1] 91
    ## [1] "DSC_208"
    ## [1] 100.1
    ## [1] 92
    ## [1] "DSC_200"
    ## [1] 100.5
    ## [1] 93
    ## [1] "DSC_221"
    ## [1] 100.5
    ## [1] 94
    ## [1] "DSC_213"
    ## [1] 100.5
    ## [1] 95
    ## [1] "DSC_204"
    ## [1] 100.5
