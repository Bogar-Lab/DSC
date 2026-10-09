# DSC_code

Data tidying and analysis for the UC Davis Bogar Lab's NSF-MMORCC "Deep Soil Carbon" project.

Where things are:

- Original data (scanned datasheets, lab notebooks, photos) --> Bogar Lab *Google Drive*
- Transcribed data --> Bogar Lab *Google Drive* (google sheets) AND *here*, in TidyData/[relevant category]/Inputs

- Tidied data --> *here*, TidyData/
- Data analysis --> *here*
  - Code for analyzing data, making tables, etc: Analysis/
  - Code for making figures: Figures/
- Figures and tables --> *here*, in Reports/ AND in /Results sub-folders associated with the scripts that generated them (in Figures/ and Analysis/)

- Prose reports or presentations --> Bogar Lab *Google Drive*

For contributors:

- Google Drive integration! To to bring transcribed data from google drive into this repo, use this code:
    - substitute these terms: name_of_google_sheet, name_of_google_sheet
```{r}
library(googledrive)

name_of_transcription_file_on_drive = "name_of_google_sheet" # copy the file's name exactly as it appears on google drive

dribble = drive_get(name_of_transcription_file_on_drive, shared_drive = 'Bogar Lab') # get necesary data for downloading

local_path <- paste0("Inputs/", 
                     name_of_transcription_file_on_drive, 
                     "_downloaded-", 
                     format(Sys.Date(), "%Y%m%d"), 
                     ".csv") #define what this local file name will be, with today's date

drive_download(dribble, 
               type = "csv", 
               path = local_path, 
               overwrite = TRUE) # maintains one copy of this file, stored in the inputs folder, per day

my_transcribed_data = read_csv(local_path, col_names = FALSE) # reads the csv (now stored on your machine) into your R enviornment
```