# Putting It All Together {#Putting-It-All-Together}

The following are examples of autograder code for a Public Question involving a tibble, vector, and list respectively, incorporating many of the [General](#General-Checks) and [Special Checks](#Special-Checks) discussed earlier.

## Example: Tibble

Consider the following question from Homework 9.[^2]

[^2]: Technically, this is the *fifth* question of the assignment, but I have modified it to be the fourth to align with its question number in `test.results[#, ]`. 

**Part 1: Coding Assignment**

4. (*Public Question*) Next, using `un_data_tfr` create a new tibble called **`un_data_tfr_gdp`** that contains a column for the year, a column for the region, and a column for the average GDP per capita in each region for each year. The new column should be called **`gdp_per_capita`**, and should be created by dividing the sum of GDP by the sum of population for each region and year.  

The answer should look something like: 


``` r
un_data_tfr_gdp <- un_data_tfr |>
  summarise(gdp_per_capita = weighted.mean(gdp_per_capita, population, na.rm = T),
            .by = c(year, region))
```

Assume this question is worth 20 points and that `un_data_tfr` was created in Question 2.

Then, the autograder code for this question could look like:


``` r
#Autograder Code for Part 1 Question 4----------

#Initializing `test.results[4, ]`
test.results[4, ] <- c("Part 1 Question 4 (Public)", 0, 20, "Try again. Hint: Start with summarize().")
  
#Prerequisite Check
if(test.results[2, 2] == 0){
  test.results[4, 4] <- "This question depends on `un_data_tfr` from Question 2 being correct. Try again."
#Name Check
} else if(is.error(un_data_tfr_gdp)){
  test.results[4, 4] <- "`un_data_tfr_gdp` is not found. Please make sure to name the variable correctly. Hint: Check spelling and capitalization."
#Structure Check (for a tibble)
} else if(!is_tibble(un_data_tfr_gdp)){
  test.results[4, 4] <- "`un_data_tfr_gdp` is not a tibble. Please make sure it is a tibble."
#Column Name Check
} else if(!all(colnames(un_data_tfr_gdp_test) %in% colnames(un_data_tfr_gdp))){
  test.results[4, 4] <- paste0(c("The following column(s) should be in un_data_tfr_gdp, but they were not found in your answer:",
                                 colnames(un_data_tfr_gdp_test)[!(colnames(un_data_tfr_gdp_test) %in% colnames(un_data_tfr_gdp))],
                                 ". Hint: Use summarize() with appropriate .by = argument (besides gdp_per_capita, the other two columns that you need will be 
                                 your .by argument, in the form of a vector.)"), collapse = " ")
} else if(!all(colnames(un_data_tfr_gdp) %in% colnames(un_data_tfr_gdp_test))){
  test.results[4, 4] <- paste0(c("The following column(s) should not be in un_data_tfr_gdp, but they were found in your answer :",
                                 colnames(un_data_tfr_gdp)[!(colnames(un_data_tfr_gdp) %in% colnames(un_data_tfr_gdp_test))],
                                 ". Hint: Use summarize() with appropriate .by= argument (besides gdp_per_capita, the other two columns that you need will be 
                                 your .by argument, in the form of a vector.)"), collapse = " ")
#NA Check 
} else if(sum(is.na(un_data_tfr_gdp$gdp_per_capita)) > 0){
  test.results[4, 4] <- "There are `NA` values in your `gdp_per_capita` column. Hint: You can do one of the following: (1) filter out all rows with missing gdp (or gdp_per_capita) and population values first, (2) set na.rm correctly when necessary."
#Type Check (numeric)
} else if(!is.numeric(un_data_tfr_gdp$gdp_per_capita)){
  test.results[4, 4] <- "`gdp_per_capita` should be a numeric column."
#Calculation Check 
} else if(isTRUE(all.equal((un_data_tfr_gdp$gdp_per_capita / 1000) |> sort(),
                            un_data_tfr_gdp_test$gdp_per_capita |> sort()))){
  test.results[4, 4] <- "The `gdp_per_capita` column is incorrect. Hint: If you used `gdp` to calculate `gdp_per_capita` column in summarize(), don't forget that `population` is in terms of thousands of people."
#Value Check 
} else if(!isTRUE(all.equal(un_data_tfr_gdp$gdp_per_capita |> sort(),
                            un_data_tfr_gdp_test$gdp_per_capita |> sort(),
                            tolerance = 0.001))){
  test.results[4, 4] <- "The `gdp_per_capita` column is incorrect. Hint: Within summarize(), try weighted.mean(). Or you can use sum() to implement a weighted average. Note: If you used sum(), you will need to remove rows that have a missing gdp or population value in the first place."
#Row Check 
} else if(nrow(un_data_tfr_gdp) != nrow(un_data_tfr_gdp_test)){
  test.results[4, 4] <- "The number of rows in un_data_tfr_gdp is not correct. Hint: Did you use the summarize() function correctly?"
#Correct Check 
} else if(isTRUE(all.equal(un_data_tfr_gdp |> ungroup() |>
                           select(colnames(un_data_tfr_gdp_test)) |>
                           arrange(across(everything())),
                           un_data_tfr_gdp_test |> ungroup() |> 
                           select(colnames(un_data_tfr_gdp_test)) |>
                           arrange(across(everything())),
                           tolerance = 0.001,
                           check.attributes = F))){
  test.results[4, 2] <- 20
  test.results[4, 4] <- "Well done!"
}
```

## Example: Vector

Consider the following question from Homework 2. 

**Part 1: Coding Assignment**

2.  (*Public Question*) Using the function `sample`, randomly sample with replacement numbers from **`key`** and save this vector as **`index`**. The length of **`index`** should be 3. 

The answer should look something like:


``` r
index <- sample(key, size = 3, replace = TRUE)
```

Assume this question is worth 10 points and that `key` was created in Question 1.

Then, the autograder code for this question could look like:


``` r
#Autograder Code for Part 1 Question 2----------
  
#Initializing `test.results[2, ]`
test.results[2, ] <- c("Part 1 Question 2 (Public)", 0, 10, "Try again.")

#Prerequisite Check 
if(test.results[1, 2] == 0){
  test.results[2, 4] <- "This problem depends on Part 1 Question 1 being correct. Try again."
#Name Check 
} else if(is.error(index)){ 
  test.results[2, 4] <- "`index` is not found. Please make sure your variable is named correctly."
#Structure Check (for a vector)
} else if(!is.vector(index, mode = "any")){ 
  test.results[2, 4] <- "Please make sure `index` is a vector."
#Structure Check (for a numeric vector)
} else if(!is.vector(index, mode = "numeric")){ 
  test.results[2, 4] <- "Please make sure `index` is a numeric vector."
#Length Check (i.e., Row & Column Check for a vector)
} else if(length(index) != length(index_test)){ 
  test.results[2, 4] <- "Please make sure the vector `index` is the correct length, as specified by the prompt (i.e., there are 3 elements in the vector)."
#Value Check (for a vector)
} else if(!all(index %in% index_test)){ 
  test.results[2, 4] <- "`index` has incorrect values. Hint: did you use the argument replace = TRUE in the `sample()` function?"
#Correct Check
} else if(isTRUE(all.equal(index, index_test, 
                           tolerance = 0.001, check.attributes = F))){ 
  test.results[2, 4] <- "Well done!"
  test.results[2, 2] <- 10
}
```

## Example: List

Consider the following question from Homework 7. 

**Part 1: Coding Assignment**

3. (*Public Question*) Create a table called **`crime_race_counts`** that shows the number of crimes along with the percentage of crimes that were committed against victims of each race in `victim_race`. Your percentages should be out of 100 and rounded to one decimal place. You should remove any rows that do not record the victim's race. 

The answer should look something like:


``` r
crime_race_counts <- crimeData_clean |> 
  filter(!is.na(vict_race)) |> 
  tabyl(vict_race) |>
  adorn_pct_formatting()
```

Assume this question is worth 10 points and that `crimeData_clean` was created in Question 1.

Then, the autograder code for this question could look like:


``` r
#Complete Autograder Code for Part 1 Question 3----------

#Initializing `test.results[3, ]`
test.results[3, ] <- c("Part 1 Question 3 (Public)", 0, 10, "Try again. Hint: Look at the function `janitor::tabyl()`.")
  
#Prerequisite Check
if(test.results[1, 2] == 0){
  test.results[3, 4] <- "This question depends on Question 1 being correct. Try again."
#Name Check
} else if(is.error(crime_race_counts)){ 
  test.results[3, 4] <- "`crime_race_counts` is not found. Please make sure the variable is named correctly."
#Structure Check (for a list)
} else if(!is.list(crime_race_counts)){
  test.results[3, 4] <- "`crime_race_counts` is not a list. Make sure it is a list. Hint: Look at the function `janitor::tabyl()`."
#Named List Check (i.e., to make sure the list is named)
} else if(is.null(names(crime_race_counts))){
    test.results[3, 4] <- "`crime_race_counts` has no names. Make sure it has names. Hint: Look at the function `janitor::tabyl()`."
#NA Check
} else if(NA %in% crime_race_counts$vict_race){
    test.results[3, 4] <- "The column `vict_race` contains `NA` values. Remember to remove any rows which do not record the victim’s race."
#List Name Check (i.e., Column Name Check for a list)
} else if(!all(names(crime_race_counts_test) %in% names(crime_race_counts))){
  test.results[3, 4] <- paste0(paste0(c("The following column name(s) should be in `crime_race_counts`, but they were not found in your answer:",
                               names(crime_race_counts_test)[!(names(crime_race_counts_test) %in% names(crime_race_counts))]),
                               collapse = "  "), ". Hint: Look at the function `janitor::tabyl()`.")
} else if(!all(names(crime_race_counts) %in% names(crime_race_counts_test))){
  test.results[3, 4] <- paste0(paste0(c("The following column name(s) should not be in `crime_race_counts`, but they were found in your answer:",
                                   names(crime_race_counts)[!(names(crime_race_counts) %in% names(crime_race_counts_test))]),
                                 collapse = "  "), ". Hint: Look at the function `janitor::tabyl()`.")
#Expression Check (for % symbol)
} else if(any(!str_detect(crime_race_counts$percent, "%"))){
  test.results[3, 4] <- "The `percent` column should be formatted as a percentage. Hint: Look at the function `janitor::adorn_pct_formatting()`."
#Expression Check (for 1 decimal place)
} else if(any(str_detect(crime_race_counts$percent, "\\.\\d{2,}"))){
    test.results[3, 4] <- "The `percent` column should be formatted by 1 decimal place. Hint: Look at the argument `digits` in `janitor::adorn_pct_formatting()` (note its default value)."
#Row & Column Check (note: the Column Check is redundant here)
} else if(nrow(crime_race_counts) != nrow(crime_race_counts_test)){
  test.results[3, 4] <- "`crime_race_counts` has the incorrect number of rows. Hint: Look at the `janitor::tabyl()` function."
} else if(ncol(crime_race_counts) != ncol(crime_race_counts_test)){ 
  test.results[3, 4] <- "`crime_race_counts` has the incorrect number of columns. Hint: Look at the `janitor::tabyl()` function."
#Correct Check
} else if(isTRUE(all.equal(crime_race_counts |> ungroup() |> 
                           select(names(crime_race_counts_test)) |>
                           arrange(across(everything())),
                           crime_race_counts_test |> ungroup() |>
                           select(names(crime_race_counts_test)) |>
                           arrange(across(everything())),
                           tolerance = 0.001,
                           check.attributes = F))){
    test.results[3, 2] <- 10
    test.results[3, 4] <- "Well done!"
}  
```
