# Part 4: `mutate()`, `group_by()/summarize()`


## Learning Objectives

- **Learn** and apply `mutate()` to change the data type of a variable
- **Apply** `mutate()` to calculate a new variable based on other variables in a `data.frame`.
- **Apply** `case_when` in a `mutate()` statement to make a continuous variable categorical
- **Apply** `group_by()/summarize()` as a pattern to get summary statistics, including counts, means, and standard deviations within a category.
- **Standardize** variable names using `clean_names()`


## Getting set up


```r
# load libraries
library(tidyverse)
library(readxl)
library(janitor)
library(here)

smoke_complete <- read_excel(here("data/smoke_complete.xlsx"), 
                             sheet=1, 
                             na="NA")
```



## `mutate()` - A confusing name, a powerful `dplyr` verb


So what is mutate? `mutate()` is one of the most useful `dplyr` verbs. You can use it to transform data (variables in your `data.frame`) and add it as a new variable into the data.frame.

I tend to think of this much like adding a formula in Excel to calculate the value of a new column based on previous columns. You can do lots of things such as:

- subtract one column from another
- convert the units of one column to new units (such as days to years)
- change the capitalization of categories in a variable
- recode a continuous variable to be a categorical one




### Using `mutate` to calculate a new variable based on other variables


One use for `mutate` is to do Excel type calculations using other columns on the data. For instance, we might want to calcluate the sum of `age_at_diagnoses`and `days_to_death` to get the `age_at_death`.


```r
smoke_complete %>% 
    mutate(age_at_death = age_at_diagnosis + days_to_death) %>%
    glimpse()
```

```
## Rows: 1,152
## Columns: 21
## $ primary_diagnosis           <chr> "C34.1", "C34.1", "C34.3", "C34.1", "C3...
## $ tumor_stage                 <chr> "stage ia", "stage ib", "stage ib", "st...
## $ age_at_diagnosis            <dbl> 24477, 26615, 28171, 27154, 23370, 1902...
## $ vital_status                <chr> "dead", "dead", "dead", "alive", "alive...
## $ morphology                  <chr> "8070/3", "8070/3", "8070/3", "8083/3",...
## $ days_to_death               <dbl> 371, 136, 2304, NA, NA, 345, 716, 2803,...
## $ state                       <chr> "live", "live", "live", "live", "live",...
## $ tissue_or_organ_of_origin   <chr> "C34.1", "C34.1", "C34.3", "C34.1", "C3...
## $ days_to_birth               <dbl> -24477, -26615, -28171, -27154, -23370,...
## $ site_of_resection_or_biopsy <chr> "C34.1", "C34.1", "C34.3", "C34.1", "C3...
## $ days_to_last_follow_up      <dbl> NA, NA, 2099, 3747, 3576, NA, NA, 1810,...
## $ cigarettes_per_day          <dbl> 10.9589041, 2.1917808, 1.6438356, 1.095...
## $ years_smoked                <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, 26,...
## $ gender                      <chr> "male", "male", "female", "male", "fema...
## $ year_of_birth               <dbl> 1936, 1931, 1927, 1930, 1942, 1953, 193...
## $ race                        <chr> "white", "asian", "white", "white", "no...
## $ ethnicity                   <chr> "not hispanic or latino", "not hispanic...
## $ year_of_death               <dbl> 2004, 2003, NA, NA, NA, 2005, 2006, NA,...
## $ bcr_patient_barcode         <chr> "TCGA-18-3406", "TCGA-18-3407", "TCGA-1...
## $ disease                     <chr> "LUSC", "LUSC", "LUSC", "LUSC", "LUSC",...
## $ age_at_death                <dbl> 24848, 26751, 30475, NA, NA, 19370, 276...
```


### Your Turn

Create a variable called `cigarettes_total` by multiplying `cigarettes_per_day` by `-days_to_birth` below:


```r
smoke_complete %>%
    mutate(cigarettes_total = ) 
```




### Using `mutate` to make our `character` variables into `factors`

Remember `factor`s? Well, we can use `mutate()` to make a `character` variable a `factor`.

Let's convert `gender` from `character` into `factor`. We'll then pipe the output into `glimpse()` so you can see the variable types.




```r
smoke_complete %>% 
    #reassign the gender variable to be a factor
    mutate(gender = factor(gender)) %>%
    glimpse()
```

```
## Rows: 1,152
## Columns: 20
## $ primary_diagnosis           <chr> "C34.1", "C34.1", "C34.3", "C34.1", "C3...
## $ tumor_stage                 <chr> "stage ia", "stage ib", "stage ib", "st...
## $ age_at_diagnosis            <dbl> 24477, 26615, 28171, 27154, 23370, 1902...
## $ vital_status                <chr> "dead", "dead", "dead", "alive", "alive...
## $ morphology                  <chr> "8070/3", "8070/3", "8070/3", "8083/3",...
## $ days_to_death               <dbl> 371, 136, 2304, NA, NA, 345, 716, 2803,...
## $ state                       <chr> "live", "live", "live", "live", "live",...
## $ tissue_or_organ_of_origin   <chr> "C34.1", "C34.1", "C34.3", "C34.1", "C3...
## $ days_to_birth               <dbl> -24477, -26615, -28171, -27154, -23370,...
## $ site_of_resection_or_biopsy <chr> "C34.1", "C34.1", "C34.3", "C34.1", "C3...
## $ days_to_last_follow_up      <dbl> NA, NA, 2099, 3747, 3576, NA, NA, 1810,...
## $ cigarettes_per_day          <dbl> 10.9589041, 2.1917808, 1.6438356, 1.095...
## $ years_smoked                <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, 26,...
## $ gender                      <fct> male, male, female, male, female, male,...
## $ year_of_birth               <dbl> 1936, 1931, 1927, 1930, 1942, 1953, 193...
## $ race                        <chr> "white", "asian", "white", "white", "no...
## $ ethnicity                   <chr> "not hispanic or latino", "not hispanic...
## $ year_of_death               <dbl> 2004, 2003, NA, NA, NA, 2005, 2006, NA,...
## $ bcr_patient_barcode         <chr> "TCGA-18-3406", "TCGA-18-3407", "TCGA-1...
## $ disease                     <chr> "LUSC", "LUSC", "LUSC", "LUSC", "LUSC",...
```


One thing to notice: we are doing something called *reassignment* here. We're taking the previous values of our variable (`gender`), doing something to it (making it a `factor`), and then reassigning the variable `gender` to our fixed set of values.


Remember when we wanted to decide the order of the categories in the factor? We can assign that order using the `levels` argument in `factor()`. To show the order, we can pipe the output into a command called `tabyl()`, from the `janitor` package, which is a `tidyverse` friendly version of `table()`.



```r
smoke_complete %>% 
    #reassign the gender variable to be a factor
    mutate(gender = factor(gender, levels = c("female", "male"))) %>%
    tabyl(gender)
```

```
##  gender   n   percent
##  female 366 0.3177083
##    male 786 0.6822917
```

Notice that the `female` value goes before the `male`, which is what we wanted.






### Using `mutate` to make a continuous variable categorical using `case_when`


Say we want to make the `cigarettes_per_day` into a categorical variable with the values:


* 0-5 cigarettes/day
* 6+ cigarettes/day


How would we do that?

Well, we need to follow the basic pattern for each of our categories:

*condition* ~ *category name*


The *left* side of the `~` is where we can specify how we define the *category&. 

The *right* side of the `~` is where we can specify the *category name* (as a character).

In the example below,

`cigarettes_per_day <= 5` is our left side,
and `0-5` is our right side (our category).

We need to do this for each level in our category.


```r
smoke_complete %>% 
    mutate(
        cigarettes_category = case_when(
            cigarettes_per_day <= 5 ~ '0-5',
            cigarettes_per_day > 5 ~ '6+'
            )
        ) %>%
    mutate(cigarettes_category = 
               factor(cigarettes_category,
                      levels = c("0-5", "6+")
                      )
           ) %>%
    tabyl(cigarettes_category)
```

```
##  cigarettes_category    n    percent
##                  0-5 1060 0.92013889
##                   6+   92 0.07986111
```



### Your Turn

Modify the code below to recode `cigarettes_category` to have 3 levels:

* `0-5` 
* `6-10` 
* `11+`

Hint: you'll have to chain conditions with a `&` to get the `6-10` category.



```r
smoke_complete %>% 
    mutate(
        cigarettes_category = case_when(
            cigarettes_per_day <= 5 ~ '0-5',
            cigarettes_per_day > 5 ~ '6+'
            )
        ) %>%
    mutate(cigarettes_category = 
               factor(cigarettes_category,
                      levels = c("0-5", "6+")
                      )
           ) %>%
    tabyl(cigarettes_category)
```

```
##  cigarettes_category    n    percent
##                  0-5 1060 0.92013889
##                   6+   92 0.07986111
```


## `group_by()/summarize()`

These verbs usually go together and allow you to produce summaries on your data.


### `summarize()`

The `summarize()` verb produces summaries across your data. 

For example, if I wanted to find the average `cigarettes_per_day` across all my data, I'd do this:



```r
smoke_complete %>%
    summarize(average_cigarettes = mean(cigarettes_per_day))
```

```
## # A tibble: 1 x 1
##   average_cigarettes
##                <dbl>
## 1               2.61
```

What are the kinds of things that summarize is useful for?

Useful functions (from https://dplyr.tidyverse.org/reference/summarise.html)

- Center: `mean()`, `median()`
- Spread: `sd()` (standard deviation) 
- Range: `min()`, `max()` 
- Position: `first()`, `last()`, `nth()`
- Count: `n()`, `n_distinct()`



By itself, `summarize()` is ok. But it's really combined with `group_by()` that it becomes extremely powerful.



### `group_by()/summarize()`


These two verbs always go together. `group_by()` doesn't do anything by itself, output wise. But what it does is split our data frame into a number of smaller `data.frame`s split out by category. Then we use `summarize` to do some sort of summary calculation (such as counting or calculating the mean) on these smaller `data.frame`s:


For example, if we want to calculate the mean `cigarettes_per_day` smoked between `male`s and `female`s, we'd do the following:


```r
smoke_complete %>%
   group_by(gender) %>%
   summarize(mean_cigarettes = mean(cigarettes_per_day))
```

```
## `summarise()` ungrouping output (override with `.groups` argument)
```

```
## # A tibble: 2 x 2
##   gender mean_cigarettes
##   <chr>            <dbl>
## 1 female            2.07
## 2 male              2.86
```




### `group_by()/summarize` to calculate mean and standard deviation values

Below, we'll calculate the average number of cigarettes smoked per day for males and females, and the standard deviation.



```r
smoke_complete %>%
    group_by(gender) %>%
    summarize(mean = mean(cigarettes_per_day), 
              sd = sd(cigarettes_per_day))
```

```
## `summarise()` ungrouping output (override with `.groups` argument)
```

```
## # A tibble: 2 x 3
##   gender  mean    sd
##   <chr>  <dbl> <dbl>
## 1 female  2.07  1.53
## 2 male    2.86  2.19
```




### `group_by()` to `count()` things

What if we want to know the number of subjects in our dataset with each type of `disease`? We can use the `n()` function.


```r
smoke_complete %>%
    group_by(disease) %>%
    summarize(count = n())
```

```
## `summarise()` ungrouping output (override with `.groups` argument)
```

```
## # A tibble: 3 x 2
##   disease count
##   <chr>   <int>
## 1 BLCA      224
## 2 CESC       92
## 3 LUSC      836
```


### Your Turn

Use `group_by()/summarize()` to calculate the maximum cigarettes per day within each `disease` category.



```r
smoke_complete %>%
    group_by(------) %>%
    summarize(max_cigarettes = -------)
```



## Standardizing variable names: `clean_names()`

The last package we'll talk about today is the `janitor` package. This package adds some really useful functions for *cleaning* data.

The one I use the most is called `clean_names()`. This will mutate column names into a standard format - all character values will be lowercase and spaces will be converted to underscores. 

The `smoke_complete` dataset has already had this function applied to it, but let's create a smaller example dataset where this wouldn't be the case.



```r
smoke_bad_colnames <- read_csv("data/smoke_bad_colnames.csv")
```

```
## Parsed with column specification:
## cols(
##   `primary diagnosis` = col_character(),
##   `tumor Stage` = col_character(),
##   `Age at Diagnosis` = col_double(),
##   vitalSTATUS = col_character()
## )
```

```r
smoke_bad_colnames
```

```
## # A tibble: 1,152 x 4
##    `primary diagnosis` `tumor Stage` `Age at Diagnosis` vitalSTATUS
##    <chr>               <chr>                      <dbl> <chr>      
##  1 C34.1               stage ia                   24477 dead       
##  2 C34.1               stage ib                   26615 dead       
##  3 C34.3               stage ib                   28171 dead       
##  4 C34.1               stage ia                   27154 alive      
##  5 C34.1               stage iiia                 23370 alive      
##  6 C34.3               stage ib                   19025 dead       
##  7 C34.3               stage iv                   26938 dead       
##  8 C34.1               stage ib                   28430 dead       
##  9 C34.1               stage iib                  30435 dead       
## 10 C34.9               stage iv                   24019 dead       
## # ... with 1,142 more rows
```

Ack! These column names have spaces, inconsistent capitalization, which make these difficult to work with.

So let's use `clean_names()` to fix this:



```r
smoke_good_colnames <- smoke_bad_colnames %>%
    clean_names()

smoke_good_colnames
```

```
## # A tibble: 1,152 x 4
##    primary_diagnosis tumor_stage age_at_diagnosis vital_status
##    <chr>             <chr>                  <dbl> <chr>       
##  1 C34.1             stage ia               24477 dead        
##  2 C34.1             stage ib               26615 dead        
##  3 C34.3             stage ib               28171 dead        
##  4 C34.1             stage ia               27154 alive       
##  5 C34.1             stage iiia             23370 alive       
##  6 C34.3             stage ib               19025 dead        
##  7 C34.3             stage iv               26938 dead        
##  8 C34.1             stage ib               28430 dead        
##  9 C34.1             stage iib              30435 dead        
## 10 C34.9             stage iv               24019 dead        
## # ... with 1,142 more rows
```

By default, `clean_names()` converts to snake case (spaces between words are replaced by an underscore `_`).  `clean_names()` will help you standardize your dataset's column names.



## Assignment 4

1. Load the `smoke_complete` dataset from the `data` folder.




2. Determine which tissue/organ of origin occurs most frequently in `smoke_complete`. (Hint: use both `group_by()/summarize()` and `arrange()`.)


```r
smoke_complete %>%
```


3.  Create a new dataset called `smoke_filtered` that only contains subjects with `tissue_or_organ_of_origin` of `C34.3`. Use `glimpse()` on `smoke_filtered`.


```r
smoke_filtered  <- smoke_complete %>%
    

glimpse(smoke_filtered)
```

4. For subjects in `smoke_filtered`, make a new variable called `age_years` by converting `age_at_diagnosis` from days to years.


```r
smoke_filtered <- smoke_filtered %>% 
    mutate(age_years = _______ * _____)
```


4. Make a new variable called `age_category` using `case_when()` from `age_years` in `smoke_filtered`. 

Make `age_category` a `factor` with the following levels:

* `0-40` years old
* `40-55` years old
* `55-70` years old
* `70+` years old



```r
smoke_filtered <- 
    smoke_filtered %>% 
    mutate(age_category = 
               case_when(age_years <= 40 ~ "0-40",
                         
                         
                         
                         )
           ) %>%
    mutate(age_category = factor(age_category,
                                 levels = c("0-40",
                                            
                                            
                                            
                                            )
                                 )
           )
```


5. Create a scatterplot using `geom_point()` demonstrating the correlation (if any) between the categorical variable `age_at_diagnosis` versus `cigarettes_per_day`.



```r
ggplot(smoke_complete) +
    aes(x = , y= ) +
    geom_point()
```



