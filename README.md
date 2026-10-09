# DSC code

Data tidying and analysis for the UC Davis Bogar Lab's NSF-MMORCC "Deep Soil Carbon" project.

Where things are:

- Original data (scanned datasheets, lab notebooks, photos) --> Bogar Lab **Google Drive**
- Transcribed data --> Bogar Lab **Google Drive** (google sheets) AND **here**, in TidyData/[relevant category]/Inputs
\n
- Tidied data --> **here**, TidyData/
- Data analysis --> **here**
  - Code for analyzing data, making tables, etc: Analysis/
  - Code for making formal figures: Figures/
- Formal figures and tables --> **here**, in Reports/ AND in /Results sub-folders associated with the scripts that generated them (in Figures/ and Analysis/)

- Prose reports or presentations --> Bogar Lab **Google Drive**
\n
\n
For contributors:

- Create a new branch when:
  - You're editing anything in TidyData (these files are upstream of most things in Analysis)

- Write your code in .Rmd files, with the following settings/fomrat
  - "knit on save" selected
  - output set to "github_document", such that the header looks something like:
---
title: "pH_analysis"
output: github_document
---
  - ideally, name your code chunks -- at least the ones that produce

- Use this Google Drive integration! 
    - Only use data files which are a) on the Google Drive (see below) or b) in this repository already
    - To to bring transcribed data from Google Drive into this repo, use this code:
      - you'll need to replace name_of_google_sheet and my_transcribed_data, leave everything else as-is
```{r}
library(googledrive)

name_of_transcription_file_on_drive = "name_of_google_sheet" # store a copy of the file's name, exactly as it appears on Google Drive

dribble = drive_get(name_of_transcription_file_on_drive, shared_drive = 'Bogar Lab') # get necessary Google Drive metadata for downloading. This stores it in a special table called a "dribble"

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

my_transcribed_data = read_csv(local_path, col_names = FALSE) # reads the version of the file you downloaded today into your R environment (from your computer). You'll need a different function if it's not a csv
```
