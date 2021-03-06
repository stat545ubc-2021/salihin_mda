Mini Data-Analysis Deliverable 3
================

# Welcome to your last milestone in your mini data analysis project!

In Milestone 1, you explored your data and came up with research
questions. In Milestone 2, you obtained some results by making summary
tables and graphs.

In this (3rd) milestone, you’ll be sharpening some of the results you
obtained from your previous milestone by:

-   Manipulating special data types in R: factors and/or dates and
    times.
-   Fitting a model object to your data, and extract a result.
-   Reading and writing data as separate files.

**NOTE**: The main purpose of the mini data analysis is to integrate
what you learn in class in an analysis. Although each milestone provides
a framework for you to conduct your analysis, it’s possible that you
might find the instructions too rigid for your data set. If this is the
case, you may deviate from the instructions – just make sure you’re
demonstrating a wide range of tools and techniques taught in this class.

## Instructions

**To complete this milestone**, edit [this very `.Rmd`
file](https://raw.githubusercontent.com/UBC-STAT/stat545.stat.ubc.ca/master/content/mini-project/mini-project-3.Rmd)
directly. Fill in the sections that are tagged with
`<!--- start your work here--->`.

**To submit this milestone**, make sure to knit this `.Rmd` file to an
`.md` file by changing the YAML output settings from
`output: html_document` to `output: github_document`. Commit and push
all of your work to your mini-analysis GitHub repository, and tag a
release on GitHub. Then, submit a link to your tagged release on canvas.

**Points**: This milestone is worth 40 points (compared to the usual 30
points): 30 for your analysis, and 10 for your entire mini-analysis
GitHub repository. Details follow.

**Research Questions**: In Milestone 2, you chose two research questions
to focus on. Wherever realistic, your work in this milestone should
relate to these research questions whenever we ask for justification
behind your work. In the case that some tasks in this milestone don’t
align well with one of your research questions, feel free to discuss
your results in the context of a different research question.

# Setup

Begin by loading your data and the tidyverse package below:

``` r
library(tidyverse)
library(readr)
library(gtsummary)
library(vdemdata)
```

From Milestone 2, you chose two research questions. What were they? Put
them here.

<!-------------------------- Start your work below ---------------------------->

1.  Does level of democracy lead to higher levels of economic
    development?
2.  What explains why some countries have high levels of economic
    development despite having low levels of democracy?
    <!----------------------------------------------------------------------------->

# Exercise 1: Special Data Types (10)

For this exercise, you’ll be choosing two of the three tasks below –
both tasks that you choose are worth 5 points each.

But first, tasks 1 and 2 below ask you to modify a plot you made in a
previous milestone. **The plot you choose should involve plotting across
at least three groups (whether by facetting, or using an aesthetic like
colour)**. Place this plot below (you’re allowed to modify the plot if
you’d like). If you don’t have such a plot, you’ll need to make one.
Place the code for your plot below.

<!-------------------------- Start your work below ---------------------------->

``` r
# Subsetting the vdem dataset to 2015 data
vdem_2015 <- vdem %>%
  filter(year == 2015) %>%
  filter(!is.na(e_boix_regime))

# Creating the histogram of GDP per capita (e_migdppc) across different regions (e_regionpol_6C)
vdem_2015 %>% 
  mutate(e_regionpol_6C = recode(e_regionpol_6C, '1' = "E. Europe & C. Asia", '2' = "L. Am & Carib", '3' = "M.E. & N. Africa", '4' = "S.S. Africa", '5' = "W. Eu & N. Am", '6' = "Asia & Pacific")) %>%
  ggplot(aes(e_migdppc)) +
  geom_histogram() +
  theme_minimal() +
  labs(title = "Distribution of GDP per capita (2015) across regions", x = "GDP per capita", y = "Count") +
  scale_x_continuous(labels = scales::dollar_format()) +
  facet_wrap(vars(e_regionpol_6C))
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 12 rows containing non-finite values (stat_bin).

![](mda_3_salihin_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

<!----------------------------------------------------------------------------->

Now, choose two of the following tasks.

1.  Produce a new plot that reorders a factor in your original plot,
    using the `forcats` package (3 points). Then, in a sentence or two,
    briefly explain why you chose this ordering (1 point here for
    demonstrating understanding of the reordering, and 1 point for
    demonstrating some justification for the reordering, which could be
    subtle or speculative.)

2.  Produce a new plot that groups some factor levels together into an
    “other” category (or something similar), using the `forcats` package
    (3 points). Then, in a sentence or two, briefly explain why you
    chose this grouping (1 point here for demonstrating understanding of
    the grouping, and 1 point for demonstrating some justification for
    the grouping, which could be subtle or speculative.)

3.  If your data has some sort of time-based column like a date (but
    something more granular than just a year):

    1.  Make a new column that uses a function from the `lubridate` or
        `tsibble` package to modify your original time-based column. (3
        points)
        -   Note that you might first have to *make* a time-based column
            using a function like `ymd()`, but this doesn’t count.
        -   Examples of something you might do here: extract the day of
            the year from a date, or extract the weekday, or let 24
            hours elapse on your dates.
    2.  Then, in a sentence or two, explain how your new column might be
        useful in exploring a research question. (1 point for
        demonstrating understanding of the function you used, and 1
        point for your justification, which could be subtle or
        speculative).
        -   For example, you could say something like “Investigating the
            day of the week might be insightful because penguins don’t
            work on weekends, and so may respond differently”.

<!-------------------------- Start your work below ---------------------------->

**Task Number**: 1. Produce a new plot that reorders a factor in your
original plot

``` r
# Creating the histogram of GDP per capita (e_migdppc) across different regions (e_regionpol_6C), but factorising region based on increasing median of GDP per capita
vdem_2015 %>% 
  mutate(e_regionpol_6C = recode(e_regionpol_6C, '1' = "E. Europe & C. Asia", '2' = "L. Am & Carib", '3' = "M.E. & N. Africa", '4' = "S.S. Africa", '5' = "W. Eu & N. Am", '6' = "Asia & Pacific")) %>%
  mutate(e_regionpol_6C = fct_reorder(e_regionpol_6C, e_boix_regime, mean)) %>%
  ggplot(aes(e_migdppc)) +
  geom_histogram() +
  theme_minimal() +
  labs(title = "Distribution of GDP per capita (2015) across regions", x = "GDP per capita", y = "Count") +
  scale_x_continuous(labels = scales::dollar_format()) +
  facet_wrap(vars(e_regionpol_6C))
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 12 rows containing non-finite values (stat_bin).

![](mda_3_salihin_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

I chose to reorder the region variable based on the mean of the regime
type variable (e\_boix\_regime). The idea here is that the mean of the
regime type will indicate the proportion of countries which are
democratic. I want to order the regions by increasing proportion of
democratic countries because it will give me a good sense of where to
find the countries which are “deviant” to my hypothesis (democracies are
more likely to be economically-developed) based on their region.

<!----------------------------------------------------------------------------->
<!-------------------------- Start your work below ---------------------------->

**Task Number**: 2. Produce a new plot that groups some factor levels
together into an “other” category

``` r
# Creating the histogram of GDP per capita (e_migdppc) across different regions (e_regionpol_6C), but re-categorising countries which are not 1) Western Europe and North America, 2) Asia & Pacific, and 3) Latin America and the Caribbean as "other".
vdem_2015 %>% 
  mutate(e_regionpol_6C = recode(e_regionpol_6C, 
                                 '1' = "Other", 
                                 '2' = "Other", 
                                 '3' = "M.E. & N. Africa", 
                                 '4' = "S.S. Africa", 
                                 '5' = "W. Eu & N. Am", 
                                 '6' = "Asia & Pacific")) %>%
  mutate(e_regionpol_6C = fct_reorder(e_regionpol_6C, e_boix_regime, mean)) %>%
  ggplot(aes(e_migdppc)) +
  geom_histogram() +
  theme_minimal() +
  labs(title = "Distribution of GDP per capita (2015) across regions", x = "GDP per capita", y = "Count") +
  scale_x_continuous(labels = scales::dollar_format()) +
  facet_wrap(vars(e_regionpol_6C))
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 12 rows containing non-finite values (stat_bin).

![](mda_3_salihin_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

I chose to group Latin America / Caribbean and Eastern Europe / Central
Asia into the “Other” category because the distribution of GDP per
capita in those regions are very similar. I suspect that the
relationship between GDP per capita and regime type will be similar in
these two regions as well, which is why I placed them together in the
“Other” category.

<!----------------------------------------------------------------------------->

# Exercise 2: Modelling

## 2.0 (no points)

Pick a research question, and pick a variable of interest (we’ll call it
“Y”) that’s relevant to the research question. Indicate these.

<!-------------------------- Start your work below ---------------------------->

**Research Question**: Does level of democracy lead to higher levels of
economic development?

**Variable of interest**: Economic development (e\_migdppc)

<!----------------------------------------------------------------------------->

## 2.1 (5 points)

Fit a model or run a hypothesis test that provides insight on this
variable with respect to the research question. Store the model object
as a variable, and print its output to screen. We’ll omit having to
justify your choice, because we don’t expect you to know about model
specifics in STAT 545.

-   **Note**: It’s OK if you don’t know how these models/tests work.
    Here are some examples of things you can do here, but the sky’s the
    limit.
    -   You could fit a model that makes predictions on Y using another
        variable, by using the `lm()` function.
    -   You could test whether the mean of Y equals 0 using `t.test()`,
        or maybe the mean across two groups are different using
        `t.test()`, or maybe the mean across multiple groups are
        different using `anova()` (you may have to pivot your data for
        the latter two).
    -   You could use `lm()` to test for significance of regression.

<!-------------------------- Start your work below ---------------------------->

``` r
# Fitting a model of GDP per capita (e_migdppc) on regime score (v2x_polyarchy)
reg_GDP_regime <- lm(e_migdppc ~ v2x_polyarchy, vdem_2015)

print(reg_GDP_regime)
```

    ## 
    ## Call:
    ## lm(formula = e_migdppc ~ v2x_polyarchy, data = vdem_2015)
    ## 
    ## Coefficients:
    ##   (Intercept)  v2x_polyarchy  
    ##          8501          17914

<!----------------------------------------------------------------------------->

## 2.2 (5 points)

Produce something relevant from your fitted model: either predictions on
Y, or a single value like a regression coefficient or a p-value.

-   Be sure to indicate in writing what you chose to produce.
-   Your code should either output a tibble (in which case you should
    indicate the column that contains the thing you’re looking for), or
    the thing you’re looking for itself.
-   Obtain your results using the `broom` package if possible. If your
    model is not compatible with the broom function you’re needing, then
    you can obtain your results by some other means, but first indicate
    which broom function is not compatible.

<!-------------------------- Start your work below ---------------------------->

``` r
# Printing the p-value of the regression coefficient (v2x_polyarchy)
reg_GDP_regime_values <- broom::tidy(reg_GDP_regime)

names(reg_GDP_regime_values$p.value) <- c("Intercept", "Regime Score")

print(reg_GDP_regime_values$p.value)
```

    ##    Intercept Regime Score 
    ##  0.019866260  0.003398546

I chose to produce the p-value of the regression coefficient which is
regime score (v2x\_polyarchy). The p-value here tells us that the
probability of observing the regression coefficient of 17914 if the null
hypothesis is true is 0.3%. With such a low p-value, the evidence
strongly suggests that we can confidently reject the null hypothesis
that the regime score has no effect on the GDP per capita.

<!----------------------------------------------------------------------------->

# Exercise 3: Reading and writing data

Get set up for this exercise by making a folder called `output` in the
top level of your project folder / repository. You’ll be saving things
there.

## 3.1 (5 points)

Take a summary table that you made from Milestone 2 (Exercise 1.2), and
write it as a csv file in your `output` folder. Use the `here::here()`
function.

-   **Robustness criteria**: You should be able to move your Mini
    Project repository / project folder to some other location on your
    computer, or move this very Rmd file to another location within your
    project repository / folder, and your code should still work.
-   **Reproducibility criteria**: You should be able to delete the csv
    file, and remake it simply by knitting this Rmd file.

<!-------------------------- Start your work below ---------------------------->

``` r
# Creating the output folder
dir.create(here::here("output"))
```

    ## Warning in dir.create(here::here("output")): '/Users/mohamedsalihinsubhan/
    ## Desktop/PhD Stuff/5_21-22 Winter/STAT 545A/salihin_mda/output' already exists

``` r
# Creating the summary table from Milestone 2: Calculating, range, mean, and sd of GDP per capita in 2015 for democracies
summ_tab_mda_2 <-summary(vdem_2015$e_migdppc[vdem_2015$e_boix_regime == 1])

# Using the data.frame command to convert the summary table into a data frame so it can be saved as a csv file
summ_tab_mda_2 <- data.frame(unclass(summ_tab_mda_2), check.names = FALSE, stringsAsFactors = FALSE)

# Write the summary table as a csv file and saving it to the output folder
write_csv(summ_tab_mda_2, here::here("output", "summ_tab_mda_2.csv"))
```

<!----------------------------------------------------------------------------->

## 3.2 (5 points)

Write your model object from Exercise 2 to an R binary file (an RDS),
and load it again. Be sure to save the binary file in your `output`
folder. Use the functions `saveRDS()` and `readRDS()`.

-   The same robustness and reproducibility criteria as in 3.1 apply
    here.

<!-------------------------- Start your work below ---------------------------->

``` r
# Save model object as RDS
saveRDS(reg_GDP_regime, here::here("output", "model_object_mda_3.csv"))

# Read model object
reg_GDP_regime_2 <- readRDS(here::here("output", "model_object_mda_3.csv"))
```

<!----------------------------------------------------------------------------->

# Tidy Repository

Now that this is your last milestone, your entire project repository
should be organized. Here are the criteria we’re looking for.

## Main README (3 points)

There should be a file named `README.md` at the top level of your
repository. Its contents should automatically appear when you visit the
repository on GitHub.

Minimum contents of the README file:

-   In a sentence or two, explains what this repository is, so that
    future-you or someone else stumbling on your repository can be
    oriented to the repository.
-   In a sentence or two (or more??), briefly explains how to engage
    with the repository. You can assume the person reading knows the
    material from STAT 545A. Basically, if a visitor to your repository
    wants to explore your project, what should they know?

Once you get in the habit of making README files, and seeing more README
files in other projects, you’ll wonder how you ever got by without them!
They are tremendously helpful.

## File and Folder structure (3 points)

You should have at least four folders in the top level of your
repository: one for each milestone, and one output folder. If there are
any other folders, these are explained in the main README.

Each milestone document is contained in its respective folder, and
nowhere else.

Every level-1 folder (that is, the ones stored in the top level, like
“Milestone1” and “output”) has a `README` file, explaining in a sentence
or two what is in the folder, in plain language (it’s enough to say
something like “This folder contains the source for Milestone 1”).

## Output (2 points)

All output is recent and relevant:

-   All Rmd files have been `knit`ted to their output, and all data
    files saved from Exercise 3 above appear in the `output` folder.
-   All of these output files are up-to-date – that is, they haven’t
    fallen behind after the source (Rmd) files have been updated.
-   There should be no relic output files. For example, if you were
    knitting an Rmd to html, but then changed the output to be only a
    markdown file, then the html file is a relic and should be deleted.

Our recommendation: delete all output files, and re-knit each
milestone’s Rmd file, so that everything is up to date and relevant.

PS: there’s a way where you can run all project code using a single
command, instead of clicking “knit” three times. More on this in STAT
545B!

## Error-free code (1 point)

This Milestone 3 document knits error-free. (We’ve already graded this
aspect for Milestone 1 and 2)

## Tagged release (1 point)

You’ve tagged a release for Milestone 3. (We’ve already graded this
aspect for Milestone 1 and 2)
