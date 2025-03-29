# Public Questions {#Public-Questions}

Public Question Checks are designed to give students *constructive and dynamic feedback* that help them to improve their answer and progress toward the solution. 

To achieve this, Public Questions will require multiple Checks that test for particular answer properties and generate tailored feedback. 

There are two categories of Checks: [**General**](#General-Checks) and [**Special Checks**](#Special-Checks).

:::: {.infobox .def data-latex="{magnifying_glass}" #General-Checks}

**WHAT IS A GENERAL CHECK?**

A *General Check* tests for a *common* answer property that applies across many, if not all, questions. 

For example, all questions require the student to label their variable *a specific name*. Accordingly, for any Public (or Private) Question, a General Check should alert the student if their variable name does not match the name specified in the instructions (this is the [Name Check](#Name-Check)).

**We implement General Checks for all Public Questions.**

::::

:::: {.infobox .def data-latex="{magnifying_glass}" #Special-Checks}

**WHAT IS A SPECIAL CHECK?**

A *Special Check* tests for an answer property *unique* to the given question. 

For example, if a Public Question asks the student to multiply a column by a certain number, a Special Check should alert them if they use a specific, improper scale (this is the [Calculation Check](#Calculation-Check)). Note that this Check is *specific to this question* and is not a general answer property.

**We implement Special Checks on a question-by-question basis.**

::::

:::: {.infobox .note data-latex="{bell_note}"}

**Note**: 

General Checks are denoted with a "(G)" and Special Checks with an "(S)".

::::

## General Form 

A Public Question's autograder code *combines* General and Special Checks like the following:


``` r
#Public Question 2 Autograder Code
#Testing Student's Question 2 Against Question 2 Solution

#Initializing test.results[2, ]
test.results[2, ] <- c("Part 1 Question 2 (Public)", 0, 20, "Try again.")

#General Check 1
if(test_condition){ 
  code... 
#General Check 2
}else if(test_condition){ 
  code...
#Special Check 1
}else if(test_condition){ 
  code...
#Special Check 2
}else if(test_condition){ 
  code...
.
.
.
#Last General Check 
}else if(test_condition){
  code... 
}
```

We will learn a variety of Checks in the following sections.

## Name Check (G) {#Name-Check}

**Purpose**: Checks whether the student's variable has the correct name.

**Motivation**: All questions require the student to give their variable a specific name. Without a Name Check, the autograder can't match and compare the student's answer to the answer key -- and other Checks that rely on a correctly named student variable, such as the  [Column Name Check](#Column-Name-Check), may error out as a result.  


``` r
#Name Check Example

if(is.error(variable_name)){ 
  test.results[2, 4] <- "`variable_name` is not found. Please make sure the variable is named correctly. (Any additional feedback as needed.)"
}
```

#### Technicals {-}

:::: {.infobox .imp data-latex="{important_exclamation}"}

**Always include the Name Check.**

If there is no [Prerequisite Check](#Prerequisite-Check), place this Check first (the `if` statement); otherwise, it should follow immediately after (the first `else if` statement).

::::

:::: {.infobox .note data-latex="{bell_note}"}

**Note**: 

The `is.error()` function comes from the `berryFunctions` package [@R-berryFunctions]. For the ECON 145 autograder, this library should already be loaded in from the file `helper_functions/packages.R`.

::::

## Prerequisite Check (G)  {#Prerequisite-Check}

**Purpose**: Checks whether the student got the prerequisite question correct.

**Motivation**: Many questions build on previous ones. If the student did not get the "prerequisite" question(s) correct, their current answer is likely incorrect as well. This Check prompts the student to revisit their earlier work. Since the prerequisite question is often the previous question, this Check is also called the "Previous Question Check."


``` r
#Prerequisite Check Example

else if(test.results[1, 2] == 0){ #If Question 1 is incorrect...
  test.results[2, 4] <- "This question depends on Question 1 being correct. Try again. (Any additional feedback as needed.)"
}
```

#### Technicals {-}

:::: {.infobox .imp data-latex="{important_exclamation}"}

Place the Prerequisite Check first so it is triggered first.

Don't use the Prerequisite Check for the first question, or for any stand-alone questions. 

::::

**Recommendation**: If the assignment has multiple parts (e.g., Part 1, Part 2, etc.), you should clarify where the prerequisite question is in the feedback message of `test.results[#, 4]`.  

## Structure Check (G) {#Structure-Check}

**Purpose**: Checks whether the student's variable has the correct data structure (e.g., list, tibble). In most cases, this will be a tibble.

**Motivation**: Using the wrong data structure may cause the autograder to reject an otherwise correct-looking answer. It can also break Checks that expect a certain data structure, like the [Calculation Check](#Calculation-Check).


``` r
#Structure Check Example 

#For a tibble...
else if(!is_tibble(variable_name)){
  test.results[2, 4] <- "Make sure `variable_name` is a tibble. (Any additional feedback as needed.)"
}

#For a list...
#`janitor::tabyl()` and other `janitor` related functions produce a list, not a tibble
#In this case, the Structure Check should check for a list, like below
else if(!is_list(variable_name)){
  test.results[2, 4] <- "Make sure `variable_name` is a list. (Any additional feedback as needed.)"
}
```

#### Technicals {-}

:::: {.infobox .imp data-latex="{important_exclamation}"}

**Always include the Structure Check.**

A common mistake students make is using `read.csv()` instead of `read_csv()` when loading datasets into R. The problem is that the former returns a *data frame*, while the latter returns a *tibble* (a special, modern type of data frame).

::::

There are slight differences [@wickham2016] between how general data frames and tibbles behave in R. For example, tibbles do not change the type of the inputs (e.g., they never convert strings to factors) and they allow for non-syntactic names (e.g., names with spaces). These small differences may cause the autograder to reject the student's answer or inconvenience the student when solving the assignment. 

For Public Questions that involve `janitor::tabyl()` and other `janitor` related functions (e.g., `adorn_pct_formatting`, `adorn_totals`), the student's answer will be a *list, not a tibble.* In this case, the Structure Check should check for a list. For three-way `tabyl` lists, see [`private_tabyl_grader()`](#private_tabyl_grader). 

**Recommendation**: For questions that involve loading data into R, tack on a reminder in `test.results[#, 4]` for the student to use `read_csv()` instead of `read.csv()`.

## Column Name Check (G) {#Column-Name-Check}

**Purpose**: Dynamically checks whether the column names in the student's tibble (A) match those in the answer key (B). We break this test into two parts:

1.  Test whether $B \not\subset A$, that is, if the student's tibble is missing any column names from the answer key.

2.  Test whether $A \not\subset B$, that is, if the student's tibble has any additional column names not in the answer key.

**Motivation**: Many questions involve the deletion or addition of columns in a tibble.  Students often misname, omit, or forget to remove such columns. This Check helps catch these  issues early, reducing the time students spends sifting through their code for a simple mistake (e.g., a typo). 


``` r
#Column Name Check Example

#First Test (B not in A)
else if(!all(colnames(variable_name_test) %in% colnames(variable_name))){
  test.results[2, 4] <- paste0(c("The following column(s) should be in `variable_name`, but they were not found in your answer: ",
    paste0(colnames(variable_name_test)[!(colnames(variable_name_test) %in% colnames(variable_name))], collapse = ", "), ". (Any additional feedback as needed.)"), collapse = "")
} 

#Second Test (A not in B)
else if(!all(colnames(variable_name) %in% colnames(variable_name_test))){
  test.results[2, 4] <- paste0(c("The following column(s) should not be in `variable_name`, but they were found in your answer: ",
    paste0(colnames(variable_name)[!(colnames(variable_name) %in% colnames(variable_name_test))],
    collapse = ", "), ". (Any additional feedback as needed.)"), collapse = "")
}
```

#### Technicals {-}

:::: {.infobox .imp data-latex="{important_exclamation}"}

**Always include the Column Name Check.**

This Check is *essential* for subsequent Checks that rely on the student's answer having correctly named columns, like the [Correct Check](#Correct-Check).

::::

While the Column Name Check is designed for tibbles, it can easily be adapted for named lists by swapping `colnames()` for `names()`. Please note that lists should *first* be checked if they are named through a `NULL` Check like `is.null(names(variable_name))`.

## Row & Column Check (G) {#Row-Column-Check}

**Purpose**: Checks whether the student's tibble has the same number of rows and columns as the answer key. 

**Motivation**: Many questions involve the deletion or addition of rows and/or columns in a tibble. This Check alerts students, rather broadly, to a discrepancy in their number of rows and columns. Row and column mismatches can cause Checks, like the [Correct Check](#Correct-Check), to error out.


``` r
#Row & Column Check Example

#Row Check
else if(nrow(variable_name) != nrow(variable_name_test)){ 
  test.results[2, 4] <- "`variable_name` has the incorrect number of rows. (Any additional feedback as needed.)"
}

#Column Check 
else if(ncol(variable_name) != ncol(variable_name_test)){ 
  test.results[2, 4] <- "`variable_name` has the incorrect number of columns. (Any additional feedback as needed.)"
}
```

#### Technicals {-}

:::: {.infobox .imp data-latex="{important_exclamation}"}

**Always include the Row & Column Check.**

::::

Please note that the Column Check is *redundant* if it follows the [Column Name Check](#Column-Name-Check). That is, if the student's answer passes the Column Name Check, it inevitably passes the Column Check. However, there are cases where the Column Check should *precede* the Column Name Check (e.g., if the tibble is very large, it is reasonable to first ensure that the number of columns is correct before checking for column names to potentially avoid a long Column Name Check message).

You may notice that 2024 ECON 145 autograders have this redundant Column Check. This is not really necessary, but is done to completely ensure that the [Correct Check](#Correct-Check)  works smoothly.

The Row & Column Check can easily be adapted for vectors by swapping `nrow()` and `ncol()` for `length()`.

## Correct Check (G) {#Correct-Check}

**Purpose**: Checks whether the student's tibble and the answer key are *essentially* the same. If so, full points are awarded. 

**Motivation**: This Check is the most important. It allows for some flexibility between the student's answer and the answer key, such as minor variations in column order, row order, rounding, and object attributes. 


``` r
#Correct Check Example

else if(isTRUE(all.equal(variable_name |> ungroup() |> 
                         select(colnames(variable_name_test)) |> 
                         arrange(across(everything())), 
                         variable_name_test |> ungroup() |> 
                         select(colnames(variable_name_test)) |> 
                         arrange(across(everything())), 
                         tolerance = 0.001, check.attributes = F))){
  test.results[2, 2] <- 20 #Full credit
  test.results[2, 4] <- "Well done!"
}
```

#### Technicals {-}

:::: {.infobox .imp data-latex="{important_exclamation}"}

**Always include the Correct Check.**

Place this Check last (the last `else if` or the `else` statement).

This Check assumes that the student's column names match the answer key, which is why the [Column Name Check](#Column-Name-Check) is essential **beforehand**.[^1] 

::::

[^1]: **Acknowledgment**: Previously, `all_equal()` was used for this Check. However, since `all_equal()` was deprecated in `dplyr 1.1.0`, we opted to use `all.equal()` instead. Unfortunately, because `all.equal()` does not contain the `ignore_col_order` and `ignore_row_order` arguments that allow for different column and row ordering, we had to implement this flexibility manually.

We set the `tolerance` to a small value (i.e., `0.001`) to allow for some flexibility in numeric rounding, because different orders of operation can produce slightly different numbers due to how floats are handled by the computer.

The argument `check.attributes = F` ignores the attributes (essentially the additional information attached to an R object) when comparing the student's answer to the answer key. In other words, we still want to award full credit to a student's answer that looks *virtually identical* to the solution, but contains internally *different metadata*.

-   For example, the function `na.omit()` attaches an attribute to a tibble, making it internally different but visually identical to a tibble produced with `drop_na()`. Similarly, answers generated by the `tabyl()` function versus those created through a string of `mutate()` and `summarize()` functions.

The Correct Check can easily be adapted for named lists by swapping `colnames()` for `names()` and for vectors by implementing a reduced version of this Check.

## PERMID Check (G) {#PERMID-Check}

**Purpose**: Checks whether the student provided a valid PERMID.

**Motivation**: This Check is *specific* to the ECON 145 autograder. In all assignments, students must provide their PERMID (a sequence of digits) at the top of their R script, which is used to randomly generate data (see [DGP.R](#DGP.R)). This Check flags missing (the default PERMID is `1`) or generic (i.e., `1234`) student PERMIDs. 


``` r
#PERMID Check Example

#Make sure students actually entered their PERMID---------------------------
#If the student does not input PERMID as instructed by the prompt
#The default PERMID will be 1
if(isTRUE(all.equal(PERMID, 1))){
  test.results[, 4] <- "Please follow the assignment prompt and input your PERMID!"
  test.results[, 2] <- 0
} else if (isTRUE(all.equal(PERMID, 1234))){
  test.results[, 4] <- "Please follow the assignment prompt and input your PERMID!"
  test.results[, 2] <- 0
}
```

#### Technicals {-}

:::: {.infobox .imp data-latex="{important_exclamation}"}

**Always implement the PERMID Check once, at the bottom of the autograder script (below all the Public and Private Question Checks).**

::::

## Type Check (S) {#Type-Check}

**Purpose**: Checks whether a column of the student's tibble has the correct data type (e.g., numeric, character).

**Motivation**: Some questions involve the conversion of a column from one data type to another. This Check detects an incorrectly converted (or unconverted) column and can hint at its correct conversion (e.g., using `as.numeric()`). This Check is especially useful for questions that rely on a column being numeric to perform computations. 


``` r
#Type Check Example

#For a numeric column...
else if(!is.numeric(variable_name$column_name)){
  test.results[2, 4] <- "`column_name` is not numeric. (Any additional feedback as needed.)"
}

#For a character column...
else if(!is.character(variable_name$column_name)){
  test.results[2, 4] <- "`column_name` is not character string. (Any additional feedback as needed.)"
}
```

#### Technicals {-}

:::: {.infobox .imp data-latex="{important_exclamation}"}

Since the Type Check assumes that the student's answer has the correct column name, it is essential that the [Column Name Check](#Column-Name-Check) is placed **beforehand**. 

::::

For examples of how to check multiple columns simultaneously, see below.


``` r
#Dynamic Type Check Examples 

#Example 1: Checking two columns, column_A (character) and column_B (numeric)
else if(typeof(variable_name$column_A) != typeof(variable_name_test$column_A) | 
        typeof(variable_name$column_B) != typeof(variable_name_test$column_B)){
    
  typeof_check <- c(typeof(variable_name$column_A) != typeof(variable_name_test$column_A),
                    typeof(variable_name$column_B) != typeof(variable_name_test$column_B))
    
  typeof_names <- paste0(paste0(c("`column_A` (correct: character)", 
                                  "`column_B` (correct: numeric)")
                                [typeof_check], collapse = " "), 
                                ". (Any additional feedback as needed.)") 
    
  test.results[2, 4] <- paste0("The following column(s) have the incorrect data type: ", typeof_names, ".")
}

#Example 2 (Experimental): Checking every column in a tibble 
else if(!all(sapply(variable_name, typeof)[order(names(sapply(variable_name_test, typeof)))] ==
             sapply(variable_name_test, typeof)[order(names(sapply(variable_name_test, typeof)))])){
  
  typeof_check <- c(sapply(variable_name, typeof)[order(names(sapply(variable_name_test, typeof))) ==
                    sapply(variable_name_test, typeof)[order(names(sapply(variable_name_test, typeof)))]) 
  
  typeof_names <- c(sapply(variable_name_test, typeof)[order(names(sapply(variable_name_test, typeof)))] |> names())
    
  typeof_names_check <- paste0(typeof_names[!typeof_check], collapse = " ")
    
  typeof_correct_check <- paste0(sapply(typeof_names[!typeof_check], function(names) typeof(variable_name_test[[names]])), collapse = " ")
    
  test.results[2, 4] <- paste0("The following column(s) have the incorrect data type: ", typeof_names_check, ". They should be of type: ", typeof_correct_check, ". (Any additional feedback as needed.)")
}
```

## Value Check (S) {#Value-Check}

**Purpose**: Checks whether the values in a column of the student's tibble match the answer key.

**Motivation**: Some questions involve the transformation of a column's values -- for example, multiplying a column by a formula. This Check detects an incorrectly transformed (or unaltered) column and can hint at its proper transformation. The `sort()` function allows for flexible column ordering. 


``` r
#Value Check Example

else if(!isTRUE(all.equal(variable_name$column_name |> sort(na.last = T), 
                           variable_name_test$column_name |> sort(na.last = T),
                           tolerance = 0.001,
                           check.attributes = F))){
  test.results[2, 4] <- "The values of `column_name` are incorrect. (Any additional feedback as needed.)"
}
```

#### Technicals {-}

:::: {.infobox .imp data-latex="{important_exclamation}"}

Since the Value Check assumes that the student's answer has the correct column name, it is essential that the [Column Name Check](#Column-Name-Check) is placed **beforehand**. 

The Value Check is a *general check* for a column's values. For more specialized checks, see the [Calculation Check](#Calculation-Check), the [NA Check](#NA-Check), or the [Expression Check](#Expression-Check).

::::

By default, `NA` values are removed from a vector when sorted. The `na.last = T` argument ensures that the `NA` values are placed last in the sorted vector but not removed.

If it is reasonable to check every column in the tibble (e.g., a very complicated public question with many intermediate steps), you could implement a Complete Value Check, as shown below. Note that loops in `R` do consume considerable computational resources -- there are certainly other and/or better ways to do this, like through the `sapply()` function. 


``` r
#Complete Value Check Example
#By construction below, this check should be implemented as the final check (the last `else if` or the `else` statement)

else { 
  q2ccheck <- c()
      #Compares every column in the student's tibble with the corresponding answer key column...
      for (colname in colnames(variable_name)){ 
        if(!isTRUE(all.equal(variable_name[[colname]] |> sort(na.last = T), 
                             variable_name_test[[colname]] |> sort(na.last = T),
                             tolerance = 0.001,
                             check.attributes = F))){
          q2ccheck <- append(q2ccheck, colname)
        }
      }
  
#Returns a list of incorrect columns... 
  test.results[2, 4] <- paste0(c("The following columns are incorrect: ",
                                paste0(q2ccheck, collapse = ", "),
                                "(Any additional feedback as needed.)"), collapse = "")
}
```

For examples of how to check multiple columns simultaneously, see below.


``` r
#Dynamic Value Check Example 

else if(any(!isTRUE(all.equal(variable_name$column_A |> sort(na.last = T), 
                              variable_name_test$column_A |> sort(na.last = T), 
                              tolerance = 0.001, check.attributes = F))), 
            !isTRUE(all.equal(variable_name$column_B |> sort(na.last = T), 
                              variable_name_test$column_B |> sort(na.last = T), 
                              tolerance = 0.001, check.attributes = F))){ 
  
  value_check <- c(!isTRUE(all.equal(variable_name$column_A |> sort(na.last = T), 
                                     variable_name_test$column_A |> sort(na.last = T), 
                                     tolerance = 0.001, check.attributes = F)), 
                   !isTRUE(all.equal(variable_name$column_B |> sort(na.last = T), 
                                     variable_name_test$column_B |> sort(na.last = T),
                                     tolerance = 0.001, check.attributes = F)))
    
  value_names <- c("column_A", "column_B")
   
  test.results[2, 4] <- paste0(c("The following column(s) are incorrect: ", value_names[value_check], ". (Any additional feedback as needed.)"), collapse = " ")
}
```

## Calculation Check (S) {#Calculation-Check}

**Purpose**: Checks whether the values in a *numeric column* of the student's tibble have been scaled correctly. 

**Motivation**: Some questions involve the multiplying of a column by a certain factor or formula (e.g., `1000`, the `log()` function, Fahrenheit to Celsius conversion). This Check detects a *specific*, improper scale for a column (e.g., accidentally inverting a formula) and can hint at its correct transformation.  


``` r
#Calculation Check Examples

#Example 1: Checking if a column is unscaled (it should have been scaled by 1000)
else if(isTRUE(all.equal((variable_name$column_name * 1000) |> sort(na.last = T),
                          variable_name_test$column_name |> sort(na.last = T),
                          tolerance = 0.001,
                          check.attributes = F))){
  test.results[2, 4] <- "The values of `column_name` are incorrect. Hint: Did you scale this column by 1000? (Any additional feedback as needed.)"
}

#Example 2: Checking if a column inversely applied a subtraction formula
else if(isTRUE(all.equal((variable_name$column_name * -1) |> sort(na.last = T), 
                          variable_name_test$column_name |> sort(na.last = T), 
                          tolerance = 0.001, 
                          check.attributes = F))){
  test.results[2, 4] <- "The values of `column_name` are incorrect. Hint: Did you flip the subtraction formula when calculating this column? (Any additional feedback as needed.)"
}
```

#### Technicals {-}

:::: {.infobox .imp data-latex="{important_exclamation}"}

Since the Calculation Check assumes that the student's answer has the correct column name, it is essential that the [Column Name Check](#Column-Name-Check) is placed **beforehand**.

This Check also relies on the student's column being numeric, so the numeric [Type Check](#Type-Check) should be placed **before** as well. 

-   For instance, in Example 1, the column must be numeric for this line `variable_name$column_name * 1000` to work. 

::::

This is a subset of the [Value Check](#Value-Check). That is, if the Calculation Check is triggered, so too will the Value Check (but not the other way around).

Thus, for the Calculation Check to be effective, it should be placed **before** the Value Check (if both are implemented). In tandem, the Calculation Check first alerts the student to the use of a *specific*, improper scale (e.g., an unscaled column), then the Value Check alerts them to whether they have used *any* improper scale. In this way, the feedback can be more personalized. 

-   For example, what if a column conversion has multiple steps? Using both Checks can allow us to target our feedback more effectively.

Alternatively, the Calculation Check can be implemented **within** the Value Check, as shown below.


``` r
#Calculation Check combined with Value Check Example (with example hints)

#Value Check 
else if(!isTRUE(all.equal(variable_name$column_name |> sort(na.last = T), variable_name_test$column_name |> sort(na.last = T)))){
  #Calculation Check 
  if(isTRUE(all.equal((variable_name$column_name / 100) |> sort(na.last = T), 
                       variable_name_test$column_name |> sort(na.last = T), tolerance = 0.001))){
    test.results[2, 4] <- "The values of `column_name` are incorrect. Hint: Did you convert `column_name` to percentage format?"
  }
  else{
    test.results[2, 4] <- "The values of `column_name` are incorrect. Hint: (a) Look at the function `lead()`. (b) Did you use the appropriate `.by` argument when constructing `column_name`?"
  }
}
```

Please note that the Calculation Check does not have to be implemented alongside the Value Check, however, its scope is *far more limited.*

## NA Check (S) {#NA-Check}

**Purpose**: Checks whether the values in a column of the student's tibble contain `NA` values.

**Motivation**: Some questions involve the removal of `NA` values from an existing column (e.g., through `filter(!is.na(column_name))`)  or a newly constructed column (e.g., through the argument `na.rm = T`). This Check detects the presence of `NA` values in a column and can hint at their correct removal.


``` r
#NA Check Examples 

#Example 1
else if(any(is.na(variable_name$column_name))){
  test.results[2, 4] <- "The `column_name` has `NA` values. (Any additional feedback as needed.)"
}

#Example 2 (Alternative)
else if(sum(is.na(variable_name$column_name) > 0)){
  test.results[2, 4] <- "The `column_name` has `NA` values. (Any additional feedback as needed.)"
}
```

#### Technicals {-}

:::: {.infobox .imp data-latex="{important_exclamation}"}

Since the NA Check assumes that the student's answer has the correct column name, it is essential that the [Column Name Check](#Column-Name-Check) is placed **beforehand**.

::::

This is a subset of the [Value Check](#Value-Check). 

Like the [Calculation Check](#Calculation-Check), the NA Check should be placed **before** or **within** the Value Check and does not have to be implemented alongside it. 

For examples of how to check multiple columns simultaneously, see below.


``` r
#Dynamic NA Check Example (with an example hint)
#Note: Use the all() function instead of any() in examples where some `NA` values might exist in a column, but not every value in the column should be `NA`. 

else if(any(all(is.na(variable_name$column_A)) |
            all(is.na(variable_name$column_B)) |
            all(is.na(variable_name$column_C)))){
  
  wrong_cols <- c(all(is.na(variable_name$column_A)),
                  all(is.na(variable_name$column_B)),
                  all(is.na(variable_name$column_C)))
    
  checked_cols <- c("column_A", "column_B", "column_C")
    
  test.results[2, 4] <- paste0(c("The following column(s) have all NA values: ", checked_cols[wrong_cols], " Hint: Did you set na.rm = T?"), collapse = " ")
}
```

## Expression Check (S) {#Expression-Check}

**Purpose**: Checks whether the values in a *string column* of the student’s tibble are correctly transformed.

**Motivation**: Many questions involve string manipulation, formatting, and concatenation. This Check detects a *specific*, wrongly transformed attribute of the string transformation and can hint at its correct modification. This Check is one of the more *common* Special Checks and can take on many forms.


``` r
#Expression Check Examples (with example hints)

#Example 1: Checking if a column has extracted the apostrophe "s" (i.e., 's) from its values
else if(sum(str_detect(variable_name$column_name, "'s"), na.rm = T) > 0){
  test.results[2, 4] <- "There are `'s` in `column_name`. Please remove all of the `'s` when modifying `column_name`."
}

#Example 2: Checking if a column has correctly removed all observations that are not a full name (i.e., names that only contain the first initial and last name, like "D. Smith")
else if(sum(str_detect(variable_name$column_name, "\\w{1}\\.\\s{1}\\w+$"), na.rm = T) > 0){
  test.results[2, 4] <- "Make sure to remove all observations that are not a full name (e.g., D. Smith) from `column_name`. Hint: Try functions like str_replace() or grepl() with the appropriate regular expression."
}

#Example 3: Checking if a column has correctly rounded its values to the hundredths place 
else if(!all(str_detect(variable_name$column_name, "\\.\\d{2}$"))){
  test.results[2, 4] <- "All values in `column_name` should be formatted to the second decimal place. Hint: Try using the round() function with the `digits` argument."
}

#Example 4: Checking if a column has correctly converted its year values to the corresponding decade (e.g., 1987 should be converted to 1980)
else if(sum(variable_name$column_name) %% 10 != 0){
  test.results[2, 4] <- "Make sure `column_name` only contains multiples of 10 (e.g., 1950, 1960, etc.). Hint: Convert the year values into multiples of 10. For example, 1987 should be converted to 1980. Consider using the `%/%` (integer division) operator, but there are other possible solutions as well."
}
```

#### Technicals {-}

:::: {.infobox .imp data-latex="{important_exclamation}"}

Since the Expression Check assumes that the student's answer has the correct column name, it is essential that the [Column Name Check](#Column-Name-Check) is placed **beforehand**. 

::::

This is a subset of the [Value Check](#Value-Check). 

Like the [Calculation Check](#Calculation-Check) and the [NA Check](#NA-Check), the Expression Check should be placed **before** or **within** the Value Check and does not have to be implemented alongside it. 

For examples of how to check multiple columns simultaneously, see below.


``` r
#Dynamic Expression Check (with an example hint)

#Checking if three columns (column_A, column_B, column_C) have converted their values to lowercase...
else if(any(str_detect(variable_name$column_A, "[A-Z]"),
            str_detect(variable_name$column_B, "[A-Z]"),
            str_detect(variable_name$column_C, "[A-Z]"))){
  
  q2_upper_check <- c(any(str_detect(variable_name$column_A, "[A-Z]")),
                      any(str_detect(variable_name$column_B, "[A-Z]")),
                      any(str_detect(variable_name$column_C, "[A-Z]")))
  
  q2_upper_name <- c("column_A", "column_B", "column_C")

  test.results[2, 4] <- paste0(c("The following column(s) contain observations
                                that are not converted to lowercase:",
                                q2_upper_name[q2_upper_check], 
                                "Hint: use str_to_lower() when necessary."),
                                collapse = "  ")
}
```

## Miscellaneous Checks (S) {#Miscellaneous-Check}

The following are some highly specific Special Checks that fall outside the common categories. While they may not be practical, their structure could serve as a reference when creating your own custom Special Checks.


``` r
#Miscellaneous Check Examples (with example hints)

#Example 1 (From HW 6, Q3): Checking if the column `mocodes` has correctly split its observations into multiple columns (named `mocodes_1`, `mocodes_2`, etc.) that each contain at most only one 4-digit M.O. code (e.g., the entry "1049 1304 1000" should be split into "1049" in `mocodes_1`, "1304" in `mocodes_2`, and "1000" in `mocodes_3`). The `separate_wider_delim()` function would be optimal to achieve this. 
#Column Check 
else if(ncol(variable_name) != ncol(variable_name_test)){ 
  #Checks if each mocodes column has at most one M.O. code (4 digits long)
  #Checks for a mistaken `delim` argument
  if(any(str_detect(colnames(variable_name), "mocodes"))){
    mocodes_col <- variable_name |> select(contains("mocodes")) |> mutate_all(~ str_detect(., "\\d.*\\d.*\\d.*\\d.*\\d")) |> colSums(., na.rm = TRUE)
    if(sum(as.vector(mocodes_col), na.rm = TRUE) > 0){
      test.results[2, 4] <- "Each `mocodes` column should have at most one M.O. code. Hint: Look at the argument `delim` in `separate_wider_delim()`."
    }
  }
  else{
    test.results[2, 4] <- "`variable_name` has the incorrect number of columns. Hint: Look at the function `separate_wider_delim()`."
  }
}
#Column Name Check  
else if(!all(colnames(variable_name_test) %in% colnames(variable_name))){
  #Checks for a mistaken `names_sep` argument 
  if(any(!str_detect(colnames(variable_name)[str_detect(colnames(variable_name), "mocodes")], "_"))){
    test.results[2, 4] <- "Every `mocodes` column should have an `_` between `mocodes` and its corresponding number. Hint: Look at the argument `names_sep` in `separate_wider_delim().`"
  } 
  else{
    test.results[2, 4] <- paste0(paste0(c("The following column(s) should be in `variable_name`, but they were not found in your answer:",
                                   colnames(variable_name_test)[!(colnames(variable_name_test) %in% colnames(variable_name))]),
                                   collapse = "  "), ". Hint: Look at the function `separate_wider_delim().`")
  }
}

#Example 2 (From H14, Q4a): Dynamically checking if a column contains the correct values (there are 4 different values/rows): 
else if(!isTRUE(all.equal(variable_name$column_name |> sort(na.last = T), variable_name_test$column_name |> sort(na.last = T)))){
    
  check_names <- variable_name$column_name
    
  correct_names <- variable_name_test$column_name
    
  correct_check <- sapply(check_names, function(names) !names %in% correct_names)
    
  correct_check_names <- paste0(correct_names[correct_check], collapse = " ")
    
  test.results[2, 4] <- paste0("The following values are missing in `column_name`: ", correct_check_names, ". Hint: (a) Look at the function `case_when()`. (b) Make sure you converted the values correctly according to the prompt.")
}
    
#Example 3 (From Final, P1 Q1b): Dynamically checking if the columns of a tibble were correctly cleaned by removing the punctuation symbols `‡`, `–`, `†`. 
else if(any(sapply(c('‡', '–', '†'), function(value) {any(variable_name |> summarise_all(~ value %in% .))}))){
  
  value_check <- sapply(c('‡', '–', '†'), function(value) {any(variable_name |> summarise_all(~ value %in% .))})
    
  value_names <- paste0(c('‡', '–', '†')[value_check], collapse = " ")
    
  test.results[2, 4] <- paste0("The following special characters were found in `variable_name`: ", value_names, ". Please clean the data before proceeding.", collapse = "  ")
}
```

## Dynamic Checks

You may have noticed a pattern in our Dynamic Checks (i.e, checks that evaluate multiple test conditions simultaneously). 

Below is a template for constructing a two-condition Dynamic Check. 

Dynamic Checks for more than two test conditions follow similarly. The `sapply` function can be useful for evaluating *many* test conditions. 


``` r
#Dynamic Check General Example (for two test conditions)

#Checking if either of the test conditions are true...
else if(any(c(test_condition1, test_condition2)){
  
  #Creating a TRUE/FALSE vector of the test conditions...
  dynamic_check <- c(test_condition1, test_condition2) 
  
  #Creating a vector of the test condition names...
  dynamic_check_names <- c("test_condition1_name", "test_condition2_name")
    
  #Creating a vector of the test condition names that are TRUE...
  dynamic_check_output <- paste0(dynamic_check_names[dynamic_check], collapse = " ")
    
  #Outputting to test.results... 
  test.results[2, 4] <- paste0("The following condition(s) are true: ", dynamic_check_output, ". (Any additional feedback as needed.)")
}
```
