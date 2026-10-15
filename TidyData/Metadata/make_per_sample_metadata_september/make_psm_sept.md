make_psm_sept
================

I’m using this document to wrangle the masses of the pH tubes ahead of
taking pH measurements, but I’m hoping it will be a good baseline to
make the september per-sample metadata (psm) later

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
library(googledrive)
```

# Download data

I partially transcribed the lab sample data (just enough to have pH for
every non-litter sample).

``` r
name_of_transcription_file_on_drive = "O_sample_data_lab_transcribed_20261014_AG" # store a copy of the file's name, exactly as it appears on Google Drive

dribble = drive_get(name_of_transcription_file_on_drive, shared_drive = 'Bogar Lab') # get necessary Google Drive metadata for downloading. This stores it in a special table called a "dribble"
```

    ## ! Using an auto-discovered, cached token.

    ##   To suppress this message, modify your code or options to clearly consent to
    ##   the use of a cached token.

    ##   See gargle's "Non-interactive auth" vignette for more details:

    ##   <https://gargle.r-lib.org/articles/non-interactive-auth.html>

    ## ℹ The googledrive package is using a cached token for 'agoodman@ucdavis.edu'.

    ## ✔ The input `path` resolved to exactly 1 file.

``` r
# generate a file path and file name for the copy of the file you're about to make on your computer (in your local clone of this repository)
local_path <- paste0("Inputs/",                               # Path starts with "Inputs/" -- you may need to manually generate an Inputs sub-folder inside the folder where this .Rmd lives
                     name_of_transcription_file_on_drive,     # name starts identically to the name of the Google Drive version
                     "_downloaded-",                          # name specifies when you downlaoded the file
                     format(Sys.Date(), "%Y%m%d"),            # name includes today's date; auto-generated here
                     ".csv")                                  # name ends in file type extension. change from .csv only if this isn't a spreadsheet for some reason

# download the file from Google Drive
drive_download(dribble,             # finds the file on Google Drive, using the metadata you saved above
               type = "csv",        # specifies the kind of file to generate locally -- only change from csv if this isn't a spreadsheet
               path = local_path,   # specifies the new file path you just generated above
               overwrite = TRUE)    # maintains a maximum of one new version of this file that gets stored in the Inputs folder per day. 
```

    ## File downloaded:

    ## • 'O_sample_data_lab_transcribed_20261014_AG'
    ##   <id: 1Zafub8Pvmn5xA-2f_kA6TYeT2QQnbjCcA4AhE3qL_Tw>

    ## Saved locally as:

    ## • 'Inputs/O_sample_data_lab_transcribed_20261014_AG_downloaded-20261014.csv'

``` r
name_of_transcription_file_on_drive = "X_sample_lab_data_sept_transcribed_202610_AG" # store a copy of the file's name, exactly as it appears on Google Drive

dribble = drive_get(name_of_transcription_file_on_drive, shared_drive = 'Bogar Lab') # get necessary Google Drive metadata for downloading. This stores it in a special table called a "dribble"
```

    ## ✔ The input `path` resolved to exactly 1 file.

``` r
# generate a file path and file name for the copy of the file you're about to make on your computer (in your local clone of this repository)
local_path <- paste0("Inputs/",                               # Path starts with "Inputs/" -- you may need to manually generate an Inputs sub-folder inside the folder where this .Rmd lives
                     name_of_transcription_file_on_drive,     # name starts identically to the name of the Google Drive version
                     "_downloaded-",                          # name specifies when you downlaoded the file
                     format(Sys.Date(), "%Y%m%d"),            # name includes today's date; auto-generated here
                     ".csv")                                  # name ends in file type extension. change from .csv only if this isn't a spreadsheet for some reason

# download the file from Google Drive
drive_download(dribble,             # finds the file on Google Drive, using the metadata you saved above
               type = "csv",        # specifies the kind of file to generate locally -- only change from csv if this isn't a spreadsheet
               path = local_path,   # specifies the new file path you just generated above
               overwrite = TRUE)    # maintains a maximum of one new version of this file that gets stored in the Inputs folder per day. 
```

    ## File downloaded:

    ## • 'X_sample_lab_data_sept_transcribed_202610_AG'
    ##   <id: 1FN7pWQGFNI_RhxrWMXnit4U6w3v84rD5TORpeLv0z54>

    ## Saved locally as:

    ## • 'Inputs/X_sample_lab_data_sept_transcribed_202610_AG_downloaded-20261014.csv'

``` r
name_of_transcription_file_on_drive = "YZ_sample_data_sept_transcribed_202610_AG" # store a copy of the file's name, exactly as it appears on Google Drive

dribble = drive_get(name_of_transcription_file_on_drive, shared_drive = 'Bogar Lab') # get necessary Google Drive metadata for downloading. This stores it in a special table called a "dribble"
```

    ## ✔ The input `path` resolved to exactly 1 file.

``` r
# generate a file path and file name for the copy of the file you're about to make on your computer (in your local clone of this repository)
local_path <- paste0("Inputs/",                               # Path starts with "Inputs/" -- you may need to manually generate an Inputs sub-folder inside the folder where this .Rmd lives
                     name_of_transcription_file_on_drive,     # name starts identically to the name of the Google Drive version
                     "_downloaded-",                          # name specifies when you downlaoded the file
                     format(Sys.Date(), "%Y%m%d"),            # name includes today's date; auto-generated here
                     ".csv")                                  # name ends in file type extension. change from .csv only if this isn't a spreadsheet for some reason

# download the file from Google Drive
drive_download(dribble,             # finds the file on Google Drive, using the metadata you saved above
               type = "csv",        # specifies the kind of file to generate locally -- only change from csv if this isn't a spreadsheet
               path = local_path,   # specifies the new file path you just generated above
               overwrite = TRUE)    # maintains a maximum of one new version of this file that gets stored in the Inputs folder per day. 
```

    ## File downloaded:

    ## • 'YZ_sample_data_sept_transcribed_202610_AG'
    ##   <id: 1b6sYWiPx_oHVO9Epv6W15CR5s6izhkfXOUViwGKziT4>

    ## Saved locally as:

    ## • 'Inputs/YZ_sample_data_sept_transcribed_202610_AG_downloaded-20261014.csv'

\#Read in and preliminarily tidy data

Read in all of these as csvs, and do preliminary tidying

``` r
o_lab = read_csv("Inputs/O_sample_data_lab_transcribed_20261014_AG_downloaded-20261014.csv")
```

    ## New names:
    ## Rows: 7 Columns: 8
    ## ── Column specification
    ## ──────────────────────────────────────────────────────── Delimiter: "," chr
    ## (8): O Horizon, ...2, Date: 23 Sept 2026, ...4, Person: Glade Bogar, ......
    ## ℹ Use `spec()` to retrieve the full column specification for this data. ℹ
    ## Specify the column types or set `show_col_types = FALSE` to quiet this message.
    ## • `` -> `...2`
    ## • `` -> `...4`
    ## • `` -> `...6`
    ## • `` -> `...7`
    ## • `` -> `...8`

``` r
  # set column names
  colname_row_index = which(o_lab[[1]] == "Sample ID") # grab index of row with correct column names in it. 
  colnameRow = unlist(o_lab[colname_row_index,]) # isolate the values that should be column names into a vector
  colnameRow[is.na(colnameRow)] <- "unnamed_column" # handle the last column, temporarily
  colnames(o_lab) = colnameRow # set them as column names
  
  # now make the column names R friendly
o_lab = o_lab %>%
  rename(Sample_ID = 'Sample ID',
         RESP_SIR_mass_g = 'Resp_SIR mass (g)',
         pH_mass_g_soil_only = 'pH mass (g)',
         small_tin_mass_empty_g = '5\" pie tin mass (g)',
         small_tin_mass_full_g = 'tin plus FM soil mass (g)',
         RESP_IS_in_bag_mass_g = 'RESP_IS soil plus bag mass (g)',
         total_mass = 'total mass') %>%
  filter(Sample_ID != "Sample ID") # remove the row with the colnames in it

head(o_lab)
```

    ## # A tibble: 6 × 8
    ##   Sample_ID RESP_SIR_mass_g pH_mass_g_soil_only small_tin_mass_empty_g          
    ##   <chr>     <chr>           <chr>               <chr>                           
    ## 1 DSC_208   4.00            2.06                2.94                            
    ## 2 DSC_200   4.08            2.15                3.06                            
    ## 3 DSC_221   4.00            2.00                3.00                            
    ## 4 DSC_213   4.00            2.00                3.00                            
    ## 5 DSC_204   all remaining   0.99                0 due to low mass, priority Res…
    ## 6 DSC_217   all remaining   1.00                0 due to low mass, priority Res…
    ## # ℹ 4 more variables: small_tin_mass_full_g <chr>, RESP_IS_in_bag_mass_g <chr>,
    ## #   total_mass <chr>, unnamed_column <chr>

``` r
x_lab = read_csv("Inputs/X_sample_lab_data_sept_transcribed_202610_AG_downloaded-20261014.csv")
```

    ## New names:
    ## Rows: 41 Columns: 6
    ## ── Column specification
    ## ──────────────────────────────────────────────────────── Delimiter: "," chr
    ## (6): 0-50 cm ("X") Soil Processing in Lab, ...2, ...3, Date: 23 Septembe...
    ## ℹ Use `spec()` to retrieve the full column specification for this data. ℹ
    ## Specify the column types or set `show_col_types = FALSE` to quiet this message.
    ## • `` -> `...2`
    ## • `` -> `...3`
    ## • `` -> `...5`

``` r
  # set column names
  colname_row_index = which(x_lab[[1]] == "Sample_ID") # grab index of row with correct column names in it. 
  print(colname_row_index) #there are three of these -- we're choosing 1
```

    ## [1]  1  6 23

``` r
  colnameRow = x_lab[1,] # isolate the values that should be column names into a vector
  colnames(x_lab) = colnameRow # set them as column names

x_lab = x_lab %>%
  # remove the sheet-header rows, which were transcribed faithfully, but don't fit in the datatable
  filter(!str_detect(Sample_ID, "Soil Processing in Lab")) %>%
  # remove the other rows with correct column names
  filter(!str_detect(Sample_ID, "Sample_ID")) %>%
  
  # also put DSC at the front of Sample_IDs where the scribe omitted
  mutate(Sample_ID = case_when((str_detect(Sample_ID, "DSC") == FALSE) ~ 
                                 paste0("DSC_", Sample_ID),
                               .default = Sample_ID
                               ))

head(x_lab) #ok yay
```

    ## # A tibble: 6 × 6
    ##   Sample_ID `pH mass (g)` `5" pie tin mass (g)` FM soil plus 5" pie tin mass (…¹
    ##   <chr>     <chr>         <chr>                 <chr>                           
    ## 1 DSC_209   4.00          3.06                  13.00                           
    ## 2 DSC_215   3.90          2.92                  12.92                           
    ## 3 DSC_206   4.00          2.91                  13.18                           
    ## 4 DSC_214   4.00          <NA>                  <NA>                            
    ## 5 DSC_203   4.18          <NA>                  <NA>                            
    ## 6 DSC_218   4.32          <NA>                  <NA>                            
    ## # ℹ abbreviated name: ¹​`FM soil plus 5" pie tin mass (g)`
    ## # ℹ 2 more variables: `final RESP tube mass (g)` <chr>,
    ## #   `FM soil plus 9" pie tin mass (g)` <chr>

``` r
# now make the column names R friendly

x_lab = x_lab %>%
  rename(pH_mass_g_soil_only = 'pH mass (g)',
         small_tin_mass_empty_g = '5\" pie tin mass (g)',
         small_tin_mass_full_g = 'FM soil plus 5\" pie tin mass (g)',
         RESP_IS_mass_g = 'final RESP tube mass (g)',
         large_tin_mass_full_g = 'FM soil plus 9\" pie tin mass (g)')

head(x_lab)
```

    ## # A tibble: 6 × 6
    ##   Sample_ID pH_mass_g_soil_only small_tin_mass_empty_g small_tin_mass_full_g
    ##   <chr>     <chr>               <chr>                  <chr>                
    ## 1 DSC_209   4.00                3.06                   13.00                
    ## 2 DSC_215   3.90                2.92                   12.92                
    ## 3 DSC_206   4.00                2.91                   13.18                
    ## 4 DSC_214   4.00                <NA>                   <NA>                 
    ## 5 DSC_203   4.18                <NA>                   <NA>                 
    ## 6 DSC_218   4.32                <NA>                   <NA>                 
    ## # ℹ 2 more variables: RESP_IS_mass_g <chr>, large_tin_mass_full_g <chr>

Read in the yz lab data and get the sample_id’s arranged correctly

``` r
# read csv into enviornment
yz_lab = read_csv("Inputs/YZ_sample_data_sept_transcribed_202610_AG_downloaded-20261014.csv")
```

    ## New names:
    ## Rows: 41 Columns: 55
    ## ── Column specification
    ## ──────────────────────────────────────────────────────── Delimiter: "," chr
    ## (55): sieve_date, 20260919...2, 20260919...3, 20260919...4, 20260919...5...
    ## ℹ Use `spec()` to retrieve the full column specification for this data. ℹ
    ## Specify the column types or set `show_col_types = FALSE` to quiet this message.
    ## • `20260919` -> `20260919...2`
    ## • `20260919` -> `20260919...3`
    ## • `20260919` -> `20260919...4`
    ## • `20260919` -> `20260919...5`
    ## • `20260919` -> `20260919...6`
    ## • `20260919` -> `20260919...7`
    ## • `20260919` -> `20260919...8`
    ## • `20260920` -> `20260920...9`
    ## • `20260920` -> `20260920...10`
    ## • `20260920` -> `20260920...11`
    ## • `20260920` -> `20260920...12`
    ## • `20260920` -> `20260920...13`
    ## • `20260920` -> `20260920...14`
    ## • `20260920` -> `20260920...15`
    ## • `20260920` -> `20260920...16`
    ## • `20260920` -> `20260920...17`
    ## • `20260920` -> `20260920...18`
    ## • `20260920` -> `20260920...19`
    ## • `20260920` -> `20260920...20`
    ## • `20260921` -> `20260921...21`
    ## • `20260921` -> `20260921...22`
    ## • `20260921` -> `20260921...23`
    ## • `20260921` -> `20260921...24`
    ## • `20260921` -> `20260921...25`
    ## • `20260921` -> `20260921...26`
    ## • `20260921` -> `20260921...27`
    ## • `20260921` -> `20260921...28`
    ## • `20260921` -> `20260921...29`
    ## • `20260921` -> `20260921...30`
    ## • `20260921` -> `20260921...31`
    ## • `20260921` -> `20260921...32`
    ## • `20260921` -> `20260921...33`
    ## • `20260922` -> `20260922...34`
    ## • `20260922` -> `20260922...35`
    ## • `20260922` -> `20260922...36`
    ## • `20260922` -> `20260922...37`
    ## • `20260922` -> `20260922...38`
    ## • `20260922` -> `20260922...39`
    ## • `20260922` -> `20260922...40`
    ## • `20260922` -> `20260922...41`
    ## • `20260922` -> `20260922...42`
    ## • `20260922` -> `20260922...43`
    ## • `20260922` -> `20260922...44`
    ## • `20260922` -> `20260922...45`
    ## • `20260922` -> `20260922...46`
    ## • `20260922` -> `20260922...47`
    ## • `20260922` -> `20260922...48`
    ## • `20260922` -> `20260922...49`
    ## • `20260923` -> `20260923...50`
    ## • `20260923` -> `20260923...51`
    ## • `20260923` -> `20260923...52`
    ## • `20260923` -> `20260923...53`
    ## • `20260923` -> `20260923...54`
    ## • `20260923` -> `20260923...55`

``` r
# add "DSC_" at the beginning of Sample_IDs wherever scribes omitted it

sampleIDrow_index = which(yz_lab[[1]] == "Sample_ID") # grab index of row with ids in it
sampleIDrow = yz_lab[sampleIDrow_index,] # isolate sample_IDs into a vector

for (i in 2:length(sampleIDrow)) { # iterate through that vector. skip the first value (which is "Sample_ID".) for each other value:
  sampleIDrow[i] = as.character(sampleIDrow[i]) # make sure its datatype is character
  if (str_detect(sampleIDrow[i], "DSC_") == FALSE) { #check if it already has "DSC" in it. If not:
    sampleIDrow[i] = paste0("DSC_", sampleIDrow[i]) # add it to the front
  }
}

# set the column names as these Sample_IDs
colnames(yz_lab) = sampleIDrow
yz_lab = yz_lab %>%
  rename(futureColumn = Sample_ID) %>% # making the sampleID row the column names means that the column with all the other variable names in has "Sample_ID" as a column name; change this column name to "futureColumn"
  dplyr::slice(-sampleIDrow_index) # remove the sample ID row from the dataframe, it's redundant now

head(yz_lab)
```

    ## # A tibble: 6 × 55
    ##   futureColumn   DSC_352 DSC_329 DSC_335 DSC_337 DSC_259 DSC_331 DSC_341 DSC_246
    ##   <chr>          <chr>   <chr>   <chr>   <chr>   <chr>   <chr>   <chr>   <chr>  
    ## 1 Scribe_initia… SSS     BKL     BKL     LRA     placeh… placeh… placeh… placeh…
    ## 2 Siever_1_init… BKL     SJS     SJS     AG      <NA>    <NA>    <NA>    <NA>   
    ## 3 Siever_2_init… KPG     <NA>    KPG     LR      <NA>    <NA>    <NA>    <NA>   
    ## 4 Siever_3_init… <NA>    <NA>    <NA>    <NA>    <NA>    <NA>    <NA>    <NA>   
    ## 5 Lroot_empty_m… 20.37   20.12   20.09g  14.04g  <NA>    <NA>    <NA>    <NA>   
    ## 6 rock_empty_ma… 14.86   14.64   14.40g  14.47g  <NA>    <NA>    <NA>    <NA>   
    ## # ℹ 46 more variables: DSC_359 <chr>, DSC_349 <chr>, DSC_340 <chr>,
    ## #   DSC_333 <chr>, DSC_350 <chr>, DSC_334 <chr>, DSC_261 <chr>, DSC_355 <chr>,
    ## #   DSC_356 <chr>, DSC_343 <chr>, DSC_348 <chr>, DSC_252 <chr>, DSC_240 <chr>,
    ## #   DSC_251 <chr>, DSC_245 <chr>, DSC_353 <chr>, DSC_250 <chr>, DSC_336 <chr>,
    ## #   DSC_358 <chr>, DSC_338 <chr>, DSC_258 <chr>, DSC_254 <chr>, DSC_332A <chr>,
    ## #   DSC_332B <chr>, DSC_256 <chr>, DSC_242 <chr>, DSC_249 <chr>, DSC_357 <chr>,
    ## #   DSC_255 <chr>, DSC_339 <chr>, DSC_260B <chr>, DSC_351 <chr>, …

``` r
yz_lab = yz_lab %>%
  
  pivot_longer(cols = !futureColumn, 
               names_to = "Sample_ID", 
               values_to = "value"
               ) %>% # pivot longer first, making a column called Sample_ID. # this was breaking because I didn't have characters in all my columns, so I put "placeholderChar" in the Scribe_initials field of the data transcription sheet for now
  pivot_wider(names_from = futureColumn, 
              values_from = value) # then wider, making each variable its own column

head(yz_lab)
```

    ## # A tibble: 6 × 41
    ##   Sample_ID Scribe_initials Siever_1_initials Siever_2_initials
    ##   <chr>     <chr>           <chr>             <chr>            
    ## 1 DSC_352   SSS             BKL               KPG              
    ## 2 DSC_329   BKL             SJS               <NA>             
    ## 3 DSC_335   BKL             SJS               KPG              
    ## 4 DSC_337   LRA             AG                LR               
    ## 5 DSC_259   placeholderChar <NA>              <NA>             
    ## 6 DSC_331   placeholderChar <NA>              <NA>             
    ## # ℹ 37 more variables: Siever_3_initials <chr>, Lroot_empty_mass <chr>,
    ## #   rock_empty_mass <chr>, ID_check_core_datasheets <chr>,
    ## #   `fraction (if on sample tube)` <chr>, other_info_on_sample_tube <chr>,
    ## #   empty_sieve_mass_g <chr>, small_tin_mass_empty_g <chr>,
    ## #   tube_length_cm <chr>, `Horizons (+gophers?)` <chr>,
    ## #   pre_trim_photo_by <chr>, subsampling_start_time <chr>,
    ## #   post_trim_photo_by <chr>, removed_soil_mass_g <chr>, …

Take unit suffixes off of yz data

``` r
yz_lab = yz_lab %>%
  mutate(S_pH_mass_g = str_remove(S_pH_mass_g, "g")) # just pH for now
```

# Join data

## just pH for now

``` r
yz = select(yz_lab, c(Sample_ID, S_pH_mass_g))
x = select(x_lab, c(Sample_ID, pH_mass_g_soil_only))
o = select(o_lab, c(Sample_ID, pH_mass_g_soil_only))
```

``` r
psm_ph = full_join(yz, x) %>%
  full_join(o)
```

    ## Joining with `by = join_by(Sample_ID)`
    ## Joining with `by = join_by(Sample_ID, pH_mass_g_soil_only)`

``` r
psm = full_join(yz_lab, x_lab) %>%
  full_join(o_lab)
```

    ## Joining with `by = join_by(Sample_ID, small_tin_mass_empty_g, RESP_IS_mass_g,
    ## small_tin_mass_full_g)`
    ## Joining with `by = join_by(Sample_ID, small_tin_mass_empty_g,
    ## small_tin_mass_full_g, pH_mass_g_soil_only)`

``` r
head(psm)
```

    ## # A tibble: 6 × 47
    ##   Sample_ID Scribe_initials Siever_1_initials Siever_2_initials
    ##   <chr>     <chr>           <chr>             <chr>            
    ## 1 DSC_352   SSS             BKL               KPG              
    ## 2 DSC_329   BKL             SJS               <NA>             
    ## 3 DSC_335   BKL             SJS               KPG              
    ## 4 DSC_337   LRA             AG                LR               
    ## 5 DSC_259   placeholderChar <NA>              <NA>             
    ## 6 DSC_331   placeholderChar <NA>              <NA>             
    ## # ℹ 43 more variables: Siever_3_initials <chr>, Lroot_empty_mass <chr>,
    ## #   rock_empty_mass <chr>, ID_check_core_datasheets <chr>,
    ## #   `fraction (if on sample tube)` <chr>, other_info_on_sample_tube <chr>,
    ## #   empty_sieve_mass_g <chr>, small_tin_mass_empty_g <chr>,
    ## #   tube_length_cm <chr>, `Horizons (+gophers?)` <chr>,
    ## #   pre_trim_photo_by <chr>, subsampling_start_time <chr>,
    ## #   post_trim_photo_by <chr>, removed_soil_mass_g <chr>, …

``` r
write.csv(psm, paste0("Results/psm_sept",
                      "_tidied-",                          
                     format(Sys.Date(), "%Y%m%d"),           
                     ".csv")
)
```
