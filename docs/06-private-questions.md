# Private Questions {#Private-Questions}

Private Question Checks are designed to give students *necessary and basic feedback* -- not detailed hints. Their purpose is to flag basic issues without guiding the student toward the solution. Accordingly, Private Question Checks only incorporate [General Checks](#General-Checks).  

These Checks help students identify *trivial errors* (e.g., a misspelled column name) and *reduce time debugging*, allowing them to focus on the question's core aspects.

To achieve this, Private Questions are graded using three built-in functions: `private_grader()`, `ggplot_grader()`, and `private_tabyl_grader()`.[^3] The following sections explain how to use each function and its parameters. 

:::: {.infobox .note data-latex="{bell_note}"}

**Note**: 

Function sample code omit the [`test.results[#, ]`](#test.results) initialization for readability. 

Remember to *always* initialize `test.results[#, ]` before implementing a question's autograder code. 

::::

[^3]: For ECON 145 TAs, `private_grader()`, `ggplot_grader()`, and `private_tabyl_grader()` can be found in the subfolder `helper_functions/misc_helper_functions`.

## private_grader() {#private_grader}

### Description {-}

Grades Private Questions involving tibbles, vectors, lists, or plots, providing only basic feedback.

The function implements many of the [General Checks](#General-Checks) discussed earlier.

### Usage {-}


``` r
private_grader(var_name, 
               var, 
               var_test, 
               status = paste0("Question ", quest_num, " (Private)"), 
               var_status = "tib", 
               prev_check = TRUE, 
               is_tibble_check = TRUE, 
               quest_check_read = FALSE, 
               quest_num = 0, 
               quest_pt = 0,
               quest_prev = 1, 
               quest_prev_status = NULL)
```

### Arguments {-}

\renewcommand{\arraystretch}{2}


|Element           |Description                                                                                                                                       |Default                                      |
|:-----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------|
|var_name          |character, the expected name of var                                                                                                               |-                                            |
|var               |expected R object, the student's object                                                                                                           |-                                            |
|var_test          |other R object, the answer key's object to be compared with var                                                                                   |-                                            |
|status            |character, the question's displayed part, number, and type (Private/Public); Example: "Part 2 Question 2 (Private)"                               |paste0("Question ", quest_num, " (Private)") |
|var_status        |the data type of var; Example: "tib" (tibble), "vect" (vector), "lst" (list), "plt" (plot)                                                        |"tib"                                        |
|prev_check        |logical indicating if the Prerequisite Check should be triggered. Requires quest_prev value and can optionally be combined with quest_prev_status |TRUE                                         |
|is_tibble_check   |logical indicating if the tibble Structure Check should be triggered                                                                              |TRUE                                         |
|quest_check_read  |logical indicating if the message "Please make sure you are using read_csv(), not read.csv()." should be attached to the tibble Structure Check   |FALSE                                        |
|quest_num         |numeric, the question's number                                                                                                                    |0                                            |
|quest_pt          |numeric, the question's maximum score                                                                                                             |0                                            |
|quest_prev        |numeric, the prerequisite question's number                                                                                                       |1                                            |
|quest_prev_status |character, the prerequisite question's part and number; Example: "Part 2 Question 3b"                                                             |NULL                                         |

### Value {-}
 
The `test.results[#, ]` vector, containing the question's `status`, amount of points awarded, total point value, and feedback message.

See [What is test.results?](#test.results) for more details.

### Details {-}

:::: {.infobox .imp data-latex="{important_exclamation}"}

Designed for grading Private Questions involving **tibbles, vectors, lists, or plots only**. 

Plot grading via `private_grader()` is limited. Consistent with previous ECON 145 autograders, full credit is awarded if the student simply names the plot properly and gets any prerequisite question correct. For more detailed feedback, consider using the [`ggplot_grader()`](#ggplot_grader) for ggplot-type questions.  See [Plots](#private_grader-plots) for more information. 

The function grades **two-way tabyl lists only**. For [three-way tabyl](#three-way_tabyl) lists, please use [`private_tabyl_grader()`](#private_tabyl_grader) instead.

::::

Note that `var` is the *expected* student's R object and may not necessarily exist (e.g., the student saved their variable under a different name).

-   This is not a problem because `private_grader()` first performs the [Name Check](#Name-Check). If `var` does not exist, the subsequent Checks will not be triggered. 

Also note that `quest_prev_status` is an *optional* argument when setting `prev_check` and `quest_prev` but helps *clarify* an unclear prerequisite question's location in the feedback message.

-   For example, if the prerequisite question is Part 2 Question 3b, but is the eighth question of the assignment (`quest_prev = 8`), this argument can specify its particular location through setting `quest_prev_status = “Part 2 Question 3b”`. Otherwise, its question's number will be displayed (i.e., `"Question 8"`). 

### Example 1: Tibble {-}

Consider the following questions from Homework 14.

**Part 1: Coding Assignment**

1.  First, you will combine player salary data with CPI data.

    (a) (*Private Question*) Load in the `Master.csv` file, keeping the columns for player ID, birth year, birth country, first name, last name, weight, height, bats, throws, debut date, and final game date. Save the tibble as **`player_master_data`**.

    (b) (*Private Question*) Load in the `Salaries.csv` file into R. Join the `player_master_data` tibble with the data from `Salaries.csv` by `playerID`, keeping only rows with data from both datasets. Save the resulting, joined tibble as **`player_salary_data1`**.

Assume each question is worth 5 points.

Then, the autograder code for this question should look like:[^4]

[^4]: While manipulation of the default parameters can allow `private_grader()` to be implemented with fewer explicit inputs, it is recommended to explicitly define *at least* the parameters included in these examples for clarity and consistency.


``` r
#Comparing the student's tibble `player_master_data` with the answer key's `player_master_data_test` 
#Since this is the first question of the assignment, there is no prerequisite check and so `prev_check` is set to `FALSE`
#Moreover, since this question involves the loading in of a .csv file, it would be helpful to remind students to use `read_csv()` and not `read.csv()` by setting `quest_check_read` to `TRUE`
test.results[1, ] <- private_grader("player_master_data", player_master_data, player_master_data_test,
                                    status = "Part 1 Question 1a (Private)", var_status = "tib",
                                    quest_check_read = TRUE, prev_check = FALSE, quest_num = 1, quest_pt = 5)

#Comparing the student's tibble `player_salary_data1` with the answer key's `player_salary_data1_test` 
#This question relies on the student correctly reproducing `player_master_data` in the previous question, so `prev_check`, `quest_prev`, and `quest_prev_status` are set appropriately
#Since this question also involves the reading in of data, `quest_check_read` is set to TRUE
test.results[2, ] <- private_grader("player_salary_data1", player_salary_data1, player_salary_data1_test,
                                    status = "Part 1 Question 1b (Private)", var_status = "tib",
                                    prev_check = TRUE, quest_prev = 1, quest_prev_status = "Part 1 Question 1a",
                                    quest_check_read = TRUE, quest_num = 2, quest_pt = 5)
```

### Example 2: Vectors {-}

Consider the following questions from Starter Assignment 1.

**Part 1: Numeric Object Type and Logical Operators**

1.  (*Private Question*) Assign number `1.45` to the variable **`num_1`**. Remember that the assignment operator in R is `<-`.

2.  (*Private Question*) Assign number `5` to the variable **`num_2`**.

Assume each question is worth 1 point.

Then, the autograder code for this question should look like:


``` r
#Comparing the student's vector `num_1` with the answer key's `num_1_test` 
#Since this question does not rely on another question, `prev_check` is set to `FALSE`
test.results[1, ] <- private_grader("num_1", num_1, num_1_test,
                                    status = "Part 1 Question 1 (Private)", var_status = "vect",
                                    prev_check = FALSE, quest_num = 1, quest_pt = 1)
  
#Comparing the student's vector `num_2` with the answer key's `num_2_test` 
#`prev_check` is set similarly
test.results[2, ] <- private_grader("num_2", num_2, num_2_test,
                                    status = "Part 1 Question 2 (Private)", var_status = "vect",
                                    prev_check = FALSE, quest_num = 2, quest_pt = 1)
```

### Example 3: Lists  {-}

Consider the following questions from Homework 7.

**Part 1: Coding Assignment**

8.  (*Private Question*) Using `crimeData`, create a table called **`age_bin_by_crime_percentages`** that shows what percent of crime experienced by each age bin that is violent or nonviolent. Your table should have a column each for violent and nonviolent crimes and a row for each age bin as well as a row for the total across all age bins.

9.  (*Private Question*) Using `crimeData`, create a table called **`crime_by_age_bin_percentages`** that shows the percent of violent and nonviolent crimes that occurred in each age bin. Your table should have a column for each age bin and a row each for violent and nonviolent crimes. You should also have a row for the total for each age bin.

Assume each question is worth 10 points and that `crimeData` was created in Question 6. 

Then, the autograder code for these questions should look: 


``` r
#Note: Tables (especially those created with `janitor::tabyl()`) should be classified as lists ("lst") when using `private_grader()`

#Comparing the student's list `age_bin_by_crime_percentages` with the answer key's `age_bin_by_crime_percentages_test` 
#Since this question relies on `crimeData` being correct, `prev_check` and `quest_prev` are set appropriately (`quest_prev_status` is omitted)
test.results[8, ] <- private_grader("age_bin_by_crime_percentages", age_bin_by_crime_percentages, age_bin_by_crime_percentages_test, 
                                    status = "Part 1 Question 8 (Private)", var_status = "lst", 
                                    prev_check = TRUE, quest_num = 8, quest_pt = 10, quest_prev = 6)

#Comparing the student's list `crime_by_age_bin_percentages` with the answer key's `crime_by_age_bin_percentages_test` 
#`prev_check` and `quest_prev` are set appropriately (`quest_prev_status` is omitted)
test.results[9, ] <- private_grader("crime_by_age_bin_percentages", crime_by_age_bin_percentages, crime_by_age_bin_percentages_test, 
                                    status = "Part 1 Question 9 (Private)", var_status = "lst", 
                                    prev_check = TRUE, quest_num = 9, quest_pt = 10, quest_prev = 6)
```

### Example 4: Plots {#private_grader-plots -}

#### Background {-}

Previously in ECON 145, plots have not been *rigorously graded* by the autograder due to the desire to award credit to a wide range of students' plots that closely resemble, but are not identical to, the solution. 

-   Accordingly, `private_grader()` only performs the [Prerequisite Check](#Prerequisite-Check) and [Name Check](#Name-Check) when grading plots. If the student's plot bypasses these Checks, full credit is awarded. Afterwards, plots are *manually graded* by the TAs in the students' write-ups.

- `ggplot_grader()` was made in an attempt to provide some level of Checks and feedback for plots created through the `ggplot2` package. See [`ggplot_grader()`](#ggplot_grader) for details. 

Now consider the hypothetical homework question:

**Part 1: Coding Assignment**

2.  (*Private Question*) Using `basketball_data` from Question 1, create a scatter plot of points (`pt`) versus assists (`ast`). Save the plot as **`basketball_scatter`**.

Assume this question is worth 5 points.

Then, the autograder code for this question should look like:


``` r
#Comparing the student's plot `basketball_scatter` with the answer key's `basketball_scatter_test` 
#Since this question relies on `basketball_data` being correct, `prev_check` and `quest_prev` are set appropriately (`quest_prev_status` is omitted)
test.results[2, ] <- private_grader("basketball_scatter", basketball_scatter, basketball_scatter_test,
                                    status = "Part 1 Question 2 (Private)", var_status = "plt",
                                    prev_check = TRUE, quest_num = 2, quest_pt = 5, quest_prev = 1)
```

### Acknowledgements {- #acknowledgements}

**Author**: Riley Berman

**Contributors**: Alex Zhao, Jack Keefer, Shreya Sinha, Michal Snopek

## ggplot_grader() {#ggplot_grader}

### Description {-}

Grades Private (or Public) Questions involving ``ggplot`` objects from the `ggplot2` package [@R-ggplot2]. 

This function provides more detailed feedback than traditionally given by the autograder. Rather than evaluating for full plot correctness, `ggplot_grader()` checks for key plot attributes, like a proper title or x-y aesthetics, and returns targeted feedback. Accordingly, some flexibility is maintained between the student's plot and the solution. 

See [Plots](#private_grader-plots) for more information. 

### Usage {-}


``` r
ggplot_grader(var_name, 
              var, 
              var_test, 
              status = paste0("Question ", quest_num, " (Private)"),
              prev_check = FALSE, 
              title_check = FALSE, 
              axis_check = FALSE, 
              aesthetic_check = FALSE, 
              aesthetic_flexible_check = FALSE, 
              axis_name_check = FALSE, 
              data_check = FALSE, 
              facet_wrap_check = FALSE, 
              geom_check = FALSE, 
              geom_check_name = c("GeomPoint"), 
              color_fill_check = FALSE, 
              color_fill_check_name = c("colour"), 
              quest_num = 0, 
              quest_pt = 0, 
              quest_prev = 1, 
              quest_prev_status = NULL)
```

### Arguments {-}


|Element                  |Description                                                                                                                                                                                |Default                                      |
|:------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------|
|var_name                 |character, the expected name of var                                                                                                                                                        |-                                            |
|var                      |expected ggplot object, the student's ggplot                                                                                                                                               |-                                            |
|var_test                 |other ggplot object, the answer key's ggplot to be compared with var                                                                                                                       |-                                            |
|status                   |character, the question’s displayed part, number, and type (Private/Public)                                                                                                                |paste0("Question ", quest_num, " (Private)") |
|prev_check               |logical indicating if the Prerequisite Check should be triggered; requires quest_prev value and can optionally be combined with quest_prev_status                                          |FALSE                                        |
|title_check              |logical indicating if an existence check for a graph title should be triggered                                                                                                             |FALSE                                        |
|axis_check               |logical indicating if an existence check for the x-y axis labels should be triggered                                                                                                       |FALSE                                        |
|aesthetic_check          |logical indicating if a strict check for the correct x-y aesthetic mappings should be triggered; alternative to aesthetic_flexible_check; see aesthetic_check or aesthetic_flexible_check? |FALSE                                        |
|aesthetic_flexible_check |logical indicating if a flexible check for the correct x-y aesthetic mappings should be triggered; alternative to aesthetic_check; see aesthetic_check or aesthetic_flexible_check?        |FALSE                                        |
|axis_name_check          |logical indicating if a check for the correct x-y axis labels should be triggered                                                                                                          |FALSE                                        |
|data_check               |logical indicating if a check for the correct data associated with the x-y aesthetics should be triggered; requires aesthetic_check                                                        |FALSE                                        |
|facet_wrap_check         |logical indicating if a check for the existence of ggplot2::facet_wrap() should be triggered                                                                                               |FALSE                                        |
|geom_check               |logical indicating if an existence check for ggplot layer types (e.g., ggplot2::geom_point()) should be triggered; requires geom_check_name                                                |FALSE                                        |
|geom_check_name          |character vector of the various ggplot layer types to check for in geom_check; Example: c("GeomPoint", "GeomSmooth")                                                                       |c("GeomPoint")                               |
|color_fill_check         |logical indicating if an existence check for the "color" and/or "fill" arguments should be triggered; requires color_fill_check_name                                                       |FALSE                                        |
|color_fill_check_name    |character vector of the "color" and/or "fill" arguments to check for in color_fill_check; Example: c("colour", "fill")                                                                     |c("colour")                                  |
|quest_num                |numeric, the question's number                                                                                                                                                             |0                                            |
|quest_pt                 |numeric, the question's maximum score                                                                                                                                                      |0                                            |
|quest_prev               |numeric, the prerequisite question's number                                                                                                                                                |1                                            |
|quest_prev_status        |character, the prerequisite question's part and number                                                                                                                                     |NULL                                         |

### Value {-}

The `test.results[#, ]` vector, containing the question's `status`, amount of points awarded, total point value, and feedback message.

See [What is test.results?](#test.results) for more details.

### Details {-}

:::: {.infobox .imp data-latex="{important_exclamation}"}

Designed for grading Private (or Public) Questions involving **ggplot objects only** (i.e., objects with a ``ggplot`` class attribute). 

The function flexibly grades ggplot objects by allowing the user to specify which ggplot attributes to check for.

::::

Note that `var` is the *expected* student's ggplot object and may not necessarily exist or be a ggplot object (e.g., the student decided to use R's base `plot()` instead). 

-   This is not a problem because `ggplot_grader()` first tests for a `ggplot` class attribute and then performs the [Name Check](#Name-Check). If `var` does not exist or is not a ggplot object, the subsequent Checks will not be triggered.

Like in [`private_grader()`](#private_grader), `quest_prev_status` is an *optional* argument when using `prev_check` and `quest_prev` but helps *clarify* an unclear prerequisite question's location in the feedback message.

#### When to use aesthetic_check or aesthetic_flexible_check? {- #aesthetic_check_difference}

`aesthetic_check` tests if the x-y aesthetic mappings of `var` are *exactly the same* as in `var_test`, while `aesthetic_flexible_check` tests if the x-y aesthetic mappings of `var_test` are *contained* in `var`. 

Consider the following motivating example: 


``` r
#student's ggplot object
var <- data |> ggplot(aes(date, death * 100)) 

#answer key's ggplot object
var_test <- data |> mutate(death = death * 100) |> 
            ggplot(aes(date, death)) 
```

Under `aesthetic_check`, the student's `var` would *not be considered correct*, because its x-y aesthetic mappings (`x = date`, `y = death * 100`) are not the same as in `var_test` (`x = date`, `y = death`), despite the ggplots being *virtually identical.*

However, under `aesthetic_flexible_check`, `var` is now *considered correct*, since it contains `var_test`'s aesthetics. 

:::: {.infobox .imp data-latex="{important_exclamation}"}

`aesthetic_flexible_check` is more flexible than `aesthetic_check`, since it allows for a wider range of acceptable x-y aesthetics. However, because the student's aesthetics can then vary *slightly* from the answer key, the `data_check` **can't be performed without an error occurring.**[^5]

Consequently, `data_check` is *automatically* turned off when `aesthetic_flexible_check` is set to `TRUE`. 

::::

[^5]: We haven't found any smooth way to fix this, which might entail comparing only the final data used in the ggplot without reference to the x-y aesthetics. The problem we ran into is that to compare the student's and answer key's ggplot data, while allowing for flexible row and column ordering, we need to have *exactly* the same x-y aesthetics. Perhaps something like
[`ggplot_build`](https://ggplot2.tidyverse.org/reference/ggplot_build.html) can resolve this. 

**Recommendation**: If it is reasonable that a student could use *similar*, but *not identical*, x-y aesthetics to the solution (like above), use `aesthetic_flexible_check`, keeping in mind that `data_check` will be turned off. Otherwise, if the aesthetics can reasonably be expected to be the same as the solution and/or the prompt is explicit about which aesthetics to chose, use `aesthetic_check`. Implementing a `data_check` is available here. 

-   Generally, opting for `aesthetic_flexible_check` is the *safer and better choice* (at least until this issue is resolved).

:::: {.infobox .warning data-latex="{warning}"}

**Warning**: 

`aesthetic_flexible_check` and `aesthetic_check` are *mutually exclusive checks.*

**Do not use both arguments together -- that is, don't set both to `TRUE`**. 

::::

### Example 1 {-}

Consider the following question from Homework 10.

**Part 1: COVID-19 Data**

4. (*Public Question*) Next, create a bar plot, using `ggplot2`, that plots the percentage of people who died from COVID-19 based on their vaccination status and age group. The plot should have four bars in two groups. One group of bars should be for those unvaccinated and the second group of bars should be for those vaccinated. In each group, you should have two bars representing the percentage who died of COVID-19, one for those 50 years or older and one for those younger than 50. Save your bar plot as **`covid_plot2`**. 

The answer should look something like: 


``` r
covid_plot2 <- covid_data |> 
  summarize(death = sum(outcome == "death")/n(),
            survived = sum(outcome == "survived")/n(),
            .by = c(vaccine_status, age_group)) |>
    ggplot(aes(x = vaccine_status, y = death, fill = age_group)) +
    geom_bar(stat = "identity", position = "dodge") +
    ylab("percentage") 
```

Assume this question is worth 20 points and that we want to check if the ggplot has: 

-   a `geom_bar` layer, 
-   a `fill` argument,
-   and x-y axis labels.

Then, the autograder code for this question should look like:


``` r
#Comparing the student's ggplot `covid_plot2` with the answer key's `covid_plot2_test` 
#Note: the student can use `geom_col()` instead of `geom_bar()`, since it has the same ggplot attribute `GeomBar`
test.results[4, ] <- ggplot_grader("covid_plot2", covid_plot2, covid_plot2_test, 
                                   status = "Part 1 Question 4 (Public)", axis_check = TRUE, 
                                   geom_check = TRUE, geom_check_name = c("GeomBar"), 
                                   color_fill_check = TRUE, color_fill_check_name = c("fill"),
                                   quest_num = 4, quest_pt = 20)
```

### Example 2 {-}

Consider the following question from Homework 12.

**Part 1: Coding Assignment**

4. In the final coding question, you will recreate Figures 1 and 2, which represent the median home price in the data for each state and the median home price to county per capita income ratio for each state. 

    c) (*Private Question*) Using `priceIncomeData`, create a bar plot called **`priceByIncomePlot`** that shows the median home price to county per capita income ratio for each state and the US as a whole. The x-axis should be the state, and the y-axis should be the median home price to county per capita income ratio. The bars should be colored based on if the state median home price to county per capita income ratio is above or below the US as a whole. 

The answer should look something like:


``` r
priceByIncomePlot <- priceIncomeData |>
  ggplot(aes(x = reorder(state, price_by_income), y = price_by_income)) +
  geom_bar(stat = "identity", aes(fill = above_below_us_price_by_income)) +
  theme(axis.text.x = element_text(angle = 90, hjust = 1)) + #rotate x-axis values for visibility 
  labs(title = 'Median Home Price to Average Income Ratio', x = 'State', y = 'Median Price to Average Income Ratio')
```

Assume this question is worth 15 points and is the eighth question of the assignment.

We want to check if the ggplot has: 

-   a title,
-   the correct x-y axis labels,
-   a `geom_bar` layer,
-   a `fill` argument, 
-   and a correctly created `priceIncomeData` from a previous question (the sixth question).

Then, the autograder code for this question should look like:


``` r
#Comparing the student's ggplot `priceByIncomePlot` with the answer key's `priceByIncomePlot_test` 
#Note: `axis_name_check` can be used without `axis_check` to check if the x-y axis labels are correct (and therefore exist)
test.results[8, ] <- ggplot_grader("priceByIncomePlot", priceByIncomePlot, priceByIncomePlot_test, 
                                   status = "Question 4c (Private)", title_check = TRUE, 
                                   axis_check = TRUE, axis_name_check = TRUE, 
                                   geom_check = TRUE, geom_check_name = c("GeomBar"), 
                                   color_fill_check = TRUE, color_fill_check_name = c("fill"), 
                                   prev_check = TRUE, quest_prev = 6, quest_prev_status = "Question 4a",
                                   quest_num = 8, quest_pt = 15) 
```

### Example 3 {-}

Consider the following question from Homework 13.

**Part 1: Coding Assignment**

2. In this question, you will work with the `gdpData.csv` file to analyze national Olympic performance and GDP. 

    d) (*Public Question*) Next, using `olympic_gdp_data` and `ggplot`, create a group of scatter plots that plot the percentage of the GDP and percentage of the medals won by each country in the 2010 through 2016 Olympic games. The x-axis should contain the percentage of the GDP and the y-axis should contain the percentage of the medals won. The plots should be faceted by the year and season of the Olympic games, such that one plot is for the 2010 winter Olympics, another is for the 2012 summer Olympics, etc. Include a line with slope equal to 1 to denote the values for which the percent of GDP and percent of medals won is equal. You should also label the points on your graph with the NOC codes for the countries that have at least 5\% of the GDP or medals won during each Olympic games. Save your plot as **`olympic_gdp_perc_plot`**.

The answer should look something like:


``` r
olympic_gdp_perc_plot <- olympic_gdp_data |> 
    filter(year >= 2010) |> 
    filter(!is.na(gdp_per_capita), medalsPerc > 0.001) |> 
    ggplot(aes(x = gdpPerc, y = medalsPerc)) + 
    geom_point() +
    geom_text(aes(label = if_else(medalsPerc > 0.05 | gdpPerc > 0.05, noc, ''), 
                  hjust = 0, vjust = 0)) +
    geom_abline(intercept = 0, slope = 1, col = 'red') +
    facet_wrap(year~season)
```

Assume that this question is worth 5 points and is the ninth question of the assignment.

We want to check if the ggplot has: 

-   x-y axis labels,
-   the flexibly correct x-y aesthetics,
-   `geom_point`, `geom_abline`, and `geom_text` layers,
-   a `facet_wrap()` function,
-   and a correctly created `olympic_gdp_data` from a previous question (the eighth question).

Then, the autograder code for this question should look like:


``` r
#Comparing the student's ggplot `olympic_gdp_perc_plot` with the answer key's `olympic_gdp_perc_plot_test` 
#Note: `geom_abline()` can be checked through the `GeomAbline` attribute and `geom_text` through the `GeomText` attribute
test.results[9, ] <- ggplot_grader("olympic_gdp_perc_plot", olympic_gdp_perc_plot , 
                                   olympic_gdp_perc_plot_test, status = "Question 2d (Public)",
                                   axis_check = TRUE, aesthetic_flexible_check = TRUE, 
                                   geom_check = TRUE, geom_check_name = c("GeomPoint", "GeomAbline", "GeomText"),  
                                   quest_num = 9, quest_pt = 5, facet_wrap_check = TRUE,
                                   prev_check = TRUE, quest_prev = 8, quest_prev_status = "Question 2c (Public)")
```

### Example 4 {-}

Consider the hypothetical homework question:

**Part 1: Coding Assignment**

2.  (*Private Question*) Using `basketball_data` from Question 1, create a scatter plot of points versus assists with `ggplot`. The x-axis should be `pt` and the y-axis should be `ast.` Save the ggplot as **`basketball_scatter`**.

The answer should look something like:


``` r
basketball_scatter <- basketball_data |> 
  ggplot(aes(x = pt, y = ast)) +
  geom_point()
```

Assume this question is worth 5 points and that we want to check if the ggplot has: 

-   the correct x-y axis labels,
-   the correct x-y aesthetics, 
-   the correct x-y data, 
-   a `geom_point` layer, 
-   and a correctly created `basketball_data` from a previous question (the first question).

Then, the autograder code for this question should look like:


``` r
#Comparing the student's plot `basketball_scatter` with the answer key's `basketball_scatter_test` 
test.results[2, ] <- ggplot_grader("basketball_scatter", basketball_scatter, basketball_scatter_test,
                                    status = "Part 1 Question 2 (Private)", axis_name_check = TRUE, 
                                    aesthetic_check = TRUE, data_check = TRUE,
                                    geom_check = TRUE, geom_check_name = c("GeomPoint"), 
                                    quest_num = 2, quest_pt = 5, prev_check = TRUE, quest_prev = 1)
```

### Acknowledgements {-}

**Author**: Riley Berman

**Contributors**: Alex Zhao, Jack Keefer, Shreya Sinha, Michal Snopek

## private_tabyl_grader() {#private_tabyl_grader}

### Description {-}

Grades Private Questions involving [three-way `tabyl`](#three-way_tabyl) objects from the `janitor` package [@R-janitor].

This function implements many of the [General Checks](#General-Checks) discussed earlier.

### Usage {-}


``` r
private_tabyl_grader(var_name, 
                     var, 
                     var_test, 
                     status = paste0("Question ", quest_num, " (Private)"), 
                     prev_check = TRUE, 
                     quest_num = 0, 
                     quest_pt = 0, 
                     quest_prev = 1, 
                     quest_prev_status = NULL)
```

### Arguments {-}


|Element           |Description                                                                                                                                       |Default                                      |
|:-----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------|
|var_name          |character, the expected name of var                                                                                                               |-                                            |
|var               |expected tabyl object, the student's tabyl                                                                                                        |-                                            |
|var_test          |other tabyl object, the answer key's tabyl to be compared with var                                                                                |-                                            |
|status            |character, the question's displayed part, number, and type (Private/Public)                                                                       |paste0("Question ", quest_num, " (Private)") |
|prev_check        |logical indicating if the Prerequisite Check should be triggered; requires quest_prev value and can optionally be combined with quest_prev_status |TRUE                                         |
|quest_num         |numeric, the question's number                                                                                                                    |0                                            |
|quest_pt          |numeric, the question's maximum score                                                                                                             |0                                            |
|quest_prev        |numeric, the prerequisite question's number                                                                                                       |1                                            |
|quest_prev_status |character, the prerequisite question's part and number                                                                                            |NULL                                         |

### Value {-}

The `test.results[#, ]` vector, containing the question's `status`, amount of points awarded, total point value, and feedback message.

See [What is test.results?](#test.results) for more details.

### Details {-}

Designed for grading **three-way tabyl objects only** (i.e., objects with a `tabyl` class attribute). 

Use [`private_grader()`](#private_grader) for two-way tabyl objects.

As with the other functions, `var` may not necessarily exist or be a tabyl object.

-   This is not a problem because `private_tabyl_grader()` first tests for a `tabyl` class attribute and then performs the [Name Check](#Name-Check).

Likewise, `quest_prev_status` is an optional argument when using   `prev_check` and `quest_prev` but helps *clarify* an unclear prerequisite question's location in the feedback message. 

#### What is a three-way tabyl? {- #three-way_tabyl}

A three-way tabyl is a multi-table created from three variables that is produced by the `janitor::tabyl()` function.

A three-way tabyl will typically create two "sub-tables."

Consider the following example from the `tabyl` documentation [@janitortabyls]:




``` r
t3 <- starwars |>
  filter(species == "Human") |>
  tabyl(eye_color, skin_color, gender)

#The result is a tabyl of eye color x skin color, split into a list by gender
```


```
## $feminine
##  eye_color dark fair light none pale tan white
##       blue    0    2     1    0    0   0     0
##  blue-gray    0    0     0    0    0   0     0
##      brown    0    1     3    0    0   0     0
##       dark    0    0     0    0    0   0     0
##      hazel    0    0     1    0    0   0     0
##    unknown    0    0     0    1    0   0     0
##     yellow    0    0     0    0    0   0     0
## 
## $masculine
##  eye_color dark fair light none pale tan white
##       blue    0    7     2    0    0   0     0
##  blue-gray    0    1     0    0    0   0     0
##      brown    3    4     3    0    0   2     0
##       dark    1    0     0    0    0   0     0
##      hazel    0    1     0    0    0   0     0
##    unknown    0    0     0    0    0   0     0
##     yellow    0    0     0    0    1   0     1
```

### Example {-}

Consider the following modified question from the Final.

**Part 3: Creating Tables and Figures**

1) (*Private Question*) Using `schoolData`, create a table that shows the percentage of schools that provide free lunch to all students, and how for each year in each `locale` category (City, Suburb, Town, and Rural). Make sure you remove any rows with missing `national_school_lunch_program` or `locale` values before creating your table. Save this table as **`nslp_locale_table`**. 

The answer should look something like:


``` r
#Note: This has been slightly modified from the actual solution
nslp_locale_table <- schoolData |> 
  filter(!is.na(locale) & !is.na(national_school_lunch_program)) |> 
  janitor::tabyl(year, national_school_lunch_program, locale) |> 
  janitor::adorn_percentages() |> 
  janitor::adorn_pct_formatting()
```

Assume this question is worth 5 points, is the sixteenth question of the assignment, and that `schoolData` was created in Part 1 Question 1b (the second question). 

Then, the autograder code for this question should look like:


``` r
#Comparing the student's three-way tabyl `nslp_locale_table` with the answer key's `nslp_locale_table_test` 
test.results[16, ] <- private_tabyl_grader("nslp_locale_table", nslp_locale_table, nslp_locale_table_test, 
                                           status = "Part 3 Question 1 (Private)", prev_check = TRUE, 
                                           quest_prev = 2, quest_prev_status = "Part 1 Question 1b", 
                                           quest_num = 16, quest_pt = 5)
```

### Acknowledgements {-}

**Co-authors**: Michal Snopek, Riley Berman

**Contributors**: Alex Zhao, Jack Keefer, Shreya Sinha
