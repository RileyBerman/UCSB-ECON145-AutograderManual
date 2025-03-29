# DGP.R {#DGP.R}

In the autograder system, the `DGP.R` file assigns each student a unique dataset drawn from the `masterdata` according to their PERMID. 

See `00-manual1.pdf` and the [PERMID Check](#PERMID-Check) for more information.

:::: {.infobox .note data-latex="{bell_note}"}

**Note**: 

The Head TA determines the sampling method for each assignment. TAs are *only* responsible for implementing this sampling technique in `DGP.R`. 

In some cases, the file does not require any edits.  

::::

## Examples 

A few examples of `DGP.R` are provided below.

### Example 1 {-}

**Description**: Randomly assign each student 1,000 observations sampled from `masterdata`. 


``` r
DGP <- function(masterdata = "Masterdata.csv", PERMID = PERMID, dataname = dataname){
  masterdata <- read_csv(masterdata)
  set.seed(PERMID)
  #---------------#
  #Edit made here... 
  d <- masterdata[sample(1:nrow(masterdata), 1000, replace = F),]
  #---------------#
  readr::write_csv(d, dataname)
}
```

### Example 2 {-}

**Description**: Assign the full `masterdata` to each student (no sampling). This results in all students receiving the same dataset (adapted from Homework 4). 


``` r
DGP <- function(masterdata = "Masterdata.csv", PERMID = PERMID, dataname = dataname){
  masterdata <- read_csv(masterdata)
  set.seed(PERMID)
  #---------------#
  #Edit made here... 
  d <- masterdata
  #---------------#
  readr::write_csv(d, dataname)
}
```

### Example 3 {-}

**Description**: Randomly assign each student 20,000 observations sampled from every `masterdata` *except* for the `masterdata` corresponding to `datasetB.csv`.[^6]

For the `masterdata` corresponding to `datasetB.csv`, assign each student the full `masterdata`. Assume that `datasetB.csv` has exactly two columns, `col_1` and `col_2` (adapted from Homework 7). 

[^6]: Sometimes, multiple csv files are required for an assignment, creating multiple `masterdata`. The `DGP.R` file will then loop through each `masterdata`. 


``` r
DGP <- function(masterdata = "Masterdata.csv", PERMID = PERMID, dataname = dataname){
  masterdata <- read_csv(masterdata)
  set.seed(PERMID)
  #---------------#
  #Edit made here... 
  #No sampling for datasetB.csv... 
  if(all(colnames(masterdata) %in% c("col_1", "col_2"))){
    d <- masterdata
  #Everything else is randomly sampled...
  } else{
    d <- masterdata[sample(1:nrow(masterdata), 20000, replace = F),]
  }
  #---------------#
  readr::write_csv(d, dataname)
}
```
 
### Example 4 {-}

**Description**: For the following `masterdata`, assign as follows:

-   `data1.csv`: Randomly sample 10,000 rows.

-   `data2.csv`: Randomly sample 20,000 rows.

-   `data3.csv`: Assign the full dataset, arrange by its column `data3_col2` (so every student will get their dataset in this format).

-   `data4.csv`: Assign the full dataset.  

Assume `data1.csv` has some unique column `data1_unique_column` and that `data2_col1`, ... ,`data2_col5` are the *only* columns in `data2.csv` and `data3_col1` and `data3_col2` are the *only* columns in `data3.csv` (adapted from Homework 14).  


``` r
DGP <- function(masterdata = "Masterdata.csv", PERMID = PERMID, dataname = dataname){
  masterdata <- read_csv(masterdata)
  set.seed(PERMID)
  #---------------#
  #Edit made here... 
  #`data1.csv` randomly sampled for 10,000 observations 
  if("data1_unique_column" %in% colnames(masterdata)){
    d <- masterdata[sample(1:nrow(masterdata), 10000, replace = F),]
  #`data2.csv` randomly sampled for 20,000 observations 
  } else if(all(colnames(masterdata) %in% c("data2_col1", "data2_col2", 
                                            "data2_col3", "data2_col4", "data2_col5"))){ 
    d <- masterdata[sample(1:nrow(masterdata), 20000, replace = F),]
  #`data3.csv` is not sampled and is arranged by `data3_col2`
  } else if(all(colnames(masterdata) %in% c("data3_col1", "data3_col2"))){
    d <- masterdata %>% arrange(data3_col2)
  #`data4.csv` is not sampled
  } else{
    d <- masterdata[sample(1:nrow(masterdata), nrow(masterdata), replace = F),]
  }
  #---------------#
  readr::write_csv(d, dataname)
}
```

### Example 5 {-}

**Description**: Assign each student a dataset containing observations from 140 randomly selected countries from `masterdata`'s `Country or Area` column (adapted from Homework 8). 


``` r
DGP <- function(masterdata = "Masterdata.csv", PERMID = PERMID, dataname = dataname){
  masterdata <- read_csv(masterdata)
  set.seed(PERMID)
  #---------------#
  #Edit made here... 
  #Creating a list of all the countries in `masterdata`
  country_list <- unique(masterdata$`Country or Area`)
  #Randomly selecting 140 countries
  country_sample <- sample(country_list, 140)
  #Selecting the rows from `masterdata` that contain the selected countries
  d <- masterdata[masterdata$`Country or Area` %in% country_sample, ]
  #---------------#
  readr::write_csv(d, dataname)
}
```
