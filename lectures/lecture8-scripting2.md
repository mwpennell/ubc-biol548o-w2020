## Conditionals and iteration

Today's lecture is about two programming techniques that I find to be extremely useful and will hopefully become an intergral part of your data science arsenal: conditions and iteration.

## Conditionals

* Usage: 
    * Generate `"logical"`:
        * `TRUE` if the condition is satisfied 
        * `FALSE` if the condition is not satisfied
* Operators:
    * `==`, `!=`
    * `<`, `>`
    * `<=`, `>=`
    * `%in%`

```r
10 > 5
"aang" == "aang"
3 != 3
"dog" %in% c("cat", "dog", "rabbit")
```

* Combine:
    * `and`, `&` 
    * `or`, `|`

```r
5 > 2 & 6 >=10
5 > 2 | 6 >=10
```

* Vectors of values compared to a single value return one logical per value

```r
c(1, 1, 2, 3, 1) == 1
```

* Checks each value to see if equal to 1
* This is what subsetting approaches use to subset
* They keep the values where the condition is `TRUE`

```r
site = c('a', 'b', 'c', 'd')
state = c('FL', 'FL', 'GA', 'AL')
state == 'FL'
site[state == 'FL']
site[c(TRUE, TRUE, FALSE, FALSE)]
```

* Used in `dplyr::filter()` and other methods for subsetting data

### Exercise 1
Create the following variables.

```
w <- 10.2
x <- 1.3
y <- 2.8
z <- 17.5
colors <- c("red", "blue", "green")
masses <- c(45.2, 36.1, 27.8, 81.6, 42.4)
dna1 <- "attattaggaccaca"
dna2 <- "attattaggaacaca"
```

In a new file `lecture8-exercises.R` write code that can prints whether or not the following statements are `TRUE` or `FALSE`.

1. `w` is greater than 10
2. `"green"` is in `colors`
3. `x` is greater than `y`
4. Each value in `masses` is greater than 40.

Add, commit, and push.
 

### `if` statements

* Conditional statements generate logical values to filter inputs.
* `if` statements use conditional statements to control flow of the program.

```r
if (the conditional statement is TRUE ) {
  do something
}
```

* Example

```r
x <- 6
if (x > 5){
  x <- x^2
}
x
```

* `x > 5` is `TRUE`, so the code in the `if` runs
* `x` is now 6^2 or 36
* Change `x` to 4

```r
x = 4
if (x > 5){
  x <- x^2
}
x
```

* `x > 5` is `FALSE`, so the code in the `if` doesn't run
* `x` is still 4
* This is *not* a function, so *everything that happens in the if statement influences the global environment* (remember scoping: what happens in a function stays in a function)


* Different mass calculations for different vegetation types

```r
veg_type <- "tree"
volume <- 16.08
if (veg_type == "tree") {
  mass <- 2.65 * volume^0.9
  }
mass
```

### Exercise 2

In your `lecture8-exercises.R` file, complete (i.e., copy into your code and them modify) the following `if` statement so that if `age_class` is equal to "sapling" it sets `y <- 10`.

```r
age_class = "sapling"
if (){
  
}
y
```

Add, commit, and push.

### Alternative conditioning

* Often want to chose one of several options
* Can add more conditions and associated actions with `else if`
 
```r
veg_type <- "grass"
volume <- 16.08
if (veg_type == "tree") {
  mass <- 2.65 * volume^0.9
} else if (veg_type == "grass") {
  mass <- 0.65 * volume^1.2
}
mass
```

* Checks the first condition
* If `TRUE` runs that condition's code and skips the rest
* If not it checks the next one until it runs out of conditions

* Can specify what to do if none of the conditions is `TRUE` using `else` on its own

```r
veg_type <- "shrub"
volume <- 16.08
if (veg_type == "tree") {
  mass <- 2.65 * volume^0.9
} else if (veg_type == "grass") {
  mass <- 0.65 * volume^1.2
} else {
  mass <- NA
}
mass
```

### Exercise 3

1. In your R file, complete the following `if` statement so that if `age_class` is equal to "sapling" it sets `y <- 10` and if `age_class` is equal to "seedling" it sets `y <- 5`.

```r
age_class <- "seedling"
if (){
  
}
y
```

2. Complete the following `if` statement so that if `age_class` is equal to "sapling" it sets `y <- 10` and if `age_class` is equal to "seedling" it sets `y <- 5` and if `age_class` is something else then it sets the value of `y <- 0`.

```r
age_class = "adult"
if (){
  
}
y
```
Add, commit, and push.

### Multiple ifs vs else if

* Multiple ifs check each conditional separately
* Executes code of all conditions that are `TRUE`

```r
x <- 5
if (x > 2){
  x * 2
}
if (x > 4){
  x * 4
}
```

* `else if` checks each condition sequentially
* Executes code for the first condition that is `TRUE`

```r
x <- 5
if (x > 2){
  x * 2
} else if (x > 4){
  x * 4
}
```

### Wrap conditionals within a function

```r
est_mass <- function(volume, veg_type){
  if (veg_type == "tree") {
    mass <- 2.65 * volume^0.9
  } else if (veg_type == "grass") {
    mass <- 0.65 * volume^1.2
  } else {
    print("I don't know how to convert volume to mass for that vegetation type")
    mass <- NA
  }
  return(mass)
}

est_mass(1.6, "tree")
est_mass(1.6, "grass")
est_mass(1.6, "shrub")
```

### Exercise 4

To make it even easier to work with your dinosaur size estimation functions that you wrote last class, you
decide to create a function that lets you specify which dinosaur group you need to estimate the size of by name and then have the function automatically choose
the right parameters.

Create a new function `get_mass_from_length_by_name()` that takes two arguments,
the `length` and the name of the dinosaur group. Inside this function use
`if`/`else if`/`else` statements to check to see if the name is one of the
following values and if so set `a` and `b` to the appropriate values.

* *Stegosauria*:  `a` = `10.95` and `b` = `2.64` ([Seebacher 2001](http://www.jstor.org/stable/4524171)).
* *Theropoda*:  `a` = `0.73` and `b` = `3.63` ([Seebacher 2001](http://www.jstor.org/stable/4524171)).
* *Sauropoda*:  `a` = `214.44` and `b` = `1.46` ([Seebacher 2001](http://www.jstor.org/stable/4524171)).

If the name is not any of these values set `a` and `b` to `NA`.

Once the function has assigned `a` and `b` have it run `get_mass_from_length()`
with the appropriate values and return the estimated mass.

Run the function for:

1. A *Stegosauria* that is 10 meters long.
2. A *Theropoda* that is 8 meters long.
3. A *Sauropoda* that is 12 meters long.
4. A *Ankylosauria* that is 13 meters long.

Add, commit, and push.

### Nested conditionals

* Sometimes decisions are more complicated
* Can "nest" conditionals inside of one another

```r
est_mass <- function(volume, veg_type, age){
  if (veg_type == "tree") {
    if (age < 5) {
      mass <- 1.6 * volume^0.8
    } else {
      mass <- 2.65 * volume^0.9
  }
  } else if (veg_type == "grass" | veg_type == "shrub") {
    mass <- 0.65 * volume^1.2
  } else {
    print("I don't know how to convert volume to mass for that vegetation type")
    mass <- NA
  }
  return(mass)
}

est_mass(1.6, "tree", age = 2)
est_mass(1.6, "shrub", age = 5)
```

* First checks if the vegetation type is "tree"
* If it is checks to see if it is < 5 years old
* If so does one calculation, if not does another
* But nesting can be difficult to follow so try to minimize it

### Two things that might trip you up

```r
> x <- 1.3
> y <- 2.8
>  x * 2 + 0.2 == y
[1] FALSE
> 1.3 * 2 + 0.2
[1] 2.8
```

* What's going on?
* Unexpected result from computer arithmetic
* Numbers combine to include very small trailing digit  
* See this more easily in Python

```r
>>> 1.3 * 2 + 0.2 == 2.8
False
>>> 1.3 * 2 + 0.2
2.8000000000000003
```

* `round(x * 2 + 0.2, 1) == y`
* `all.equal(x * 2 + 0.2, y)`


## Iteration

Iteration basically just means running a function multiple times. For example, you might want perform the same operation across multiple columns of your dataset but want to avoid having to copy and paste code over and over again. (At some point, the copy and paste approach becomes totally unworkable; typical phylogenetic analyses, for example, repeat the same calculations hundreds of millions of times!)

Iteration in R can, broadly speaking, be done in three different ways:
* loops (for loops and while loops)
* apply functions 
* map functions (in the *purrr* package, which is part of the *tidyverse*)



### Basic `for` loop

* Fundamental structure for repetition in programming
* Do same action to each item in a list of things

```r
for (item in list_of_items) {
  do_something(item)
}
```

* Need `print()` to display values inside a loop or function.

```r
volumes = c(1.6, 3, 8)
for (volume in volumes){
  print(2.65 * volume^0.9)
}
```

* This does the same exact thing as

```r
volume <- volumes[1]
print(2.65 * volume ^ 0.9)
volume <- volumes[2]
print(2.65 * volume ^ 0.9)
volume <- volumes[3]
print(2.65 * volume ^ 0.9)
```

* Can have many rows in a loop body

```r
for (volume in volumes){
   mass <- 2.65 * volume ^ 0.9
   mass_lb <- mass * 2.2
   print(mass_lb)
}
```

### Exercise 5

In your `lecture8-exercises.R` file, complete the following 5 exercises:

1\. The code below prints the numbers 1 through 5 one line at a time. Modify it to print the numbers 2 through 16.

```r
for (i in 1:5){
  print(i)
}
```

2\. The code below prints the numbers 1 through 5 one line at a time. Modify it to print each of these numbers multiplied by 3.

```r
for (i in 1:5){
  print(i)
}
```

3\. Complete the code below so that it prints out the name of each bird one line at a time.

```r
birds = c('robin', 'woodpecker', 'blue jay', 'sparrow')
for (i in 1:length(_________)){
  print(birds[__])
}
```

4\. Complete the code below so that it stores one area for each radius.

```r
radius <- c(1.3, 2.1, 3.5)
areas <- vector(_____ = "numeric", length = ______)
for (__ in 1:length(________)){
  areas[__] <- pi * radius[i] ^ 2
}
areas
```

5\. Complete the code below to calculate an area for each pair of `lengths` and `widths`, store the areas in a vector, and after they are all calculated print them out: 

```r
lengths = c(1.1, 2.2, 1.6)
widths = c(3.5, 2.4, 2.8)
areas <- vector(length = __________)
for (i in _____) {
  areas[__] <- lengths[__] * widths[__]
}
areas
```
Add, commit, and push.


### Looping with an index & storing results

* Loops over integers and uses these integers to access the vector the associated positions

```r
for (i in 1:length(volumes)){
   mass <- 2.65 * volumes[i] ^ 0.9
   print(mass)
}
```

* Use this "index" to get the values at that position
* Can use the "index" for multiple vectors

* Looping with an index allows us to store results calculated in the loop
* First create an empty vector the length of the results
* `mode` is the type of data we are going to store
* `length` is the length of the vector

```r
masses <- vector(mode = "numeric", length = length(volumes))
masses
```

* Then add each result in the right position
* For each trip through the loop put the output into the empty vector at the ith position

```r
for (i in 1:length(volumes)){
   mass <- 2.65 * volumes[i] ^ 0.9
   masses[i] <- mass
}
masses
```

* Looping with an index also allows us to access values from multiple vectors


```r
b0 <- c(2.65, 1.28, 3.29)
b1 <- c(0.9, 1.1, 1.2)
masses <- vector(mode="numeric", length=length(volumes))
for (i in seq_along(volumes)){
   mass <- b0[i] * volumes[i] ^ b1[i]
   masses[i] <- mass
}
```
