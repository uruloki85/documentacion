# R

1. [Working with data frames](#data-frames)
1. [Working with strings](#strings)
1. [Working with lists](#lists)

<a name="data-frames"></a>
## Working with data frames
* Number of rows
  ```r
  nrow(data) # 1920
  ```
* Check columns types:
```r
sapply(data,class)

        city          sex   educ_level     job_type    happiness          age    seniority   sick_leave sick_leave_b   work_hours  Cho_initial    Cho_final 
    "factor"     "factor"     "factor"     "factor"    "numeric"    "integer"    "numeric"    "integer"    "logical"    "numeric"    "numeric"    "numeric" 
```
* Filter rows using different columns in the condition using `filter`
  ```r
  library(dplyr)

  nrow(filter(data, age - seniority < 18)) # 543
  nrow(filter(data, age - seniority >= 18)) # 1377
  ```
* Replace a value in a column using other columns with `mutate`
  ```r
  data$seniority <- mutate(data, seniority = ifelse(age - seniority < 18, age - 18, seniority))
  ```
  
<a name="strings"></a>
## Working with strings
* Convert to upper case each of the words in the string
  ```r
  library(stringr)

  str_to_title(c("a simple test", "Another-test"))
  
  [1] "A Simple Test" "Another-Test" 
  ```

<a name="lists"></a>
## Working with lists
* Length
```r
class(boxplot.stats(data$sick_leave)) # list
length(boxplot.stats(data$sick_leave)$out) # 473
```
