tidy_pH
================

``` r
library(googledrive)
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

# June Data

``` r
name_of_transcription_file_on_drive = "DSC_pH_data_Lila_Aug2026" # copy the file's name exactly as it appears on google drive

dribble = drive_get(name_of_transcription_file_on_drive, shared_drive = 'Bogar Lab') # get necesary data for downloading
```

    ## ! Using an auto-discovered, cached token.

    ##   To suppress this message, modify your code or options to clearly consent to
    ##   the use of a cached token.

    ##   See gargle's "Non-interactive auth" vignette for more details:

    ##   <https://gargle.r-lib.org/articles/non-interactive-auth.html>

    ## ℹ The googledrive package is using a cached token for 'agoodman@ucdavis.edu'.

    ## ✔ The input `path` resolved to exactly 1 file.

``` r
local_path <- paste0("Inputs/", 
                     name_of_transcription_file_on_drive, 
                     "_downloaded-", 
                     format(Sys.Date(), "%Y%m%d"), 
                     ".csv") #define what this local file name will be, with today's date

drive_download(dribble, type = "csv", path = local_path, overwrite = TRUE)
```

    ## File downloaded:

    ## • 'DSC_pH_data_Lila_Aug2026' <id: 1jKoXm2R6Iv2amUxZTCai4g4mLOjZfxrFM4tkNhR_S-M>

    ## Saved locally as:

    ## • 'Inputs/DSC_pH_data_Lila_Aug2026_downloaded-20261118.csv'

``` r
transcribed_data = read.csv(local_path)

pH_measures_june = transcribed_data
```

Remove rows which do not contain measurements

``` r
pH_measures_june = transcribed_data %>%
  filter(endsWith(Date.Measured, "2026"))
```

Clean up column names/contents

``` r
colnames(pH_measures_june)
```

    ##  [1] "SampleID"                            "notes"                              
    ##  [3] "Date.Measured"                       "mass.soil.plus.tube..g."            
    ##  [5] "average.15.mL.tube.mass..g."         "Approximate.soil.mass..g."          
    ##  [7] "Approximate.0.01M.CaCl2.to.add..mL." "Actual.0.01.CaCl2.added..mL."       
    ##  [9] "pH...first"                          "pH...second"

``` r
pH_cols_tidy_june = pH_measures_june %>%
  rename(approx_soil_mass_g = Approximate.soil.mass..g., # most of these for legibility
         CaCl2_mL = Actual.0.01.CaCl2.added..mL.,
         pH_1st_measurement = pH...first,
         pH_2nd_measurement = pH...second,
         Sample_ID = SampleID) %>% # make consistent with other metadata
  
  select(!c(mass.soil.plus.tube..g., 
            average.15.mL.tube.mass..g.,
            Approximate.0.01M.CaCl2.to.add..mL.)) # these both just for measurement set-up, not relevant downstream

colnames(pH_cols_tidy_june)
```

    ## [1] "Sample_ID"          "notes"              "Date.Measured"     
    ## [4] "approx_soil_mass_g" "CaCl2_mL"           "pH_1st_measurement"
    ## [7] "pH_2nd_measurement"

little things

``` r
pH_tidy_june = pH_cols_tidy_june %>%
  #put the letters in 16A and 16B in caps
  mutate(Sample_ID = case_when(
    Sample_ID == "DSC_016a" ~ "DSC_016A",
    Sample_ID == "DSC_016b" ~ "DSC_016B",
    .default = Sample_ID
  )) %>%
  rename(date = Date.Measured)

pH_tidy_june
```

    ##                 Sample_ID notes      date approx_soil_mass_g CaCl2_mL
    ## 1                 DSC_010        8/7/2026               3.68      7.4
    ## 2                 DSC_012       8/13/2026               2.06      4.1
    ## 3                 DSC_013       8/13/2026               1.96      3.9
    ## 4                 DSC_015        8/7/2026               4.04      8.1
    ## 5                 DSC_021        8/7/2026               3.63      7.3
    ## 6                 DSC_022        8/7/2026               3.89      7.8
    ## 7                 DSC_024       8/13/2026               1.95      3.9
    ## 8                 DSC_026        8/7/2026                3.9      7.8
    ## 9                 DSC_034        8/7/2026               4.65      9.3
    ## 10                DSC_038       8/13/2026                  2        4
    ## 11                DSC_039        8/7/2026               4.68      9.4
    ## 12                DSC_044       8/13/2026               1.07      2.1
    ## 13                DSC_047       8/13/2026               1.96      3.9
    ## 14                DSC_049        8/7/2026               5.27     10.5
    ## 15                DSC_052       8/13/2026               1.94      3.9
    ## 16                DSC_075        8/7/2026               3.95      7.9
    ## 17                DSC_091       8/13/2026               1.96      3.9
    ## 18                DSC_094       8/13/2026               1.08      2.2
    ## 19                DSC_103       8/13/2026                  2        4
    ## 20                DSC_105       8/13/2026                  1        2
    ## 21                DSC_106       8/13/2026                  1        2
    ## 22                DSC_112       8/13/2026                  2        4
    ## 23                DSC_130        8/7/2026               4.05      8.1
    ## 24  DSC_pH_blank_highmass        8/7/2026                        10.5
    ## 25   DSC_pH_blank_lowmass       8/13/2026                           2
    ## 26                DSC_009       8/13/2026               2.09      4.2
    ## 27                DSC_018       8/13/2026               2.06      4.1
    ## 28                DSC_019       8/10/2026               3.62      7.2
    ## 29                DSC_027       8/10/2026               3.88      7.8
    ## 30                DSC_030       8/10/2026               3.64      7.3
    ## 31                DSC_032       8/10/2026               3.44      6.9
    ## 32                DSC_037       8/10/2026               5.14     10.3
    ## 33                DSC_043       8/10/2026               3.64      7.3
    ## 34                DSC_045       8/13/2026               1.96      3.9
    ## 35                DSC_048       8/13/2026                  2        4
    ## 36                DSC_054       8/10/2026               3.09      6.2
    ## 37                DSC_055       8/10/2026               3.54      7.1
    ## 38                DSC_071       8/13/2026               2.06      4.1
    ## 39                DSC_074       8/10/2026               4.19      8.4
    ## 40                DSC_078       8/10/2026               4.46      8.9
    ## 41                DSC_082       8/10/2026               5.22     10.4
    ## 42                DSC_085       8/10/2026               3.64      7.3
    ## 43                DSC_086       8/10/2026               4.01        8
    ## 44                DSC_097       8/10/2026               4.55      9.1
    ## 45                DSC_098       8/10/2026               3.44      6.9
    ## 46                DSC_099       8/10/2026               3.89      7.8
    ## 47                DSC_102       8/13/2026               1.94      3.9
    ## 48                DSC_131       8/10/2026               3.93      7.9
    ## 49  DSC_pH_blank_highmass       8/10/2026                        10.5
    ## 50   DSC_pH_blank_lowmass       8/13/2026                           2
    ## 51                DSC_014       8/11/2026               4.42      8.8
    ## 52               DSC_016A       8/11/2026               2.81      5.6
    ## 53                DSC_025       8/11/2026               4.14      8.3
    ## 54                DSC_029       8/11/2026               3.79      7.6
    ## 55                DSC_033       8/11/2026                3.2      6.4
    ## 56                DSC_040       8/11/2026               4.22      8.4
    ## 57                DSC_046       8/13/2026                  2        4
    ## 58                DSC_050       8/11/2026               4.13      8.3
    ## 59                DSC_053       8/13/2026                  2        4
    ## 60                DSC_072       8/13/2026                  2        4
    ## 61                DSC_073       8/13/2026                  1        2
    ## 62                DSC_076       8/11/2026               5.24     10.5
    ## 63                DSC_077       8/11/2026               4.71      9.4
    ## 64                DSC_092       8/13/2026               2.04      4.1
    ## 65                DSC_095       8/13/2026               1.05      2.1
    ## 66                DSC_096       8/13/2026                  2        4
    ## 67                DSC_100       8/11/2026               4.48        9
    ## 68                DSC_101       8/11/2026               4.04      8.1
    ## 69                DSC_126       8/11/2026               3.88      7.8
    ## 70                DSC_127       8/11/2026               4.18      8.4
    ## 71                DSC_128       8/11/2026               3.22      6.4
    ## 72                DSC_129       8/11/2026               3.84      7.7
    ## 73                DSC_133       8/11/2026               3.93      7.9
    ## 74  DSC_pH_blank_highmass       8/11/2026                        10.5
    ## 75   DSC_pH_blank_lowmass       8/13/2026                           2
    ## 76               DSC_016B       8/11/2026               3.68      7.4
    ## 77                DSC_017       8/11/2026               4.36      8.7
    ## 78                DSC_020       8/11/2026               2.78      5.6
    ## 79                DSC_023       8/13/2026               1.94      3.9
    ## 80                DSC_028       8/11/2026               3.42      6.8
    ## 81                DSC_031       8/13/2026                  2        4
    ## 82                DSC_035       8/13/2026               1.93      3.9
    ## 83                DSC_036       8/13/2026                  2        4
    ## 84                DSC_041       8/11/2026               5.07     10.1
    ## 85                DSC_042       8/11/2026               4.27      8.5
    ## 86                DSC_051       8/11/2026               5.31     10.6
    ## 87                DSC_056       8/11/2026               5.33     10.7
    ## 88                DSC_080       8/13/2026                  2        4
    ## 89                DSC_081       8/13/2026                  2        4
    ## 90                DSC_084       8/13/2026               1.95      3.9
    ## 91                DSC_089       8/13/2026               1.93      3.9
    ## 92                DSC_090       8/13/2026               1.93      3.9
    ## 93                DSC_093       8/13/2026                  2        4
    ## 94                DSC_107       8/13/2026                  2        4
    ## 95                DSC_108       8/13/2026                  2        4
    ## 96                DSC_109       8/13/2026                  2        4
    ## 97                DSC_110       8/13/2026               1.95      3.9
    ## 98                DSC_111       8/13/2026                  2        4
    ## 99                DSC_132       8/11/2026               3.13      6.3
    ## 100 DSC_pH_blank_highmass       8/11/2026                        10.5
    ## 101  DSC_pH_blank_lowmass       8/13/2026                           2
    ##     pH_1st_measurement pH_2nd_measurement
    ## 1                 4.98               4.88
    ## 2                 4.64               5.05
    ## 3                 5.58               5.57
    ## 4                 4.83               4.83
    ## 5                 5.08               5.07
    ## 6                 5.29               5.27
    ## 7                 6.07               6.03
    ## 8                 4.67               4.68
    ## 9                 5.21               5.14
    ## 10                5.05               5.06
    ## 11                4.83               4.83
    ## 12                4.44               4.48
    ## 13                 5.4               5.56
    ## 14                5.06               5.07
    ## 15                4.55               4.58
    ## 16                4.66               4.68
    ## 17                5.63               5.59
    ## 18                4.23               4.22
    ## 19                5.03               5.04
    ## 20                4.91               4.91
    ## 21                4.46               4.52
    ## 22                5.03               5.04
    ## 23                4.82               4.81
    ## 24                5.37               5.03
    ## 25                5.33               5.39
    ## 26                5.11               5.12
    ## 27                6.51               5.11
    ## 28                5.09               5.01
    ## 29                 4.4               4.42
    ## 30                4.96               4.96
    ## 31                5.06               5.06
    ## 32                4.88               4.86
    ## 33                5.35               5.33
    ## 34                5.57                5.6
    ## 35                6.36                6.4
    ## 36                4.84               4.87
    ## 37                4.76               4.77
    ## 38                6.13               6.26
    ## 39                4.77               4.87
    ## 40                5.25               5.26
    ## 41                5.07               5.11
    ## 42                5.01               5.03
    ## 43                5.04               5.13
    ## 44                4.88                4.9
    ## 45                5.17               5.16
    ## 46                5.24               5.27
    ## 47                5.82               5.86
    ## 48                4.74               4.74
    ## 49                5.66               5.33
    ## 50                5.33               5.39
    ## 51                5.24                5.2
    ## 52                4.83               4.85
    ## 53                5.11               5.12
    ## 54                 4.8               4.81
    ## 55                5.02                4.9
    ## 56                4.98               4.96
    ## 57                4.84               4.74
    ## 58                4.99               4.96
    ## 59                4.77               4.74
    ## 60                4.57               4.58
    ## 61                3.58                3.6
    ## 62                5.47               5.47
    ## 63                5.34               5.39
    ## 64                5.26               5.28
    ## 65                 4.4               4.38
    ## 66                5.23                5.2
    ## 67                5.08                  5
    ## 68                5.49               5.39
    ## 69                4.63               4.64
    ## 70                4.71               4.73
    ## 71                4.64               4.62
    ## 72                4.87               4.84
    ## 73                4.88                4.9
    ## 74                 5.3               5.36
    ## 75                5.33               5.39
    ## 76                4.97                   
    ## 77                4.92               4.94
    ## 78                4.79               4.85
    ## 79                5.61               5.63
    ## 80                 4.5               4.55
    ## 81                5.37                5.4
    ## 82                5.33               5.33
    ## 83                5.26               5.24
    ## 84                5.38                5.3
    ## 85                4.95               5.33
    ## 86                5.01               5.06
    ## 87                5.03               5.04
    ## 88                 5.3               5.28
    ## 89                5.25               5.22
    ## 90                5.95               5.93
    ## 91                5.79               5.56
    ## 92                5.69               5.46
    ## 93                5.24               5.21
    ## 94                5.19               5.16
    ## 95                5.14               5.11
    ## 96                5.12               5.12
    ## 97                5.12               5.13
    ## 98                5.13               5.14
    ## 99                4.83               4.86
    ## 100                5.3               5.36
    ## 101               5.33               5.39

# September data

``` r
name_of_transcription_file_on_drive = "pH_data_sept_claude_transcribed_AG_manually_verified_20261118" # copy the file's name exactly as it appears on google drive

dribble = drive_get(name_of_transcription_file_on_drive, shared_drive = 'Bogar Lab') # get necesary data for downloading
```

    ## ✔ The input `path` resolved to exactly 1 file.

``` r
local_path <- paste0("Inputs/", 
                     name_of_transcription_file_on_drive, 
                     "_downloaded-", 
                     format(Sys.Date(), "%Y%m%d"), 
                     ".csv") #define what this local file name will be, with today's date

drive_download(dribble, type = "csv", path = local_path, overwrite = TRUE)
```

    ## File downloaded:

    ## • 'pH_data_sept_claude_transcribed_AG_manually_verified_20261118'
    ##   <id: 1NOI-jiwxPDqZrH6IgpEg1hoctvKEy7TycAX64KNaFZU>

    ## Saved locally as:

    ## • 'Inputs/pH_data_sept_claude_transcribed_AG_manually_verified_20261118_downloaded-20261118.csv'

``` r
transcribed_data = read.csv(local_path)

pH_measures_sept = transcribed_data
```

Written with Claude:

``` r
# ---- 1. Per-row calculations (one row = one subsample's pair of measurements) --
pH_calc <- pH_measures_sept |>
  mutate(
    # make sure the pH columns are numeric
    across(c(pH_first_measurement, pH_second_measurement), as.numeric),
    # second minus first: positive = pH drifted UP between the two measurements
    pH_diff     = pH_second_measurement - pH_first_measurement,
    # round before any threshold comparison (floating point: 4.85 - 4.70 is
    # stored as 0.14999999..., which would fail `>= 0.15`)
    abs_pH_diff = round(abs(pH_diff), 2)
  )

# CaCl2 blanks are not samples (the same "blank" ID repeats on several dates),
# so they are kept in their own table instead of being collapsed
pH_blanks <- pH_calc |> filter(Sample_ID == "blank")


# ---- 2. Drop subsamples that were replaced -----------------------------------------
pH_sets <- pH_calc |>
  filter(Sample_ID != "blank") |>
  # chronological order within each sample (each Sample_ID is its own sample,
  # including 260A/260B and 332A/332B)
  arrange(Sample_ID, date) |>
  group_by(Sample_ID) |>
  mutate(
    # a replacement set: any set from 11/17, or a set whose note says it replaces
    # the first subsample data (coalesce() turns NA from missing notes into FALSE)
    is_replacement = date == 20261117 |
      coalesce(
        str_detect(
          notes_claude_transcription_not_verified_unless_marked,
          "to replace first subsample"
        ),
        FALSE
      ),
    # a set is "replaced" when the NEXT set of the same sample is a replacement
    replaced = lead(is_replacement, default = FALSE),
    # order among the sets that remain once replaced sets are removed
    set_n = cumsum(!replaced) * !replaced
  ) |>
  ungroup() |>
  filter(!replaced) |>              # remove replaced sets, keep replacements
  select(-is_replacement, -replaced)

# at most two sets per sample should remain; stop with an error if not
stopifnot(max(pH_sets$set_n) <= 2)


# ---- 3. One row per sample ------------------------------------------------------------
# First remaining subsample keeps the ORIGINAL column names
first_sets <- pH_sets |>
  filter(set_n == 1) |>
  select(-set_n)

# Second remaining subsample (only samples with a QC re-subsample): every
# column gets the suffix "_subsample2", Sample_ID is left alone for joining
second_sets <- pH_sets |>
  filter(set_n == 2) |>
  select(-set_n) |>
  rename_with(\(x) str_c(x, "_subsample2"), .cols = -Sample_ID)

# pH_mean: average of ALL individual measurements (first and second) from every
# subsample that remains for the sample, pooled together
sample_means <- pH_sets |>
  group_by(Sample_ID) |>
  summarise(
    pH_mean = mean(c(pH_first_measurement, pH_second_measurement), na.rm = TRUE),
    .groups = "drop"
  )

pH_tidy_sept <- first_sets |>
  left_join(second_sets, by = "Sample_ID") |>
  left_join(sample_means, by = "Sample_ID") |>
  relocate(pH_mean, .after = abs_pH_diff)

# sanity check: exactly one row per sample
stopifnot(!anyDuplicated(pH_tidy_sept$Sample_ID))
```

export complete september pH data

``` r
local_path_tidydata <- paste0("Results/", 
                     "pH_sept_tidied-", 
                     format(Sys.Date(), "%Y%m%d"), 
                     ".csv") #define what this local file name will be, with today's date

write_csv(pH_tidy_sept, local_path_tidydata)
```

# Trim and prepare each for join

## June

``` r
pH_june_trimmed = pH_tidy_june %>%
  rename(pH_first_measurement = pH_1st_measurement, pH_second_measurement = pH_2nd_measurement) %>%
  mutate(
    # make sure the pH columns are numeric
    across(c(pH_first_measurement, pH_second_measurement), as.numeric),
    # second minus first: positive = pH drifted UP between the two measurements
    pH_diff     = pH_second_measurement - pH_first_measurement,
    # round before any threshold comparison (floating point: 4.85 - 4.70 is
    # stored as 0.14999999..., which would fail `>= 0.15`)
    abs_pH_diff = round(abs(pH_diff), 2),
    pH_mean = (pH_first_measurement + pH_second_measurement)/2
  ) %>%
  mutate(newdate = strptime(as.character(date), "%m/%d/%Y")) %>%
  mutate(date = format(newdate, "%Y%m%d")) %>%
  select(c(Sample_ID, date, pH_first_measurement, pH_second_measurement, pH_mean, abs_pH_diff)) %>%
  
  # organize blanks
  mutate(
    Sample_ID = case_when(
      str_detect(Sample_ID, "blank") ~ paste0(Sample_ID, "_", date),
      .default = Sample_ID
      ),
    Sample_ID = str_remove(Sample_ID, "DSC_pH_")
    )



pH_june_trimmed
```

    ##                   Sample_ID     date pH_first_measurement pH_second_measurement
    ## 1                   DSC_010 20260807                 4.98                  4.88
    ## 2                   DSC_012 20260813                 4.64                  5.05
    ## 3                   DSC_013 20260813                 5.58                  5.57
    ## 4                   DSC_015 20260807                 4.83                  4.83
    ## 5                   DSC_021 20260807                 5.08                  5.07
    ## 6                   DSC_022 20260807                 5.29                  5.27
    ## 7                   DSC_024 20260813                 6.07                  6.03
    ## 8                   DSC_026 20260807                 4.67                  4.68
    ## 9                   DSC_034 20260807                 5.21                  5.14
    ## 10                  DSC_038 20260813                 5.05                  5.06
    ## 11                  DSC_039 20260807                 4.83                  4.83
    ## 12                  DSC_044 20260813                 4.44                  4.48
    ## 13                  DSC_047 20260813                 5.40                  5.56
    ## 14                  DSC_049 20260807                 5.06                  5.07
    ## 15                  DSC_052 20260813                 4.55                  4.58
    ## 16                  DSC_075 20260807                 4.66                  4.68
    ## 17                  DSC_091 20260813                 5.63                  5.59
    ## 18                  DSC_094 20260813                 4.23                  4.22
    ## 19                  DSC_103 20260813                 5.03                  5.04
    ## 20                  DSC_105 20260813                 4.91                  4.91
    ## 21                  DSC_106 20260813                 4.46                  4.52
    ## 22                  DSC_112 20260813                 5.03                  5.04
    ## 23                  DSC_130 20260807                 4.82                  4.81
    ## 24  blank_highmass_20260807 20260807                 5.37                  5.03
    ## 25   blank_lowmass_20260813 20260813                 5.33                  5.39
    ## 26                  DSC_009 20260813                 5.11                  5.12
    ## 27                  DSC_018 20260813                 6.51                  5.11
    ## 28                  DSC_019 20260810                 5.09                  5.01
    ## 29                  DSC_027 20260810                 4.40                  4.42
    ## 30                  DSC_030 20260810                 4.96                  4.96
    ## 31                  DSC_032 20260810                 5.06                  5.06
    ## 32                  DSC_037 20260810                 4.88                  4.86
    ## 33                  DSC_043 20260810                 5.35                  5.33
    ## 34                  DSC_045 20260813                 5.57                  5.60
    ## 35                  DSC_048 20260813                 6.36                  6.40
    ## 36                  DSC_054 20260810                 4.84                  4.87
    ## 37                  DSC_055 20260810                 4.76                  4.77
    ## 38                  DSC_071 20260813                 6.13                  6.26
    ## 39                  DSC_074 20260810                 4.77                  4.87
    ## 40                  DSC_078 20260810                 5.25                  5.26
    ## 41                  DSC_082 20260810                 5.07                  5.11
    ## 42                  DSC_085 20260810                 5.01                  5.03
    ## 43                  DSC_086 20260810                 5.04                  5.13
    ## 44                  DSC_097 20260810                 4.88                  4.90
    ## 45                  DSC_098 20260810                 5.17                  5.16
    ## 46                  DSC_099 20260810                 5.24                  5.27
    ## 47                  DSC_102 20260813                 5.82                  5.86
    ## 48                  DSC_131 20260810                 4.74                  4.74
    ## 49  blank_highmass_20260810 20260810                 5.66                  5.33
    ## 50   blank_lowmass_20260813 20260813                 5.33                  5.39
    ## 51                  DSC_014 20260811                 5.24                  5.20
    ## 52                 DSC_016A 20260811                 4.83                  4.85
    ## 53                  DSC_025 20260811                 5.11                  5.12
    ## 54                  DSC_029 20260811                 4.80                  4.81
    ## 55                  DSC_033 20260811                 5.02                  4.90
    ## 56                  DSC_040 20260811                 4.98                  4.96
    ## 57                  DSC_046 20260813                 4.84                  4.74
    ## 58                  DSC_050 20260811                 4.99                  4.96
    ## 59                  DSC_053 20260813                 4.77                  4.74
    ## 60                  DSC_072 20260813                 4.57                  4.58
    ## 61                  DSC_073 20260813                 3.58                  3.60
    ## 62                  DSC_076 20260811                 5.47                  5.47
    ## 63                  DSC_077 20260811                 5.34                  5.39
    ## 64                  DSC_092 20260813                 5.26                  5.28
    ## 65                  DSC_095 20260813                 4.40                  4.38
    ## 66                  DSC_096 20260813                 5.23                  5.20
    ## 67                  DSC_100 20260811                 5.08                  5.00
    ## 68                  DSC_101 20260811                 5.49                  5.39
    ## 69                  DSC_126 20260811                 4.63                  4.64
    ## 70                  DSC_127 20260811                 4.71                  4.73
    ## 71                  DSC_128 20260811                 4.64                  4.62
    ## 72                  DSC_129 20260811                 4.87                  4.84
    ## 73                  DSC_133 20260811                 4.88                  4.90
    ## 74  blank_highmass_20260811 20260811                 5.30                  5.36
    ## 75   blank_lowmass_20260813 20260813                 5.33                  5.39
    ## 76                 DSC_016B 20260811                 4.97                    NA
    ## 77                  DSC_017 20260811                 4.92                  4.94
    ## 78                  DSC_020 20260811                 4.79                  4.85
    ## 79                  DSC_023 20260813                 5.61                  5.63
    ## 80                  DSC_028 20260811                 4.50                  4.55
    ## 81                  DSC_031 20260813                 5.37                  5.40
    ## 82                  DSC_035 20260813                 5.33                  5.33
    ## 83                  DSC_036 20260813                 5.26                  5.24
    ## 84                  DSC_041 20260811                 5.38                  5.30
    ## 85                  DSC_042 20260811                 4.95                  5.33
    ## 86                  DSC_051 20260811                 5.01                  5.06
    ## 87                  DSC_056 20260811                 5.03                  5.04
    ## 88                  DSC_080 20260813                 5.30                  5.28
    ## 89                  DSC_081 20260813                 5.25                  5.22
    ## 90                  DSC_084 20260813                 5.95                  5.93
    ## 91                  DSC_089 20260813                 5.79                  5.56
    ## 92                  DSC_090 20260813                 5.69                  5.46
    ## 93                  DSC_093 20260813                 5.24                  5.21
    ## 94                  DSC_107 20260813                 5.19                  5.16
    ## 95                  DSC_108 20260813                 5.14                  5.11
    ## 96                  DSC_109 20260813                 5.12                  5.12
    ## 97                  DSC_110 20260813                 5.12                  5.13
    ## 98                  DSC_111 20260813                 5.13                  5.14
    ## 99                  DSC_132 20260811                 4.83                  4.86
    ## 100 blank_highmass_20260811 20260811                 5.30                  5.36
    ## 101  blank_lowmass_20260813 20260813                 5.33                  5.39
    ##     pH_mean abs_pH_diff
    ## 1     4.930        0.10
    ## 2     4.845        0.41
    ## 3     5.575        0.01
    ## 4     4.830        0.00
    ## 5     5.075        0.01
    ## 6     5.280        0.02
    ## 7     6.050        0.04
    ## 8     4.675        0.01
    ## 9     5.175        0.07
    ## 10    5.055        0.01
    ## 11    4.830        0.00
    ## 12    4.460        0.04
    ## 13    5.480        0.16
    ## 14    5.065        0.01
    ## 15    4.565        0.03
    ## 16    4.670        0.02
    ## 17    5.610        0.04
    ## 18    4.225        0.01
    ## 19    5.035        0.01
    ## 20    4.910        0.00
    ## 21    4.490        0.06
    ## 22    5.035        0.01
    ## 23    4.815        0.01
    ## 24    5.200        0.34
    ## 25    5.360        0.06
    ## 26    5.115        0.01
    ## 27    5.810        1.40
    ## 28    5.050        0.08
    ## 29    4.410        0.02
    ## 30    4.960        0.00
    ## 31    5.060        0.00
    ## 32    4.870        0.02
    ## 33    5.340        0.02
    ## 34    5.585        0.03
    ## 35    6.380        0.04
    ## 36    4.855        0.03
    ## 37    4.765        0.01
    ## 38    6.195        0.13
    ## 39    4.820        0.10
    ## 40    5.255        0.01
    ## 41    5.090        0.04
    ## 42    5.020        0.02
    ## 43    5.085        0.09
    ## 44    4.890        0.02
    ## 45    5.165        0.01
    ## 46    5.255        0.03
    ## 47    5.840        0.04
    ## 48    4.740        0.00
    ## 49    5.495        0.33
    ## 50    5.360        0.06
    ## 51    5.220        0.04
    ## 52    4.840        0.02
    ## 53    5.115        0.01
    ## 54    4.805        0.01
    ## 55    4.960        0.12
    ## 56    4.970        0.02
    ## 57    4.790        0.10
    ## 58    4.975        0.03
    ## 59    4.755        0.03
    ## 60    4.575        0.01
    ## 61    3.590        0.02
    ## 62    5.470        0.00
    ## 63    5.365        0.05
    ## 64    5.270        0.02
    ## 65    4.390        0.02
    ## 66    5.215        0.03
    ## 67    5.040        0.08
    ## 68    5.440        0.10
    ## 69    4.635        0.01
    ## 70    4.720        0.02
    ## 71    4.630        0.02
    ## 72    4.855        0.03
    ## 73    4.890        0.02
    ## 74    5.330        0.06
    ## 75    5.360        0.06
    ## 76       NA          NA
    ## 77    4.930        0.02
    ## 78    4.820        0.06
    ## 79    5.620        0.02
    ## 80    4.525        0.05
    ## 81    5.385        0.03
    ## 82    5.330        0.00
    ## 83    5.250        0.02
    ## 84    5.340        0.08
    ## 85    5.140        0.38
    ## 86    5.035        0.05
    ## 87    5.035        0.01
    ## 88    5.290        0.02
    ## 89    5.235        0.03
    ## 90    5.940        0.02
    ## 91    5.675        0.23
    ## 92    5.575        0.23
    ## 93    5.225        0.03
    ## 94    5.175        0.03
    ## 95    5.125        0.03
    ## 96    5.120        0.00
    ## 97    5.125        0.01
    ## 98    5.135        0.01
    ## 99    4.845        0.03
    ## 100   5.330        0.06
    ## 101   5.360        0.06

``` r
# some of these blanks are duplicated, I think
pH_june_trimmed[which(duplicated(pH_june_trimmed)), ] # yep all duplicates are blanks
```

    ##                   Sample_ID     date pH_first_measurement pH_second_measurement
    ## 50   blank_lowmass_20260813 20260813                 5.33                  5.39
    ## 75   blank_lowmass_20260813 20260813                 5.33                  5.39
    ## 100 blank_highmass_20260811 20260811                 5.30                  5.36
    ## 101  blank_lowmass_20260813 20260813                 5.33                  5.39
    ##     pH_mean abs_pH_diff
    ## 50     5.36        0.06
    ## 75     5.36        0.06
    ## 100    5.33        0.06
    ## 101    5.36        0.06

``` r
#remove all of these

pH_june_trimmed_tidyblanks = pH_june_trimmed[-which(duplicated(pH_june_trimmed)), ] 

pH_june_trimmed_tidyblanks[which(duplicated(pH_june_trimmed_tidyblanks)), ]
```

    ## [1] Sample_ID             date                  pH_first_measurement 
    ## [4] pH_second_measurement pH_mean               abs_pH_diff          
    ## <0 rows> (or 0-length row.names)

add abs_pH_diff_subsample2 as a column to june, just so rbind will work
when we keep it for sept

``` r
pH_june_trimmed = pH_june_trimmed_tidyblanks %>%
  mutate(abs_pH_diff_subsample2 = NA)
```

## Sept

``` r
pH_sept_trimmed = pH_tidy_sept %>%
  select(c(Sample_ID, date, pH_first_measurement, pH_second_measurement, pH_mean, abs_pH_diff, abs_pH_diff_subsample2))
```

# Join

``` r
pH_all = rbind(pH_june_trimmed, pH_sept_trimmed)

pH_all = pH_all %>%
  select(Sample_ID, pH_mean, date, everything()) %>%
  select(-c(pH_first_measurement, pH_second_measurement)) %>%
  rename(pH_measurement_date = date)

pH_all
```

    ##                   Sample_ID pH_mean pH_measurement_date abs_pH_diff
    ## 1                   DSC_010  4.9300            20260807        0.10
    ## 2                   DSC_012  4.8450            20260813        0.41
    ## 3                   DSC_013  5.5750            20260813        0.01
    ## 4                   DSC_015  4.8300            20260807        0.00
    ## 5                   DSC_021  5.0750            20260807        0.01
    ## 6                   DSC_022  5.2800            20260807        0.02
    ## 7                   DSC_024  6.0500            20260813        0.04
    ## 8                   DSC_026  4.6750            20260807        0.01
    ## 9                   DSC_034  5.1750            20260807        0.07
    ## 10                  DSC_038  5.0550            20260813        0.01
    ## 11                  DSC_039  4.8300            20260807        0.00
    ## 12                  DSC_044  4.4600            20260813        0.04
    ## 13                  DSC_047  5.4800            20260813        0.16
    ## 14                  DSC_049  5.0650            20260807        0.01
    ## 15                  DSC_052  4.5650            20260813        0.03
    ## 16                  DSC_075  4.6700            20260807        0.02
    ## 17                  DSC_091  5.6100            20260813        0.04
    ## 18                  DSC_094  4.2250            20260813        0.01
    ## 19                  DSC_103  5.0350            20260813        0.01
    ## 20                  DSC_105  4.9100            20260813        0.00
    ## 21                  DSC_106  4.4900            20260813        0.06
    ## 22                  DSC_112  5.0350            20260813        0.01
    ## 23                  DSC_130  4.8150            20260807        0.01
    ## 24  blank_highmass_20260807  5.2000            20260807        0.34
    ## 25   blank_lowmass_20260813  5.3600            20260813        0.06
    ## 26                  DSC_009  5.1150            20260813        0.01
    ## 27                  DSC_018  5.8100            20260813        1.40
    ## 28                  DSC_019  5.0500            20260810        0.08
    ## 29                  DSC_027  4.4100            20260810        0.02
    ## 30                  DSC_030  4.9600            20260810        0.00
    ## 31                  DSC_032  5.0600            20260810        0.00
    ## 32                  DSC_037  4.8700            20260810        0.02
    ## 33                  DSC_043  5.3400            20260810        0.02
    ## 34                  DSC_045  5.5850            20260813        0.03
    ## 35                  DSC_048  6.3800            20260813        0.04
    ## 36                  DSC_054  4.8550            20260810        0.03
    ## 37                  DSC_055  4.7650            20260810        0.01
    ## 38                  DSC_071  6.1950            20260813        0.13
    ## 39                  DSC_074  4.8200            20260810        0.10
    ## 40                  DSC_078  5.2550            20260810        0.01
    ## 41                  DSC_082  5.0900            20260810        0.04
    ## 42                  DSC_085  5.0200            20260810        0.02
    ## 43                  DSC_086  5.0850            20260810        0.09
    ## 44                  DSC_097  4.8900            20260810        0.02
    ## 45                  DSC_098  5.1650            20260810        0.01
    ## 46                  DSC_099  5.2550            20260810        0.03
    ## 47                  DSC_102  5.8400            20260813        0.04
    ## 48                  DSC_131  4.7400            20260810        0.00
    ## 49  blank_highmass_20260810  5.4950            20260810        0.33
    ## 51                  DSC_014  5.2200            20260811        0.04
    ## 52                 DSC_016A  4.8400            20260811        0.02
    ## 53                  DSC_025  5.1150            20260811        0.01
    ## 54                  DSC_029  4.8050            20260811        0.01
    ## 55                  DSC_033  4.9600            20260811        0.12
    ## 56                  DSC_040  4.9700            20260811        0.02
    ## 57                  DSC_046  4.7900            20260813        0.10
    ## 58                  DSC_050  4.9750            20260811        0.03
    ## 59                  DSC_053  4.7550            20260813        0.03
    ## 60                  DSC_072  4.5750            20260813        0.01
    ## 61                  DSC_073  3.5900            20260813        0.02
    ## 62                  DSC_076  5.4700            20260811        0.00
    ## 63                  DSC_077  5.3650            20260811        0.05
    ## 64                  DSC_092  5.2700            20260813        0.02
    ## 65                  DSC_095  4.3900            20260813        0.02
    ## 66                  DSC_096  5.2150            20260813        0.03
    ## 67                  DSC_100  5.0400            20260811        0.08
    ## 68                  DSC_101  5.4400            20260811        0.10
    ## 69                  DSC_126  4.6350            20260811        0.01
    ## 70                  DSC_127  4.7200            20260811        0.02
    ## 71                  DSC_128  4.6300            20260811        0.02
    ## 72                  DSC_129  4.8550            20260811        0.03
    ## 73                  DSC_133  4.8900            20260811        0.02
    ## 74  blank_highmass_20260811  5.3300            20260811        0.06
    ## 76                 DSC_016B      NA            20260811          NA
    ## 77                  DSC_017  4.9300            20260811        0.02
    ## 78                  DSC_020  4.8200            20260811        0.06
    ## 79                  DSC_023  5.6200            20260813        0.02
    ## 80                  DSC_028  4.5250            20260811        0.05
    ## 81                  DSC_031  5.3850            20260813        0.03
    ## 82                  DSC_035  5.3300            20260813        0.00
    ## 83                  DSC_036  5.2500            20260813        0.02
    ## 84                  DSC_041  5.3400            20260811        0.08
    ## 85                  DSC_042  5.1400            20260811        0.38
    ## 86                  DSC_051  5.0350            20260811        0.05
    ## 87                  DSC_056  5.0350            20260811        0.01
    ## 88                  DSC_080  5.2900            20260813        0.02
    ## 89                  DSC_081  5.2350            20260813        0.03
    ## 90                  DSC_084  5.9400            20260813        0.02
    ## 91                  DSC_089  5.6750            20260813        0.23
    ## 92                  DSC_090  5.5750            20260813        0.23
    ## 93                  DSC_093  5.2250            20260813        0.03
    ## 94                  DSC_107  5.1750            20260813        0.03
    ## 95                  DSC_108  5.1250            20260813        0.03
    ## 96                  DSC_109  5.1200            20260813        0.00
    ## 97                  DSC_110  5.1250            20260813        0.01
    ## 98                  DSC_111  5.1350            20260813        0.01
    ## 99                  DSC_132  4.8450            20260811        0.03
    ## 110                 DSC_200  4.5050            20261105        0.03
    ## 210                 DSC_201  4.7550            20261028        0.05
    ## 310                 DSC_202  5.1300            20261029        0.04
    ## 410                 DSC_203  4.9500            20261028        0.10
    ## 510                 DSC_204  4.6450            20261105        0.01
    ## 610                 DSC_205  4.7550            20261028        0.09
    ## 710                 DSC_206  5.0000            20261117        0.00
    ## 810                 DSC_207  4.8600            20261028        0.04
    ## 910                 DSC_208  4.6250            20261105        0.01
    ## 101                 DSC_209  4.8950            20261105        0.07
    ## 111                 DSC_210  5.0800            20261028        0.14
    ## 121                 DSC_211  5.0000            20261028        0.02
    ## 131                 DSC_213  4.1700            20261105        0.02
    ## 141                 DSC_214  5.0450            20261028        0.11
    ## 151                 DSC_215  5.0750            20261028        0.11
    ## 161                 DSC_216  5.2650            20261028        0.05
    ## 171                 DSC_217  4.8450            20261105        0.01
    ## 181                 DSC_218  4.9650            20261028        0.07
    ## 191                 DSC_219  5.0250            20261105        0.03
    ## 201                 DSC_220  5.1100            20261029        0.02
    ## 211                 DSC_221  4.7600            20261105        0.02
    ## 221                 DSC_222  5.0800            20261028        0.02
    ## 231                 DSC_223  4.8800            20261028        0.06
    ## 241                 DSC_224  5.0000            20261117        0.02
    ## 251                 DSC_238  5.0900            20261027        0.02
    ## 261                 DSC_239  5.0750            20261105        0.05
    ## 271                 DSC_240  4.9350            20261020        0.03
    ## 281                 DSC_241  4.8700            20261027        0.02
    ## 291                 DSC_242  5.0000            20261022        0.02
    ## 301                 DSC_243  4.8650            20261027        0.01
    ## 311                 DSC_244  5.1550            20261027        0.05
    ## 321                 DSC_245  4.9250            20261020        0.01
    ## 331                 DSC_246  4.9800            20261015        0.02
    ## 341                 DSC_247  4.8350            20261027        0.01
    ## 351                 DSC_248  4.8675            20261027        0.02
    ## 361                 DSC_249  5.1450            20261022        0.03
    ## 371                 DSC_250  4.9800            20261020        0.02
    ## 381                 DSC_251  4.9250            20261020        0.03
    ## 391                 DSC_252  5.0300            20261020        0.18
    ## 401                 DSC_253  5.0600            20261027        0.02
    ## 411                 DSC_254  4.7700            20261022        0.02
    ## 421                 DSC_255  4.9100            20261022        0.00
    ## 431                 DSC_256  5.1350            20261022        0.01
    ## 441                 DSC_257  5.3050            20261027        0.01
    ## 451                 DSC_258  5.2200            20261022        0.04
    ## 461                 DSC_259  5.2775            20261015        0.01
    ## 471                DSC_260A  5.1800            20261027        0.02
    ## 481                DSC_260B  4.8300            20261027        0.00
    ## 491                 DSC_261  5.0300            20261020        0.02
    ## 50                  DSC_262  4.9450            20261027        0.03
    ## 511                 DSC_300  5.2500            20261029        0.00
    ## 521                 DSC_301  4.9150            20261028        0.07
    ## 531                 DSC_302  5.2500            20261029        0.10
    ## 541                 DSC_303  5.1200            20261029        0.02
    ## 551                 DSC_304  4.8300            20261028        0.08
    ## 561                 DSC_305  5.5200            20261117        0.04
    ## 571                 DSC_306  5.2550            20261029        0.03
    ## 581                 DSC_307  5.2750            20261029        0.07
    ## 591                 DSC_308  6.9475            20261028        0.10
    ## 601                 DSC_310  5.1150            20261029        0.07
    ## 611                 DSC_311  5.0400            20261028        0.02
    ## 621                 DSC_312  5.0700            20261028        0.00
    ## 631                 DSC_313  5.2000            20261029        0.00
    ## 641                 DSC_314  5.0400            20261029        0.00
    ## 651                 DSC_315  4.9200            20261028        0.08
    ## 661                 DSC_316  5.2600            20261105        0.04
    ## 671                 DSC_317  5.2800            20261029        0.04
    ## 681                 DSC_318  5.8400            20261117        0.00
    ## 691                 DSC_328  5.1600            20261027        0.02
    ## 701                 DSC_329  5.6150            20261117        0.03
    ## 711                 DSC_330  5.0900            20261027        0.02
    ## 721                 DSC_331  5.1350            20261015        0.01
    ## 731                DSC_332A  5.1000            20261022        0.08
    ## 741                DSC_332B  5.5600            20261022        0.08
    ## 75                  DSC_333  5.1950            20261020        0.03
    ## 761                 DSC_334  4.9700            20261020        0.04
    ## 771                 DSC_335  5.0400            20261015        0.00
    ## 781                 DSC_336  4.9850            20261020        0.03
    ## 791                 DSC_337  5.0650            20261015        0.03
    ## 801                 DSC_338  5.0600            20261022        0.06
    ## 811                 DSC_339  4.7750            20261027        0.15
    ## 821                 DSC_340  5.2900            20261020        0.04
    ## 831                 DSC_341  5.0000            20261015        0.00
    ## 841                 DSC_342  4.9600            20261027        0.00
    ## 851                 DSC_343  4.7450            20261020        0.09
    ## 861                 DSC_348  4.8600            20261020        0.00
    ## 871                 DSC_349  5.0500            20261015        0.02
    ## 881                 DSC_350  4.8950            20261020        0.11
    ## 891                 DSC_351  4.8450            20261027        0.01
    ## 901                 DSC_352  5.1500            20261015        0.06
    ## 911                 DSC_353  4.9400            20261020        0.02
    ## 921                 DSC_355  4.9250            20261020        0.07
    ## 931                 DSC_356  4.8200            20261020        0.06
    ## 941                 DSC_357  5.0950            20261022        0.01
    ## 951                 DSC_358  4.9850            20261020        0.03
    ## 961                 DSC_359  5.1450            20261015        0.07
    ##     abs_pH_diff_subsample2
    ## 1                       NA
    ## 2                       NA
    ## 3                       NA
    ## 4                       NA
    ## 5                       NA
    ## 6                       NA
    ## 7                       NA
    ## 8                       NA
    ## 9                       NA
    ## 10                      NA
    ## 11                      NA
    ## 12                      NA
    ## 13                      NA
    ## 14                      NA
    ## 15                      NA
    ## 16                      NA
    ## 17                      NA
    ## 18                      NA
    ## 19                      NA
    ## 20                      NA
    ## 21                      NA
    ## 22                      NA
    ## 23                      NA
    ## 24                      NA
    ## 25                      NA
    ## 26                      NA
    ## 27                      NA
    ## 28                      NA
    ## 29                      NA
    ## 30                      NA
    ## 31                      NA
    ## 32                      NA
    ## 33                      NA
    ## 34                      NA
    ## 35                      NA
    ## 36                      NA
    ## 37                      NA
    ## 38                      NA
    ## 39                      NA
    ## 40                      NA
    ## 41                      NA
    ## 42                      NA
    ## 43                      NA
    ## 44                      NA
    ## 45                      NA
    ## 46                      NA
    ## 47                      NA
    ## 48                      NA
    ## 49                      NA
    ## 51                      NA
    ## 52                      NA
    ## 53                      NA
    ## 54                      NA
    ## 55                      NA
    ## 56                      NA
    ## 57                      NA
    ## 58                      NA
    ## 59                      NA
    ## 60                      NA
    ## 61                      NA
    ## 62                      NA
    ## 63                      NA
    ## 64                      NA
    ## 65                      NA
    ## 66                      NA
    ## 67                      NA
    ## 68                      NA
    ## 69                      NA
    ## 70                      NA
    ## 71                      NA
    ## 72                      NA
    ## 73                      NA
    ## 74                      NA
    ## 76                      NA
    ## 77                      NA
    ## 78                      NA
    ## 79                      NA
    ## 80                      NA
    ## 81                      NA
    ## 82                      NA
    ## 83                      NA
    ## 84                      NA
    ## 85                      NA
    ## 86                      NA
    ## 87                      NA
    ## 88                      NA
    ## 89                      NA
    ## 90                      NA
    ## 91                      NA
    ## 92                      NA
    ## 93                      NA
    ## 94                      NA
    ## 95                      NA
    ## 96                      NA
    ## 97                      NA
    ## 98                      NA
    ## 99                      NA
    ## 110                     NA
    ## 210                     NA
    ## 310                     NA
    ## 410                     NA
    ## 510                     NA
    ## 610                     NA
    ## 710                     NA
    ## 810                     NA
    ## 910                     NA
    ## 101                     NA
    ## 111                   0.10
    ## 121                     NA
    ## 131                     NA
    ## 141                     NA
    ## 151                     NA
    ## 161                     NA
    ## 171                     NA
    ## 181                     NA
    ## 191                     NA
    ## 201                   0.04
    ## 211                     NA
    ## 221                     NA
    ## 231                     NA
    ## 241                     NA
    ## 251                     NA
    ## 261                     NA
    ## 271                     NA
    ## 281                     NA
    ## 291                     NA
    ## 301                     NA
    ## 311                     NA
    ## 321                     NA
    ## 331                     NA
    ## 341                     NA
    ## 351                   0.05
    ## 361                     NA
    ## 371                     NA
    ## 381                     NA
    ## 391                     NA
    ## 401                     NA
    ## 411                     NA
    ## 421                     NA
    ## 431                     NA
    ## 441                     NA
    ## 451                     NA
    ## 461                   0.10
    ## 471                     NA
    ## 481                     NA
    ## 491                     NA
    ## 50                      NA
    ## 511                     NA
    ## 521                     NA
    ## 531                     NA
    ## 541                     NA
    ## 551                     NA
    ## 561                     NA
    ## 571                     NA
    ## 581                     NA
    ## 591                   0.17
    ## 601                     NA
    ## 611                     NA
    ## 621                     NA
    ## 631                     NA
    ## 641                     NA
    ## 651                     NA
    ## 661                     NA
    ## 671                     NA
    ## 681                     NA
    ## 691                     NA
    ## 701                     NA
    ## 711                     NA
    ## 721                     NA
    ## 731                     NA
    ## 741                     NA
    ## 75                      NA
    ## 761                     NA
    ## 771                     NA
    ## 781                     NA
    ## 791                     NA
    ## 801                     NA
    ## 811                     NA
    ## 821                   0.04
    ## 831                     NA
    ## 841                     NA
    ## 851                     NA
    ## 861                     NA
    ## 871                     NA
    ## 881                     NA
    ## 891                     NA
    ## 901                     NA
    ## 911                     NA
    ## 921                     NA
    ## 931                     NA
    ## 941                     NA
    ## 951                     NA
    ## 961                     NA

export all pH data (though still missing sept blanks as of now)

``` r
local_path_tidydata <- paste0("Results/", 
                     "pH_tidied-", 
                     format(Sys.Date(), "%Y%m%d"), 
                     ".csv") #define what this local file name will be, with today's date

write_csv(pH_all, local_path_tidydata)
```
