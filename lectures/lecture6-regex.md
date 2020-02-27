In this tutorial, we will use the Gapminder data as an example to demonstrate using regular expression in R. First, let's start off by loading the Gapminder dataset into R. 

We will also need to use some functions from the **stringr** package. It provids a clean, modern alternative to common string operations, and is sometimes easier to remember and use than R basic string functions.

```{r input}
library(stringr)
install.packages("gapminder")
gDat <- gapminder::gapminder
str(gDat)
```

## String functions related to regular expression
Regular expression is a pattern that describes a specific set of strings with a common structure. It is heavily used for string matching / replacing in all programming languages, although specific syntax may differ a bit. It is truly the heart and soul for string operations. In R, many string functions in `base` R as well as in `stringr` package use regular expressions, even Rstudio's search and replace allows regular expression, we will go into more details about these functions later this week:       

  * identify match to a pattern: `grep(..., value = FALSE)`, `grepl()`, `stringr::str_detect()`
  * extract match to a pattern: `grep(..., value = TRUE)`, `stringr::str_extract()`, `stringr::str_extract_all()`     
  * locate pattern within a string, i.e. give the start position of matched patterns. `regexpr()`, `gregexpr()`, `stringr::str_locate()`, `string::str_locate_all()`     
  * replace a pattern: `sub()`, `gsub()`, `stringr::str_replace()`, `stringr::str_replace_all()`     
  * split a string using a pattern: `strsplit()`, `stringr::str_split()`     

## Regular expression syntax 

Regular expressions typically specify characters (or character classes) to seek out, possibly with information about repeats and location within the string. This is accomplished with the help of metacharacters that have specific meaning: `$ * + . ? [ ] ^ { } | ( ) \`. We will use some small examples to introduce regular expression syntax and what these metacharacters mean. 

### Escape sequences

There are some special characters in R that cannot be directly coded in a string. For example, let's say you specify your pattern with single quotes and you want to find countries with the single quote `'`. You would have to "escape" the single quote in the pattern, by preceding it with `\`, so it's clear it is not part of the string-specifying machinery: 

```{r quote_error}
grep('\'', levels(gDat$country), value = TRUE)
```

There are other characters in R that require escaping, and this rule applies to all string functions in R, including regular expressions. See [here](https://stat.ethz.ch/R-manual/R-devel/library/base/html/Quotes.html) for a complete list of R esacpe sequences.        

  * `\'`: single quote. You don't need to escape single quote inside a double-quoted string, so we can also use `"'"` in the previous example.        
  * `\"`: double quote. Similarly, double quotes can be used inside a single-quoted string, i.e. `'"'`.          
  * `\n`: newline.   
  * `\r`: carriage return.   
  * `\t`: tab character.   

> Note: `cat()` and `print()` to handle escape sequences differently, if you want to print a string out with these sequences interpreted, use `cat()`.      
```{r cat_print}
print("a\nb")
cat("a\nb")
```

### Quantifiers

Quantifiers specify how many repetitions of the pattern.   

  * `*`: matches at least 0 times.   
  * `+`: matches at least 1 times.     
  * `?`: matches at most 1 times.    
  * `{n}`: matches exactly n times.    
  * `{n,}`: matches at least n times.    
  * `{n,m}`: matches between n and m times.     

```{r quantifiers}
(strings <- c("a", "ab", "acb", "accb", "acccb", "accccb"))
grep("ac*b", strings, value = TRUE)
grep("ac+b", strings, value = TRUE)
grep("ac?b", strings, value = TRUE)
grep("ac{2}b", strings, value = TRUE)
grep("ac{2,}b", strings, value = TRUE)
grep("ac{2,3}b", strings, value = TRUE)
```

#### Exercise 1 
Write a line of code to find all countries with `ee` in Gapminder **using quantifiers**. Add, commit, and push. 


### Position of pattern within the string 

  * `^`: matches the start of the string.   
  * `$`: matches the end of the string.   
  * `\b`: matches the empty string at either edge of a _word_. Don't confuse it with `^ $` which marks the edge of a _string_.   
  * `\B`: matches the empty string provided it is not at an edge of a word.    

```{r position}
(strings <- c("abcd", "cdab", "cabd", "c abd"))
grep("ab", strings, value = TRUE)
grep("^ab", strings, value = TRUE)
grep("ab$", strings, value = TRUE)
grep("\\bab", strings, value = TRUE)
```

#### Exercise 2 
Write code to find all `.R` files in the main folder of your repository.  

Hint: use `dir()` to get a vector of all filenames

Add, commit, and push.

### Operators

  * `.`: matches any single character, as shown in the first example. 
  * `[...]`: a character list, matches any one of the characters inside the square brackets. We can also use `-` inside the brackets to specify a range of characters.   
  * `[^...]`: an inverted character list, similar to `[...]`, but matches any characters __except__ those inside the square brackets.  
  * `\`: suppress the special meaning of metacharacters in regular expression, i.e. `$ * + . ? [ ] ^ { } | ( ) \`, similar to its usage in escape sequences. Since `\` itself needs to be escaped in R, we need to escape these metacharacters with double backslash like `\\$`.   
  * `|`: an "or" operator, matches patterns on either side of the `|`.  
  * `(...)`: grouping in regular expressions. This allows you to retrieve the bits that matched various parts of your regular expression so you can alter them or use them for building up a new string. Each group can than be refer using `\\N`, with N being the No. of `(...)` used. This is called __backreference__.    

```{r operators}
(strings <- c("^ab", "ab", "abc", "abd", "abe", "ab 12"))
grep("ab.", strings, value = TRUE)
grep("ab[c-e]", strings, value = TRUE)
grep("ab[^c]", strings, value = TRUE)
grep("^ab", strings, value = TRUE)
grep("\\^ab", strings, value = TRUE)
grep("abc|abd", strings, value = TRUE)
gsub("(ab) 12", "\\1 34", strings)
```

#### Excercise 3.

Write code to find countries in Gapminder with letter `i` or `t`, and ends with `land`, and replace `land` with `LAND` using backreference. 

Add, commit, and push.


### Character classes

Character classes allows to -- surprise! -- specify entire classes of characters, such as numbers, letters, etc. There are two flavors of character classes, one uses `[:` and `:]` around a predefined name inside square brackets and the other uses `\` and a special character. They are sometimes interchangeable.   

  * `[:digit:]` or `\d`: digits, 0 1 2 3 4 5 6 7 8 9, equivalent to `[0-9]`.  
  * `\D`: non-digits, equivalent to `[^0-9]`.  
  * `[:lower:]`: lower-case letters, equivalent to `[a-z]`.  
  * `[:upper:]`: upper-case letters, equivalent to `[A-Z]`.  
  * `[:alpha:]`: alphabetic characters, equivalent to `[[:lower:][:upper:]]` or `[A-z]`.  
  * `[:alnum:]`: alphanumeric characters, equivalent to `[[:alpha:][:digit:]]` or `[A-z0-9]`.   
  * `\w`: word characters, equivalent to `[[:alnum:]_]` or `[A-z0-9_]`.  
  * `\W`: not word, equivalent to `[^A-z0-9_]`.  
  * `[:xdigit:]`: hexadecimal digits (base 16), 0 1 2 3 4 5 6 7 8 9 A B C D E F a b c d e f, equivalent to `[0-9A-Fa-f]`.
  * `[:blank:]`: blank characters, i.e. space and tab.  
  * `[:space:]`: space characters: tab, newline, vertical tab, form feed, carriage return, space.
  * `\s`: space, ` `.  
  * `\S`: not space.  
  * `[:punct:]`: punctuation characters, ! " # $ % & ' ( ) * + , - . / : ; < = > ? @ [ \ ] ^ _ ` { | } ~.
  * `[:graph:]`: graphical (human readable) characters: equivalent to `[[:alnum:][:punct:]]`.
  * `[:print:]`: printable characters, equivalent to `[[:alnum:][:punct:]\\s]`.
  * `[:cntrl:]`: control characters, like `\n` or `\r`, `[\x00-\x1F\x7F]`.  
Note:       
* `[:...:]` has to be used inside square brackets, e.g. `[[:digit:]]`.     
* `\` itself is a special character that needs escape, e.g. `\\d`. Do not confuse these regular expressions with R escape sequences such as `\t`.      

## General modes for patterns

There are different [syntax standards](http://en.wikipedia.org/wiki/Regular_expression#Standards) for regular expressions, and R offers two:

  * POSIX extended regular expressions (default)
  * Perl-like regular expressions.
  
You can easily switch between by specifying `perl = FALSE/TRUE` in `base` R functions, such as `grep()` and `sub()`. For functions in the `stringr` package, wrap the pattern with `perl()`. The syntax between these two standards are a bit different sometimes, see an example [here](http://www.inside-r.org/packages/cran/stringr/docs/perl). If you had previous experience with Python or Java, you are probably more familiar with the Perl-like mode. But for this tutorial, we will only use R's default POSIX standard.  

There's one last type of regular expression -- "fixed", meaning that the pattern should be taken literally. Specify this via `fixed = TRUE` (base R functions) or wrapping with `fixed()` (`stringr` functions). For example, `"A.b"` as a regular expression will match a string with "A" followed by any single character followed by "b", but as a fixed pattern, it will only match a literal "A.b".  

```{r fixed}
(strings <- c("Axbc", "A.bc"))
pattern <- "A.b"
grep(pattern, strings, value = TRUE)
grep(pattern, strings, value = TRUE, fixed = TRUE)
```

By default, pattern matching is case sensitive in R, but you can turn it off with `ignore.case = TRUE` (base R functions) or wrapping with `ignore.case()` (`stringr` functions). Alternatively, you can use `tolower()` and `toupper()` functions to convert everything to lower or upper case. Take the same example above: 

```{r ignore.case}
pattern <- "a.b"
grep(pattern, strings, value = TRUE)
grep(pattern, strings, value = TRUE, ignore.case = TRUE)
```

### Exercise 4

Find continents in Gapminder with letter `o` in it.  

Add, commit, and push.


## Resources

  * Regular expression in R [official document](https://stat.ethz.ch/R-manual/R-devel/library/base/html/regex.html).  
  * Perl-like regular expression: regular expression in perl [manual](http://perldoc.perl.org/perlre.html#Regular-Expressions).   
  * [`qdapRegex` package](http://trinkerrstuff.wordpress.com/2014/09/27/canned-regular-expressions-qdapregex-0-1-2-on-cran/): a collection of handy regular expression tools, including handling abbreviations, dates, email addresses, hash tags, phone numbers, times, emoticons, and URL etc.   
  * Recently, there are some attemps to create human readable regular expression packages, [Regularity](https://github.com/andrewberls/regularity) in Ruby is a very successful one. Unfortunately, its implementation in R is still quite beta at this stage, not as friendly as Regularity yet. But keep an eye out, better packages may become available in the near future!    
  * There are some online tools to help learn, build and test regular expressions. On these websites, you can simply paste your test data and write regular expression, and matches will be highlighted.   
  + [regexpal](http://regexpal.com/)    
  + [RegExr](http://www.regexr.com/)   