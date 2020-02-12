### Introduction to tabular data

* We will be working with data from the Portal Project.
    * Long-term experimental study of small mammals in Arizona.
    * Download `surveys`, `species`, and `plots` from `Datasets` into folder.
    * Need to know where the data is: Right click -> `Save link as`.

* Start/open a project (modeling good practice)

* Dataset is composed of three tables.
* Load these into `R` using `read_csv()`.

```
surveys <- read_csv("surveys.csv")
species <- read_csv("species.csv")
plots <- read_csv("plots.csv")
```

* Display data by clicking on it in `Environment`
* Three tables
    * `surveys` - main table, one row for each rodent captured, date on date,
      location, species ID, sex, and size
    * `species` - latin species names for each species ID + general taxon
    * `plots` - information on the experimental manipulations at the site

* Good tabular data structure -- keep it **tidy**
    * One table per type of data
        * Tables can be linked together to combine information.
    * Each row contains a single record.
        * A single observation or data point
    * Each column or field contains a single attribute.
        * A single type of information

### Packages

* Main way that reusable code is shared in R
* Combination of code, data, and documentation
* R has a rich ecosystem of packages for data manipulation & analysis
* Download and install packages with the R console:
    * `install.packages("dplyr")`
* Using a package:
    * Load all of the functions in the package: `library("dplyr")`

### Basic `dplyr`

* Modern data manipulation library for R

```
surveys <- read.csv("surveys.csv")
```

* Select a subset of columns.

```
select(surveys, year, month, day)
```

* They can occur in any order.

```
select(surveys, month, day, year)
```

* Use `filter()` to get only the rows that meet certain criteria.
    * Combine the data frame to be filtered with a series of conditional statements.
    * Column, condition, value
  
```
filter(surveys, species_id == "DS")
```

* Add new columns with calculated values using `mutate()`

```
mutate(surveys, hindfoot_length_cm = hindfoot_length / 10)
```

* If we look at `surveys` now will it contain the new column?
* *Open `surveys`*
* All of these commands produce new values, data frames in this case
* To store them for later use we need to assign them to a variable

```
surveys_plus <- mutate(surveys,
                       hindfoot_length_cm = hindfoot_length / 10)
```

* Or we could overwrite the existing variable if we don't need it

```
surveys <- mutate(surveys,
                  hindfoot_length_cm = hindfoot_length / 10)
```

### Basic aggregation

* Aggregation combines rows into groups based on one of more columns.
* Calculates combined values for each group.
* First, group the data frame.

```
group_by(surveys, species_id)
```

* Different looking kind of `data.frame`
    * Source, grouping, and data type information

```r
surveys_by_species <- group_by(surveys, species_id)
```

* Use `summarize()` to calculate values for each group.
* Count the number of rows for each group (individuals in each species).

```r
summarize(surveys_by_species, abundance = n())
```

* Can group by multiple columns

```r
surveys_by_species_plot <- group_by(surveys, species_id, plot_id)
summarize(surveys_by_species, abundance = n())
```

* Use any function that returns a single value from a vector.
* E.g., mean, max, min

```r
species_weight <- summarize(surveys_by_species_plot, avg_weight = mean(weight))
```

* *Open table*
* Why did we get `NA`?
    * `mean(weight)` returns `NA` when `weight` has missing values (`NA`)
* Can fix using `mean(weight, na.rm = TRUE)`

```
species_weight <- summarize(surveys_by_species,
                            avg_weight = mean(weight, na.rm = TRUE))
```

* Still has `NaN` for species that have never been weighed
* Can use `na.omit()` to drop rows with `NA` or `NaN` in any column

```
na.omit(surveys_weight)
```

#### Intermediate variables

* Run a command
* Store the output in a variable
* Use that variable later in the code
* Repeat

* Determine the mean weight of DS in each year

```
ds_data <- filter(surveys, species_id == "DS")
ds_data_by_year <- group_by(ds_data, year)
ds_weight_by_year <- summarize(ds_data_by_year,
                               avg_weight = mean(weight, na.rm = TRUE))
```

> Do [Portal Data Manipulation Exercise 1-2]({{ site.baseurl }}/exercises/Portal-data-manip-R)

#### Pipes

* Intermediate variables can get cumbersome if their are lots of steps.
* `%>%` ("pipe") takes the output of one command and passes it as input to the
  next command
* Want to take the mean of a vector
* Normally we would run the `mean` function with the vector as the input:

```
x = c(1, 2, 3)
mean(x)
```

* Instead we could pipe the vector into the function

```
x %>% mean()
```

* So `x` becomes the first argument in `mean`
* If we want to add other arguments they get added to the function call

```
x = c(1, 2, 3, NA)
mean(x, na.rm = TRUE)
x %>% mean(na.rm = TRUE)
```

* *Questions?*

```
surveys %>%
  filter(species_id == "DS", !is.na(weight))
```

```
ds_weight_by_year <- surveys %>%
  filter(species_id == "DS") %>%
  group_by(year) %>%
  summarize(avg_weight = mean(weight, na.rm = TRUE))
```

### Multiple filter conditions

* We can filter on multiple conditions at once
* In computing we combine conditions in two ways "and" & "or"
* "and" means that all of the conditions must be true
* Do this in `dplyr` using either additional comma separate arguments

```
filter(surveys, species_id == "DS", year > 1995)
```

* Or by using `&`

```
filter(surveys, species_id == "DS" & year > 1995)
```

* "or" means that one or more of the conditions must be true
* Do this using `|`
* Say we wanted data on all of the *Dipodomys* species.

```
filter(surveys, species_id == "DS" | species_id == "DM" | species_id == "DO")
```

### Filtering by aggregated properties

* You can also filter based on aggregated values
* If we wanted to estimate species weights only for species with > 100 individuals

```r
species_weights <- surveys %>%
  group_by(species) %>%
  filter(n() > 100) %>%
  summarize(avg_weight = mean(weight, na.rm = TRUE))
```