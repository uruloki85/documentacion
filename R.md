# R

## Working with data frames
* Total number of rows
  ```r
  nrow(data) # 1920
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

## Working with strings
* Convert to upper case each of the words in the string
  ```r
  library(stringr)

  str_to_title(c("a simple test", "Another-test"))
  ```
  [1] "A Simple Test" "Another-Test" 
