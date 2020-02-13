## Introduction

An important aspect of “writing data for computers” is to make your data
**tidy**. Key features of **tidy** data:
  
- Each column is a variable
- Each row is an observation

A few things are important to note here: 

First, the optimal level of **tidy**ness depends on the exact use case. You want to find the optimal level of **tidyness** for your particular use case. **Tidy** data is also often referred to as "long" data since it makes the data table longer that it might typically be.

Second, this definition of tidy data essentially presumes that all the information in your data can be encoded in plain ("flat"") text files (e.g., in tab or comma-delimited formats). Microsoft Excel, Google Sheets, Numbers, etc. are truly awesome and useful tools. While I would encourage you to use these for data entry, you should not encode any additional information in the cells themselves. For examples, it is best not: i) to use colour highlighting to identify variables; ii) use formulas to impute data cells (we'll do this in R, instead); or iii) use creative spacing to separate sections. This information will be lost as you try and tidy your data.

Third, you should always leave your data pretty much as you found it in your spreadsheets (there are always exceptions) and do any modifications and reformatting in R using the tools we will learn below. And related to this, it is best to write scripts that modify the original data rather than change things in the console or overwrite the original file. That way, it is i) transparent what has been done to the data (you can also keep it under version control) and it is easy to modify the script to reshape it in a different way.

While tidy data is a good ideal to strive towards, **untidy** data abounds. In fact, we often inflict it
on ourselves, because untidy formats are more attractive for data entry
or examination. So how do you make **untidy** data **tidy**?

Note: the following is adapted from [a lesson developed by Jenny Bryan](https://github.com/jennybc/lotr-tidy). 
  
## Import untidy Lord of the Rings data
  
We now import the untidy data that was presented in the three film-specific word count tables that is described [here](https://github.com/jennybc/lotr-tidy/blob/master/01-intro.md).

I assume that data can be found as three plain text, delimited files, one for each film. 

Get them into your current project in a `data` subdirectory with your favorite method:
  
  - [The\_Fellowship\_Of\_The\_Ring.csv](https://github.com/jennybc/lotr-tidy/tree/master/data/The_Fellowship_Of_The_Ring.csv)
- [The\_Two\_Towers.csv](https://github.com/jennybc/lotr-tidy/tree/master/data/The_Two_Towers.csv)
- [The\_Return\_Of\_The\_King.csv](https://github.com/jennybc/lotr-tidy/tree/master/data/The_Return_Of_The_King.csv)

We bring the data into data frames or tibbles (the preferred data format of the **tidyverse**) using the function `read_csv`, one per film, and do some inspection.

``` r
library(tidyverse)
#> ── Attaching packages ──────────────────────────────────────── tidyverse 1.2.1 ──
#> ✔ ggplot2 3.0.0     ✔ purrr   0.2.5
#> ✔ tibble  1.4.2     ✔ dplyr   0.7.6
#> ✔ tidyr   0.8.1     ✔ stringr 1.3.1
#> ✔ readr   1.1.1     ✔ forcats 0.3.0
#> ── Conflicts ─────────────────────────────────────────── tidyverse_conflicts() ──
#> ✖ dplyr::filter() masks stats::filter()
#> ✖ dplyr::lag()    masks stats::lag()
fship <- read_csv(file.path("data", "The_Fellowship_Of_The_Ring.csv"))
#> Parsed with column specification:
#> cols(
#>   Film = col_character(),
#>   Race = col_character(),
#>   Female = col_integer(),
#>   Male = col_integer()
#> )
ttow <- read_csv(file.path("data", "The_Two_Towers.csv"))
#> Parsed with column specification:
#> cols(
#>   Film = col_character(),
#>   Race = col_character(),
#>   Female = col_integer(),
#>   Male = col_integer()
#> )
rking <- read_csv(file.path("data", "The_Return_Of_The_King.csv")) 
#> Parsed with column specification:
#> cols(
#>   Film = col_character(),
#>   Race = col_character(),
#>   Female = col_integer(),
#>   Male = col_integer()
#> )
rking
#> # A tibble: 3 x 4
#>   Film                   Race   Female  Male
#>   <chr>                  <chr>   <int> <int>
#> 1 The Return Of The King Elf       183   510
#> 2 The Return Of The King Hobbit      2  2673
#> 3 The Return Of The King Man       268  2459
```

## Collect untidy Lord of the Rings data into a single data frame

We now have one data frame per film, each with a common set of 4
variables. Step one in tidying this data is to glue them together into
one data frame, stacking them up row wise. This is called row binding
and we use `dplyr::bind_rows()`.

``` r
lotr_untidy <- bind_rows(fship, ttow, rking)
str(lotr_untidy)
#> Classes 'tbl_df', 'tbl' and 'data.frame':    9 obs. of  4 variables:
#>  $ Film  : chr  "The Fellowship Of The Ring" "The Fellowship Of The Ring" "The Fellowship Of The Ring" "The Two Towers" ...
#>  $ Race  : chr  "Elf" "Hobbit" "Man" "Elf" ...
#>  $ Female: int  1229 14 0 331 0 401 183 2 268
#>  $ Male  : int  971 3644 1995 513 2463 3589 510 2673 2459
lotr_untidy
#> # A tibble: 9 x 4
#>   Film                       Race   Female  Male
#>   <chr>                      <chr>   <int> <int>
#> 1 The Fellowship Of The Ring Elf      1229   971
#> 2 The Fellowship Of The Ring Hobbit     14  3644
#> 3 The Fellowship Of The Ring Man         0  1995
#> 4 The Two Towers             Elf       331   513
#> 5 The Two Towers             Hobbit      0  2463
#> 6 The Two Towers             Man       401  3589
#> 7 The Return Of The King     Elf       183   510
#> 8 The Return Of The King     Hobbit      2  2673
#> 9 The Return Of The King     Man       268  2459
```

Assembling one large data object from lots of little ones is common data
preparation task. When the pieces are as similar as they here, it’s nice
to assemble them into one object right away. In other scenarios, you may
need to do some remedial work on the pieces before they can be fitted
together nicely.

A good guiding principle is to glue the pieces together as early as
possible, because it’s easier and more efficient to tidy a single object
than 20 or 1000.

## Exercise 1

Write the joined untidy data to a file in the `data` folder using the function `write_csv()`. Add this and the original files to your repository. Commit and push.

## Tidy the untidy Lord of the Rings data

We are still violating one of the fundamental principles of **tidy
data**. “Word count” is a fundamental variable in our dataset and it’s
currently spread out over two variables, `Female` and `Male`.
Conceptually, we need to gather up the word counts into a single
variable and create a new variable, `Gender`, to track whether each
count refers to females or males. We use the `pivot_longer()` function from
the tidyr package to do this.

Note: There has been a recent (2019) change to the syntax of **tidyr**. `pivot_longer()` has replaced the `gather()` function


``` r
lotr_tidy <-
  gather(lotr_untidy, key = 'Gender', value = 'Words', Female, Male)
lotr_tidy
#> # A tibble: 18 x 4
#>    Film                       Race   Gender Words
#>    <chr>                      <chr>  <chr>  <int>
#>  1 The Fellowship Of The Ring Elf    Female  1229
#>  2 The Fellowship Of The Ring Hobbit Female    14
#>  3 The Fellowship Of The Ring Man    Female     0
#>  4 The Two Towers             Elf    Female   331
#>  5 The Two Towers             Hobbit Female     0
#>  6 The Two Towers             Man    Female   401
#>  7 The Return Of The King     Elf    Female   183
#>  8 The Return Of The King     Hobbit Female     2
#>  9 The Return Of The King     Man    Female   268
#> 10 The Fellowship Of The Ring Elf    Male     971
#> 11 The Fellowship Of The Ring Hobbit Male    3644
#> 12 The Fellowship Of The Ring Man    Male    1995
#> 13 The Two Towers             Elf    Male     513
#> 14 The Two Towers             Hobbit Male    2463
#> 15 The Two Towers             Man    Male    3589
#> 16 The Return Of The King     Elf    Male     510
#> 17 The Return Of The King     Hobbit Male    2673
#> 18 The Return Of The King     Man    Male    2459
```
or now...
```r
lotr_tidy <-
  pivot_longer(lotr_untidy, cols=c(Female, Male), names_to = 'Gender', values_to = 'Words')
lotr_tidy <- arrange(lotr_tidy, Gender)
# A tibble: 18 x 4
#>   Film                       Race   Gender Words
#>   <chr>                      <chr>  <chr>  <dbl>
#> 1 The Fellowship Of The Ring Elf    Female  1229
#> 2 The Fellowship Of The Ring Hobbit Female    14
#> 3 The Fellowship Of The Ring Man    Female     0
#> 4 The Two Towers             Elf    Female   331
#> 5 The Two Towers             Hobbit Female     0
#> 6 The Two Towers             Man    Female   401
#> 7 The Return Of The King     Elf    Female   183
#> 8 The Return Of The King     Hobbit Female     2
#> 9 The Return Of The King     Man    Female   268
#> 10 The Fellowship Of The Ring Elf    Male     971
#> 11 The Fellowship Of The Ring Hobbit Male    3644
#> 12 The Fellowship Of The Ring Man    Male    1995
#> 13 The Two Towers             Elf    Male     513
#> 14 The Two Towers             Hobbit Male    2463
#> 15 The Two Towers             Man    Male    3589
#> 16 The Return Of The King     Elf    Male     510
#> 17 The Return Of The King     Hobbit Male    2673
#> 18 The Return Of The King     Man    Male    2459
```

Tidy data … mission accomplished\!
  
  To explain our call to `gather()` above, let’s read it from right to
left: we took the variables `Female` and `Male` and gathered their
*values* into a single new variable `Words`. This forced the creation of
a companion variable `Gender`, a *key*, which tells whether a specific
value of `Words` came from `Female` or `Male`. All other variables, such
as `Film`, remain unchanged and are simply replicated as needed. The
documentation for `gather()` gives more examples and documents
additional arguments.

Or, using the new language of `pivot_longer()`, we use *values_to* instead of *values* and instead of a *key* we specify *names_to*. We now also need to explicity write *cols_to* to designate the columns 'Male' and 'Female'.

## Write the tidy data to a delimited file

Now we write this multi-film, tidy dataset to file for use in various
downstream scripts for further analysis and visualization. This would
make an excellent file to share on the web with others, providing a
tool-agnostic, ready-to-analyze entry point for anyone wishing to play
with this data.

``` r
write_csv(lotr_tidy, path = file.path("data", "lotr_tidy.csv"))
```

## Exercise 2

The word count data is given in these two **untidy** and gender-specific
files:
  
- [Female.csv](https://github.com/jennybc/lotr-tidy/tree/master/data/Female.csv)
- [Male.csv](https://github.com/jennybc/lotr-tidy/tree/master/data/Male.csv)

Write an R script that reads them in and writes a single tidy data frame
to file. Literally, reproduce the `lotr_tidy` data frame and the
`lotr_tidy.csv` data file from above.

Write R code to compute the total number of words spoken by each race across the entire trilogy. Do it two ways:
  
- Using film-specific or gender-specific, untidy data frames as the input data.
- Using the `lotr_tidy` data frame as input.

**Hint**: use the code presented [here](https://github.com/jennybc/lotr-tidy/blob/master/01-intro.md) and on the [RStudio data cheat sheet](https://rstudio.com/wp-content/uploads/2015/02/data-wrangling-cheatsheet.pdf). We haven't covered this yet but will do so next class. 

Reflect on the process of writing this code and on the code itself. Which is easier to write? Easier to read?
  
Add, commit, and push the tidy versions of your data to GitHub.
  
## Summary of this section 
  
  It is untidy to have have data parcelled out across different files or
data frames. We used `dplyr::bind_rows()` above to combine film-specific
data frames into one large data frame.

It is untidy to have a conceptual variable, e.g. “word count”, spread
across multiple variables, such as word counts for males and word counts
for females. We used the `gather()` function from the tidyr package to
stack up all the word counts into a single variable, create a new
variable to convey male vs. female, and do the replication needed for
the other variables.

Many data analytic projects will benefit from a script that marshals
data from different files, tidies the data, and writes a clean result to
file for further analysis.

Watch out for how **untidy** data seduces you into working with it more
than you should:
  
  - Data optimized for consumption by human eyeballs *is* attractive, so
it’s hard to remember it’s suboptimal for computation. How can
something that looks so pretty be so wrong?
  - Tidy data often has lots of repetition, which triggers hand-wringing
about efficiency and aesthetics. Until you can document a
performance problem, keep calm and tidy on.
- Tidying operations are unfamiliar to many of us and we avoid them,
subconsciously preferring to faff around with other workarounds that
are more familiar.

## Part 2

Enough about tidy data. How do I make it messy?


``` r
library(tidyverse)
#> Loading tidyverse: ggplot2
#> Loading tidyverse: tibble
#> Loading tidyverse: tidyr
#> Loading tidyverse: readr
#> Loading tidyverse: purrr
#> Loading tidyverse: dplyr
#> Conflicts with tidy packages ----------------------------------------------
#> filter(): dplyr, stats
#> lag():    dplyr, stats
lotr_tidy <- read_csv(file.path("data", "lotr_tidy.csv"))
#> Parsed with column specification:
#> cols(
#>   Film = col_character(),
#>   Race = col_character(),
#>   Gender = col_character(),
#>   Words = col_integer()
#> )

lotr_tidy
#> # A tibble: 18 × 4
#>                          Film   Race Gender Words
#>                         <chr>  <chr>  <chr> <int>
#> 1  The Fellowship Of The Ring    Elf Female  1229
#> 2  The Fellowship Of The Ring Hobbit Female    14
#> 3  The Fellowship Of The Ring    Man Female     0
#> 4              The Two Towers    Elf Female   331
#> 5              The Two Towers Hobbit Female     0
#> 6              The Two Towers    Man Female   401
#> 7      The Return Of The King    Elf Female   183
#> 8      The Return Of The King Hobbit Female     2
#> 9      The Return Of The King    Man Female   268
#> 10 The Fellowship Of The Ring    Elf   Male   971
#> 11 The Fellowship Of The Ring Hobbit   Male  3644
#> 12 The Fellowship Of The Ring    Man   Male  1995
#> 13             The Two Towers    Elf   Male   513
#> 14             The Two Towers Hobbit   Male  2463
#> 15             The Two Towers    Man   Male  3589
#> 16     The Return Of The King    Elf   Male   510
#> 17     The Return Of The King Hobbit   Male  2673
#> 18     The Return Of The King    Man   Male  2459

## practicing with spread: let's get one variable per Race
lotr_tidy %>% 
  spread(key = Race, value = Words)
#> # A tibble: 6 × 5
#>                         Film Gender   Elf Hobbit   Man
#> *                      <chr>  <chr> <int>  <int> <int>
#> 1 The Fellowship Of The Ring Female  1229     14     0
#> 2 The Fellowship Of The Ring   Male   971   3644  1995
#> 3     The Return Of The King Female   183      2   268
#> 4     The Return Of The King   Male   510   2673  2459
#> 5             The Two Towers Female   331      0   401
#> 6             The Two Towers   Male   513   2463  3589

## practicing with spread: let's get one variable per Gender
lotr_tidy %>% 
  spread(key = Gender, value = Words)
#> # A tibble: 9 × 4
#>                         Film   Race Female  Male
#> *                      <chr>  <chr>  <int> <int>
#> 1 The Fellowship Of The Ring    Elf   1229   971
#> 2 The Fellowship Of The Ring Hobbit     14  3644
#> 3 The Fellowship Of The Ring    Man      0  1995
#> 4     The Return Of The King    Elf    183   510
#> 5     The Return Of The King Hobbit      2  2673
#> 6     The Return Of The King    Man    268  2459
#> 7             The Two Towers    Elf    331   513
#> 8             The Two Towers Hobbit      0  2463
#> 9             The Two Towers    Man    401  3589

## practicing with spread ... and unite: let's get one variable per combo of Race and Gender
lotr_tidy %>% 
  unite(Race_Gender, Race, Gender) %>% 
  spread(key = Race_Gender, value = Words)
#> # A tibble: 3 × 7
#>                         Film Elf_Female Elf_Male Hobbit_Female Hobbit_Male
#> *                      <chr>      <int>    <int>         <int>       <int>
#> 1 The Fellowship Of The Ring       1229      971            14        3644
#> 2     The Return Of The King        183      510             2        2673
#> 3             The Two Towers        331      513             0        2463
#> # ... with 2 more variables: Man_Female <int>, Man_Male <int>

```

## Exercise 3:

Untidy your tidy using the `pivot_wider()` function instead of `spread()`. Save this as its own .csv file called `data/lotr_untidy_pivot.csv`. Add, commit, and push.