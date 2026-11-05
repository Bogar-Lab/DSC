pH_redo_planning_sept
================

Ok so the plan is to repeat the following pH measurements on new
subsamples:

- DSC_239

  - because the 15mL falcon cracked in the centrifuge (I still measured
    the reoverable supernatant, but it had touched the inside of the
    swinging bucket which could have had salt residues/chemical
    contaminants on it)

- DSC_209

  - because I a) attempted to transfer all soil from the 50mL pH
    subsampling tube weighed during processing to new 15mL tube,
    potentially biasing the particle size (leaving behind finer
    particles but taking all coarse), and b) left it out on the bench at
    RT for several hours before adding CaCl2/measuring.

- AND ~ 5 others, randomly selected amongst samples which have plenty of
  extra field-moist still. All remaining FM soil is in RESP_IS tubes.

  - maybe 3 yzs and 3 xs?

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
psm_sept = read_csv("../../../TidyData/Metadata/make_per_sample_metadata_september/Results/psm_sept_tidied-20261104.csv")
```

    ## New names:
    ## Rows: 96 Columns: 49
    ## ── Column specification
    ## ──────────────────────────────────────────────────────── Delimiter: "," chr
    ## (32): Sample_ID, Scribe_initials, Siever_1_initials, Siever_2_initials,... dbl
    ## (8): ...1, sieve_date, S_pH_mass_g, large_tin_mass_full_g_1, large_tin... lgl
    ## (3): Siever_3_initials, large_tin_mass_full_g_3, large_tin_mass_full_g_4 time
    ## (6): subsampling_start_time, sieving_start_time, sieving_end_time, all...
    ## ℹ Use `spec()` to retrieve the full column specification for this data. ℹ
    ## Specify the column types or set `show_col_types = FALSE` to quiet this message.
    ## • `` -> `...1`

## make a copy of the per-sample metadata which only has the Sample_IDs and masses of soil set aside for pH – AND fraction, so we can treat Os differently

``` r
colnames(psm_sept) # no fraction in here.
```

    ##  [1] "...1"                         "Sample_ID"                   
    ##  [3] "sieve_date"                   "Scribe_initials"             
    ##  [5] "Siever_1_initials"            "Siever_2_initials"           
    ##  [7] "Siever_3_initials"            "Lroot_empty_mass"            
    ##  [9] "rock_empty_mass"              "ID_check_core_datasheets"    
    ## [11] "fraction (if on sample tube)" "other_info_on_sample_tube"   
    ## [13] "empty_sieve_mass_g"           "small_tin_mass_empty_g"      
    ## [15] "tube_length_cm"               "Horizons (+gophers?)"        
    ## [17] "pre_trim_photo_by"            "subsampling_start_time"      
    ## [19] "post_trim_photo_by"           "removed_soil_mass_g"         
    ## [21] "total_wet_mass_w_sieve_g"     "sieving_start_time"          
    ## [23] "sieving_end_time"             "Lroot_full_mass_g"           
    ## [25] "Lroot_photo_by"               "rock_full_mass_g"            
    ## [27] "rock_photo_by"                "all_ROOTS_freeze_time"       
    ## [29] "ROOTS_W_exists"               "ROOTS_H_exists"              
    ## [31] "ROOTS_D_exists"               "L_ROOT_exists"               
    ## [33] "S_DNA_mass_g"                 "RESP_IS_mass_g"              
    ## [35] "S_pH_mass_g"                  "everything_cool_time"        
    ## [37] "small_tin_mass_full_g"        "large_tin_mass_full_g_1"     
    ## [39] "large_tin_mass_full_g_2"      "large_tin_mass_full_g_3"     
    ## [41] "large_tin_mass_full_g_4"      "all_set_to_dry_time"         
    ## [43] "transcription_notes_AG"       "pH_mass_g_soil_only"         
    ## [45] "large_tin_mass_full_g"        "RESP_SIR_mass_g"             
    ## [47] "RESP_IS_in_bag_mass_g"        "total_mass"                  
    ## [49] "unnamed_column"

``` r
# get it from core data
draft_core_data = read_csv("../../Inventory/sample_ID_audit_20260922/Inputs/core_data_sept_transcribed20260922_AG_downloaded-20260922.csv")
```

    ## Rows: 120 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (6): Sample_ID, Core_ID, fraction, extra_pieces, other_notes, depth_inte...
    ## lgl (2): This sheet is not a faithful transcription -- tabling this for late...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
colnames(draft_core_data)
```

    ## [1] "This sheet is not a faithful transcription -- tabling this for later. Anna is going through the core datasheets and making a list of all the sample_IDs we assigned + metadata about them, reformatting as she feels most useful right now 20260922"
    ## [2] "Sample_ID"                                                                                                                                                                                                                                          
    ## [3] "Core_ID"                                                                                                                                                                                                                                            
    ## [4] "fraction"                                                                                                                                                                                                                                           
    ## [5] "YZ processed?"                                                                                                                                                                                                                                      
    ## [6] "extra_pieces"                                                                                                                                                                                                                                       
    ## [7] "other_notes"                                                                                                                                                                                                                                        
    ## [8] "depth_interval_calculated_in_field"

``` r
fraction_key = draft_core_data %>% select(Sample_ID, fraction)

sept_xy_psm_w_fractions = left_join(psm_sept, fraction_key, by = "Sample_ID", keep = FALSE)
head(sept_xy_psm_w_fractions)
```

    ## # A tibble: 6 × 50
    ##    ...1 Sample_ID sieve_date Scribe_initials Siever_1_initials Siever_2_initials
    ##   <dbl> <chr>          <dbl> <chr>           <chr>             <chr>            
    ## 1     1 DSC_352     20260919 SSS             BKL               KPG              
    ## 2     2 DSC_329     20260919 BKL             SJS               <NA>             
    ## 3     3 DSC_335     20260919 BKL             SJS               KPG              
    ## 4     4 DSC_337     20260919 LRA             AG                LR               
    ## 5     5 DSC_259     20260919 placeholderChar <NA>              <NA>             
    ## 6     6 DSC_331     20260919 placeholderChar <NA>              <NA>             
    ## # ℹ 44 more variables: Siever_3_initials <lgl>, Lroot_empty_mass <chr>,
    ## #   rock_empty_mass <chr>, ID_check_core_datasheets <chr>,
    ## #   `fraction (if on sample tube)` <chr>, other_info_on_sample_tube <chr>,
    ## #   empty_sieve_mass_g <chr>, small_tin_mass_empty_g <chr>,
    ## #   tube_length_cm <chr>, `Horizons (+gophers?)` <chr>,
    ## #   pre_trim_photo_by <chr>, subsampling_start_time <time>,
    ## #   post_trim_photo_by <chr>, removed_soil_mass_g <chr>, …

## disqualify

- the two samples we’re already re-doing,
- O samples
- anything that has not enough RESP_IS leftover? – ok I haven’t
  transcribed all the RESP_IS masses this time, but probably they all
  have enough

``` r
psm_sans_dqs = sept_xy_psm_w_fractions %>%
  filter(fraction != "O") %>%
  filter(Sample_ID != "DSC_239") %>%
  filter(Sample_ID != "DSC_209")

possible_redos_x = psm_sans_dqs %>%
  filter(startsWith(fraction, "0-") | startsWith(fraction, "15-") | startsWith(fraction, "30-"))


possible_redos_yz = psm_sans_dqs %>%
  filter(! (Sample_ID %in% possible_redos_x$Sample_ID))

possible_redos_x = possible_redos_x$Sample_ID

possible_redos_yz = possible_redos_yz$Sample_ID
```

# randomly select 3 xs and 3 yzs to redo

``` r
random_list_indicies_x = sample(1:length(possible_redos_x) , 3) # choose random indicies in the list of possible redo sids

chosen_sids_x = possible_redos_x[random_list_indicies_x] # make a list of the sample IDs at these indicies


random_list_indicies_yz = sample(1:length(possible_redos_yz), 3)  # choose random indicies in the list of possible redo sids

chosen_sids = append(chosen_sids_x, possible_redos_yz[random_list_indicies_yz]) # add these sampe ids to the list
```

## look at their metadata

``` r
chosen_for_redo = sept_xy_psm_w_fractions %>%
  filter(Sample_ID %in% chosen_sids | Sample_ID == "DSC_239" | Sample_ID == "DSC_209") %>%
  select(c(Sample_ID, sieve_date, fraction, starts_with("RESP")))

chosen_for_redo
```

    ## # A tibble: 8 × 6
    ##   Sample_ID sieve_date fraction   RESP_IS_mass_g RESP_SIR_mass_g
    ##   <chr>          <dbl> <chr>      <chr>          <chr>          
    ## 1 DSC_349     20260920 300-400_cm <NA>           <NA>           
    ## 2 DSC_255     20260922 100-150_cm <NA>           <NA>           
    ## 3 DSC_239     20260922 50-100_cm  <NA>           <NA>           
    ## 4 DSC_244     20260922 300-400_cm <NA>           <NA>           
    ## 5 DSC_209           NA 15-30_cm   29.21          <NA>           
    ## 6 DSC_223           NA 15-30_cm   <NA>           <NA>           
    ## 7 DSC_210           NA 30-50_cm   <NA>           <NA>           
    ## 8 DSC_314           NA 15-30_cm   <NA>           <NA>           
    ## # ℹ 1 more variable: RESP_IS_in_bag_mass_g <chr>

## calculate the g extra field moist soil (derived from RESP_IS_g bc that’s where all the extra field moist soil is now)

``` r
redos_w_SFM_leftover = chosen_for_redo %>%
  mutate(RESP_IS_mass_g = case_when(
    Sample_ID == "DSC_259" ~ 66.87,
    Sample_ID == "DSC_340" ~ 57.37,
    Sample_ID == "DSC_239" ~ 64.52,
    Sample_ID == "DSC_248" ~ 72.71,
    Sample_ID == "DSC_209" ~ 29.21,
    Sample_ID == "DSC_308" ~ 32.93,
    Sample_ID == "DSC_210" ~ 33.58,
    Sample_ID == "DSC_220" ~ 32.89)
  ) %>%
  mutate (SFM_g_availible_for_redo = RESP_IS_mass_g - 
            (13 + # ~ mass of 50mL tube + labels, in g)
            14) # minimum needed for resp: 4 for SIR and 10 for IS
          )

redos_w_SFM_leftover
```

    ## # A tibble: 8 × 7
    ##   Sample_ID sieve_date fraction   RESP_IS_mass_g RESP_SIR_mass_g
    ##   <chr>          <dbl> <chr>               <dbl> <chr>          
    ## 1 DSC_349     20260920 300-400_cm           NA   <NA>           
    ## 2 DSC_255     20260922 100-150_cm           NA   <NA>           
    ## 3 DSC_239     20260922 50-100_cm            64.5 <NA>           
    ## 4 DSC_244     20260922 300-400_cm           NA   <NA>           
    ## 5 DSC_209           NA 15-30_cm             29.2 <NA>           
    ## 6 DSC_223           NA 15-30_cm             NA   <NA>           
    ## 7 DSC_210           NA 30-50_cm             33.6 <NA>           
    ## 8 DSC_314           NA 15-30_cm             NA   <NA>           
    ## # ℹ 2 more variables: RESP_IS_in_bag_mass_g <chr>,
    ## #   SFM_g_availible_for_redo <dbl>

Looks like we should take ~1g from DSC_209 … It would need to be closer
to 2 to get a measuremnt with the 2:1 ratio. Can we do do a clean 2?

``` r
redo_plan = redos_w_SFM_leftover %>%
    # I'm planning to take 2g soil when we have less than 5g extra and 4g soil when we have more than 5g extra
  mutate(g_into_new_pH_measurement = case_when(
    SFM_g_availible_for_redo <= 5 ~ 2,
    SFM_g_availible_for_redo > 5 ~ 4)
    ) %>% 
  select(c(Sample_ID, fraction, g_into_new_pH_measurement)) %>%

# prep this to become a datasheet
  rename(planned_soil_mass_g = g_into_new_pH_measurement) %>%
    # add blank columns 
  mutate(measured_soil_mass_g = NA,
         vol_CaCl2 = "2x measured soil mass =      ",
         pipetteable_CaCl2_mL = NA,
         pH_first_measurement = NA,
         time_first = NA, # this added manually to last iteration
         pH_second_measurement = NA,
         time_second = NA, # this added manually to last iteration
         date = NA,
         # time = NA, this removed/replaced manually in last iteration
         notes = NA) %>%
  select(Sample_ID, date, everything()) # put these columns first
```

Writing this out bc if I re-run this it will pick new random numbers

``` r
#write.csv(redo_plan, paste0("Results/pH_second_subsample_measurement_datasheet_",
#                    format(Sys.Date(), "%Y%m%d"),           
#                    ".csv"))
```

# Should I also re-subsample pH where the two measuremnts of the first sample were dissimilar?

(2026 11 04, evening)

``` r
claude_transcription = read_csv("../../../../DSC/Data/pH/soil_pH_transcription_claude_AG_edited_20261104.csv")
```

    ## Rows: 81 Columns: 17
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr   (5): Sample_ID, fraction, notes, uncertain_fields, source_pdf
    ## dbl  (10): date, mass_soil_plus_tube_g, mass_soil_original_measurement_g, av...
    ## time  (2): time_first, time_second
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
# this path is only on my local computer rn but the file is also on the drive in Transcribed_Data/pH/intermediate_draft_transcriptons_via_claude
```

``` r
drift_check = claude_transcription %>%
  mutate(drift_check = pH_first_measurement - pH_second_measurement) %>%
  filter(abs(drift_check) >= 0.15) %>% # I've talked with glade about 0.1 being not awesome and 0.2 being bad for difference between measurements
  filter(Sample_ID != "blank")

drift_check
```

    ## # A tibble: 1 × 18
    ##   Sample_ID     date mass_soil_plus_tube_g mass_soil_original_measurement_g
    ##   <chr>        <dbl>                 <dbl>                            <dbl>
    ## 1 DSC_329   20261015                  11.2                               NA
    ## # ℹ 14 more variables: avg_mass_15mL_tube_g <dbl>, soil_mass_g <dbl>,
    ## #   calcl2_to_soil_mass_ratio <dbl>, calculated_CaCl2_mL <dbl>, fraction <chr>,
    ## #   pipetteable_CaCl2_mL <dbl>, pH_first_measurement <dbl>, time_first <time>,
    ## #   pH_second_measurement <dbl>, time_second <time>, notes <chr>,
    ## #   uncertain_fields <chr>, source_pdf <chr>, drift_check <dbl>

# ok let’s also do 329 again!

# wait – that claude transcription was missing some of my datasheets

(2026 11 05 daytime)

``` r
claude_transcription = read_csv("../../../../DSC/Data/pH/soil_pH_transcription_updated_claude_AG_edited_20261105.csv")
```

    ## Rows: 96 Columns: 17
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr   (5): Sample_ID, fraction, notes, uncertain_fields, source_pdf
    ## dbl  (10): date, mass_soil_plus_tube_g, mass_soil_original_measurement_g, av...
    ## time  (2): time_first, time_second
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
# this path is only on my local computer rn but the file is also on the drive in Transcribed_Data/pH/intermediate_draft_transcriptons_via_claude
```

``` r
drift_check = claude_transcription %>%
  mutate(drift_check = pH_first_measurement - pH_second_measurement) %>%
  filter(abs(drift_check) >= 0.15) %>%
  filter(Sample_ID != "blank")

drift_check %>% select (c(Sample_ID, drift_check))
```

    ## # A tibble: 5 × 2
    ##   Sample_ID drift_check
    ##   <chr>           <dbl>
    ## 1 DSC_329        -0.160
    ## 2 DSC_206         0.270
    ## 3 DSC_224         0.240
    ## 4 DSC_318        -0.86 
    ## 5 DSC_305        -0.230

ok now there are 5 :/

are do any of these overlap with re-do’s I already planned and measured
out soil for yesterday?

``` r
weighed_1104 = read_csv("Results/pH_second_subsample_measurement_datasheet_20261104.csv")
```

    ## New names:
    ## Rows: 8 Columns: 13
    ## ── Column specification
    ## ──────────────────────────────────────────────────────── Delimiter: "," chr
    ## (3): Sample_ID, fraction, vol_CaCl2 dbl (2): ...1, planned_soil_mass lgl (8):
    ## date, measured_soil_mass, pipetteable_CaCl2_mL, pH_first_measuremen...
    ## ℹ Use `spec()` to retrieve the full column specification for this data. ℹ
    ## Specify the column types or set `show_col_types = FALSE` to quiet this message.
    ## • `` -> `...1`

``` r
drift_check$Sample_ID %in% weighed_1104$Sample_ID
```

    ## [1] FALSE FALSE FALSE FALSE FALSE

``` r
weighed_1104$Sample_ID %in% drift_check$Sample_ID
```

    ## [1] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE

``` r
# all false:(
```

ok so what if I needed to re-do all of these? would we have enough soil?

## calculate the g extra field moist soil (derived from RESP_IS_g bc that’s where all the extra field moist soil is now)

first we need to add RESP_IS_mass_g. I transcribed this manually last
time, now I have this super rough, unverified claude transcription file
that laura got for me. I’m going to have claude also pull the values
from it for me

``` r
tentative_claude_yz_psm = read_csv("../../../TidyData/Metadata/make_per_sample_metadata_september/Results/UNREVIEWED_barely_tidied_claude_transcription_YZ_sample_data_20261105_AG.csv") 
```

    ## Rows: 54 Columns: 45
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (39): Sample_ID, Scribe_initials, Siever_1_initials, Siever_2_initials,...
    ## dbl   (5): sieve_date, S_pH_mass_g, large_tin_mass_full_g_5, large_tin_mass_...
    ## time  (1): everything_cool_time
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
resp_masses_for_drift_redos = tentative_claude_yz_psm %>%
  filter(Sample_ID %in% drift_check$Sample_ID) %>%
  select(c(Sample_ID, RESP_IS_mass_g))

resp_masses_for_drift_redos
```

    ## # A tibble: 1 × 2
    ##   Sample_ID RESP_IS_mass_g
    ##   <chr>     <chr>         
    ## 1 DSC_329   67.61

why is there only one? ugggh I checked the raw file I got from Laura
from claude and it only has data for 54 samples (out of our 80-100ish,
can’t remember). bummer.

ok which of these might be yz samples? because then I’m pretty sure we
have enough to re-do them regardless.

``` r
drift_check_w_fraction = sept_xy_psm_w_fractions %>%
  filter(Sample_ID %in% drift_check$Sample_ID) %>%
  select(c(Sample_ID, sieve_date, fraction, starts_with("RESP")))

drift_check_w_fraction
```

    ## # A tibble: 5 × 6
    ##   Sample_ID sieve_date fraction   RESP_IS_mass_g RESP_SIR_mass_g
    ##   <chr>          <dbl> <chr>      <chr>          <chr>          
    ## 1 DSC_329     20260919 150-200_cm 67.61g         <NA>           
    ## 2 DSC_206           NA 15-30_cm   31.75          <NA>           
    ## 3 DSC_224           NA 30-50_cm   <NA>           <NA>           
    ## 4 DSC_318           NA 30-50_cm   <NA>           <NA>           
    ## 5 DSC_305           NA 0-15_cm    <NA>           <NA>           
    ## # ℹ 1 more variable: RESP_IS_in_bag_mass_g <chr>

ahh it’s only DSC_329 – that explains why the big claude transcription
didn’t include them, that was just yz metadata.

329 is the only one I can be sure is ok to re-do, then, without digging
manually through the ox datasheets or asking claude to do more
transcription

With it, I’ll have 8 left to do + 8 re-dos planned yesterday + DSC_329 =
17 samples, which is a good number for a day of pH measurements.

I can come back to 206, 224, 318, and 305 later if I need to. So I’m
just going to add DSC_239 manually to the sheet I made yesterday(Nov 4),
move that soil over, and start making measurements for these 17 tubes.
