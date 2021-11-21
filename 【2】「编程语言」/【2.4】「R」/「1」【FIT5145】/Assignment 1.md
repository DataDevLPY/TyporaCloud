---
title: "Peiyang_29894778_Assessment#AB"
author: "Peiyang"
date: "9/7/2020"
output: html_document
---
```
FIT5145_S2_2020_A1_Data1_IndigAusPopData_byregion.csv
FIT5145_S2_2020_A1_Data2_IndigAusPopData_bystate.csv
FIT5145_S2_2020_A1_Data3_AusImmunisationData.csv
```
```{r}
library(dplyr)

library(formattable)

library(ggplot2)

library(tidyverse)

library(googleVis)
```



# Task A

## A1. Investigating the Distribution of Indigenous Australians

1. Use R to read, wrangle and analyse the data in Data1. Make sure you describe any
complications you encounter and the steps you take when answering the following questions.
```{r}
## Read data
Indigenous_regions_file <- read.csv("FIT5145_S2_2020_A1_Data1_IndigAusPopData_byregion.csv")
#head(Indigenous_regions_file)
```

    * a. What regions have the maximum and minimum total Indigenous populations in 2016 and 
    2031?
```{r}
## To check the regions
Indigenous_region_factor = factor(Indigenous_regions_file$Indigenous.region)
#Indigenous_region_factor

## To select and filter the data
data_2016 <- select(Indigenous_regions_file, Indigenous.region, TIME, Value) %>%
  filter(TIME == 2016) %>%
  select(Indigenous.region, Value)
data_2031 <- select(Indigenous_regions_file, Indigenous.region, TIME, Value) %>%
  filter(TIME == 2031) %>%
  select(Indigenous.region, Value)
#data_2016
#data_2031


## Group by the regions to sum all of values
groupBy_2016 <- data_2016 %>%
  group_by(Indigenous.region) %>%
  summarize_all(sum)
#groupBy_2016
groupBy_2031 <- data_2031 %>%
  group_by(Indigenous.region) %>%
  summarise_all(sum)
#groupBy_2031


## To sort and get the max and min
order_2016 <- groupBy_2016[order(groupBy_2016$Value, decreasing = TRUE),]
max_2016_value <- order_2016$Value[1]
max_2016_region <- order_2016$Indigenous.region[1]
cat('In 2016, the maximum region is', max_2016_region, 'and the value is', max_2016_value,'\n')
min_2016_value <- order_2016$Value[length(order_2016$Value)]
min_2016_region <- order_2016$Indigenous.region[length(order_2016$Indigenous.region)]
cat('In 2016, the minimun region is', min_2016_region,' and the value is',min_2016_value,'\n')

order_2031 <- groupBy_2031[order(groupBy_2031$Value, decreasing = TRUE),]
max_2031_value <- order_2031$Value[1]
max_2031_region <- order_2031$Indigenous.region[1]
cat('In 2031, the maximum region is', max_2031_region, 'and the value is', max_2031_value,'\n')
min_2031_value <- order_2031$Value[1]
min_2031_region <- order_2031$Indigenous.region[1]
cat('In 2016, the minimun region is', min_2031_region,' and the value is',min_2031_value,'\n')


```


    * b. What region/s have the maximum and minimum growth or decay rates of their total 
    Indigenous population between 2016 and 2031?
        Calculate these rates as the percentage difference between the 2016 and 2031,
        e.g., if 2031 population = 5500 & 2016 population = 5000,
        then rate = (5500 – 5000) / 5000 = 500/5000 = 0.1, so 10% growth



```{r}
## Create a new dataframe to show
difference <- groupBy_2031
## Use columns to compute the percentage
difference$Value <- (groupBy_2031$Value - groupBy_2016$Value) / groupBy_2016$Value
#difference

## To order the data by Value
order_difference <- difference[order(difference$Value, decreasing = TRUE),]
#order_difference
max_difference_value <- order_difference$Value[1]
max_difference_region <- order_difference$Indigenous.region[1]
cat('The maximum growth rates of their total Indigenous population between 2016 and 2031
    is in', max_difference_region,', which rate is', max_difference_value,'\n')

min_difference_value <- order_difference$Value[length(order_difference$Value)]
min_difference_region <- order_difference$Indigenous.region[length(order_difference$Value)]
cat('The minimum growth rates of their total Indigenous population between 2016 and 2031
    is in', min_difference_region,', which rate is', min_difference_value,'\n')

```



    c. Plot and describe the growth or decay of the total Indigenous populations for the capitals of 
    the 7 state/territories across all time periods.
    For these calculations, you will need to work out the growth/decay rates for each time period,
    where the total population of the capital in time period N is compared to that in time period
    N+1.
        e.g., if 2017 population = 5050 and 2016 population = 5000,
        then rate = (5050 - 5000) / 5000 = 50/5000 = 0.01, so 1% growth for 2016-2017


```{r fig.height=3, fig.width=5}
## To filter the capital of each state
capital_state <- Indigenous_regions_file %>%
  filter(Indigenous.region == 'ACT' |
           Indigenous.region == 'Adelaide' |
           Indigenous.region == 'Perth' |
           Indigenous.region == 'Darwin' |
           Indigenous.region == 'Brisbane' |
           Indigenous.region == 'Melbourne' |
           Indigenous.region == 'Sydney - Wollongong' |
           Indigenous.region == 'Tasmania') %>%
  select(Indigenous.region, Age, TIME, Value)
#capital_state

## Group by state and year to get the value in every year for each state
groupBy_capital_year <- capital_state %>%
  select(-Age) %>%
  group_by(Indigenous.region, TIME) %>%
  summarise_all(sum)
#groupBy_capital_year

capital_year_lag <- groupBy_capital_year %>%
  mutate(diff = (Value - lag(Value, default = first(Value))) / first(Value))
#capital_year_lag

## To drop the first row
capital_year_lag <- filter(capital_year_lag, diff != 0)

## To show the plot
capital_year_lag %>%
  ggplot(aes(x = TIME, y = diff, group = Indigenous.region)) +
  geom_line(aes(color = Indigenous.region)) +                   
  scale_x_continuous(breaks = seq(2016, 2031, 5))

```


## A2. Investigating the Ages of Indigenous Australians

### 1. Using Data1, which region has the highest percentage of children in its total 2016 population?

    For this, calculate this as a percentage of the total population for a region. The ABS
    commonly considers children to be under 15 years of age.


```{r}

## To filter all of children in 2016 and select Indigenous.region, TIME, Value
region_2016_child <- filter(Indigenous_regions_file, TIME == 2016 & (Age == 'Oct-14' |
                                                                       Age == '5-Sep' |
                                                                       Age == '0 - 4')) %>%
  select(Indigenous.region, Age, TIME, Value)
#head(region_2016_child)
#nrow(region_2016_child)

## To group by regions get the sum value
groupby_region_2016_child <- region_2016_child %>%
  select(-Age,-TIME) %>%
  group_by(Indigenous.region) %>%
  summarize_all(sum)
#nrow(groupby_region_2016_child)


## To filter all of person in 2016 and select Indigenous.region, TIME, Value
region_2016_total <- filter(Indigenous_regions_file, TIME == 2016) %>%
  select(Indigenous.region, Age, TIME, Value)

groupby_region_2016_total <- region_2016_total %>%
  select(-Age, -TIME) %>%
  group_by(Indigenous.region) %>%
  summarise_all(sum)
#nrow(groupby_regions_2016_total)

## To group by regions get the sum value
child_percent_2016 <- groupby_region_2016_child %>%
  select(- Value)
child_percent_2016$Percent <- percent(groupby_region_2016_child$Value / groupby_region_2016_total$Value)
#nrow(child_percent_2016)
#child_percent_2016


## To order and get result
order_percent <- child_percent_2016[order(child_percent_2016$Percent, decreasing = TRUE),]
#nrow(order_percent)
highest_region_2016 <- order_percent$Indigenous.region[1]
highest_percent_2016 <- order_percent$Percent[1]

cat(highest_region_2016,'has the highest percentage of children in its total 2016 population,\
    which is',highest_percent_2016,'\n')


```


### 2. Data2 includes estimated populations measured for the years 2006-2016 and projected
    estimates predicted for the years 2016-2031. Data1 just uses projected estimates. Using
    Data2 only, calculate and discuss which state or territory has the highest percentage of
    children in its total 2006, 2016 and 2031 populations.

```{r}
Indigenous_state_file <- read_csv("FIT5145_S2_2020_A1_Data2_IndigAusPopData_bystate.csv")
#head(Indigenous_state_file)

## Pre-processing -- to gather columns "Year"
Indigenous_state_file <- gather(Indigenous_state_file, key = "Year", value = "Value", -Age, -State)
#head(Indigenous_state_file)
#nrow(Indigenous_state_file)

## To change the typeof
Indigenous_state_file$Value <- as.integer(Indigenous_state_file$Value)
Indigenous_state_file$Year <- as.integer(Indigenous_state_file$Year)
#typeof(Indigenous_state_file$Value)
#head(Indigenous_state_file)
```


```{r}
#levels(as.factor(Indigenous_state_file$Age))
## To change column type
Indigenous_state_file$Age <- as.integer(Indigenous_state_file$Age)

## Compute total population and the child amount  in 2006
total_2006 <- Indigenous_state_file %>%
  filter(Year == 2006)
child_2006 <- total_2006 %>%
  filter(Age < 15)
#total_2006
#child_2006

groupby_state_total_2006 <- total_2006 %>%
  select(-Age,-Year) %>%
  group_by(State) %>%
  summarise_all(sum)

groupby_state_child_2006 <- child_2006 %>%
  select(-Age,-Year) %>%
  group_by(State) %>%
  summarise_all(sum)
#nrow(groupby_state_total_2006)
#nrow(groupby_state_child_2006)

child_2006_percent <- groupby_state_child_2006 %>%
  select(-Value)
child_2006_percent$Percent <- percent(groupby_state_child_2006$Value / groupby_state_total_2006$Value)
#child_2006_percent

order_2006_child_state <- child_2006_percent[order(child_2006_percent$Percent, decreasing = TRUE),]
highest_2006_state <- order_2006_child_state$State[1]
highest_2006_percent <- order_2006_child_state$Percent[1]

cat(highest_2006_state,"has the highest percentage of children in its total 2006 population, \
    which is",highest_2006_percent,'\n')




## Compute total population and the child amount  in 2016
total_2016 <- Indigenous_state_file %>%
  filter(Year == 2016)
child_2016 <- total_2016 %>%
  filter(Age < 15)
#total_2016
#child_2016

groupby_state_total_2016 <- total_2016 %>%
  select(-Age,-Year) %>%
  group_by(State) %>%
  summarise_all(sum)

groupby_state_child_2016 <- child_2016 %>%
  select(-Age,-Year) %>%
  group_by(State) %>%
  summarise_all(sum)
#nrow(groupby_state_total_2016)
#nrow(groupby_state_child_2016)

child_2016_percent <- groupby_state_child_2016 %>%
  select(-Value)
child_2016_percent$Percent <- percent(groupby_state_child_2016$Value / groupby_state_total_2016$Value)
#child_2006_percent

order_2016_child_state <- child_2016_percent[order(child_2016_percent$Percent, decreasing = TRUE),]
highest_2016_state <- order_2016_child_state$State[1]
highest_2016_percent <- order_2016_child_state$Percent[1]

cat(highest_2016_state,"has the highest percentage of children in its total 2016 population, \
    which is",highest_2016_percent,'\n')




## Compute total population and the child amount  in 2031
total_2031 <- Indigenous_state_file %>%
  filter(Year == 2031)
child_2031 <- total_2031 %>%
  filter(Age < 15)
#total_2031
#child_2031

groupby_state_total_2031 <- total_2031 %>%
  select(-Age,-Year) %>%
  group_by(State) %>%
  summarise_all(sum)

groupby_state_child_2031 <- child_2031 %>%
  select(-Age,-Year) %>%
  group_by(State) %>%
  summarise_all(sum)
#nrow(groupby_state_total_2031)
#nrow(groupby_state_child_2031)

child_2031_percent <- groupby_state_child_2031 %>%
  select(-Value)
child_2031_percent$Percent <- percent(groupby_state_child_2031$Value / groupby_state_total_2031$Value)
#child_2006_percent

order_2031_child_state <- child_2031_percent[order(child_2031_percent$Percent, decreasing = TRUE),]
highest_2031_state <- order_2031_child_state$State[1]
highest_2031_percent <- order_2031_child_state$Percent[1]

cat(highest_2031_state,"has the highest percentage of children in its total 2031 population, \
    which is",highest_2031_percent,'\n')


```



### 3. Use R to build a Motion Chart comparing the total Indigenous Australian population of each
    region to the percentage of Indigenous Australian children in each state/territory. Use the
    region populations calculated from Data1 and the child percentage values calculated
    from Data2. The motion chart should show the population on the x-axis, the percentage on
    the y-axis, the bubble size should depend on the population.


```{r}
#head(Indigenous_regions_file)
#head(Indigenous_state_file)

## Pre-processing

## To find the data about region name and state name
##https://www.abs.gov.au/AUSSTATS/abs@.nsf/DetailsPage/1270.0.55.002July%202016?OpenDocument
## The first file"Indigenous_Allocations.csv"
## I take it upload in github
Indigenous_allocation <- read_csv("https://raw.githubusercontent.com/Peiyang-Felix/Peiyang/master/Indigenous_Allocations.csv")
#colnames(Indigenous_allocation)

## To create a dataframe which only about region and state
location <- Indigenous_allocation %>%
  select(region = `IREG_NAME_2016`,State = `STATE_NAME_2016`)


location <- distinct(location)
## To check the name of region and state
#location
#levels(as.factor(location$region))
#cat('=========\n')
#levels(as.factor(location$state))
#cat('=========\n')
#levels(as.factor(Indigenous_regions_file$Indigenous.region))
#cat('=========\n')
#levels(as.factor(Indigenous_state_file$State))

## To change the name
location$State[location$State == 'Australian Capital Territory'] <- "ACT"
location$State[location$State == 'New South Wales'] <- "NSW"
location$State[location$State == 'Northern Territory'] <- "NT"
location$State[location$State == 'Queensland'] <- "QLD"
location$State[location$State == 'South Australia'] <- "SA"
location$State[location$State == 'Tasmania'] <- "Tas"
location$State[location$State == 'Victoria'] <- "Vic"
location$State[location$State == 'Western Australia'] <- "WA"
#location
```

```{r}
region_value <- Indigenous_regions_file %>%
  select(region = Indigenous.region,time = TIME,Value)

## To sum all of values in the same year and region 
region_value <- region_value %>%
  group_by(region, time) %>%
  summarise_all(sum)
#region_value

## To merge two dataframe
region_state_merge <- merge(region_value,location, on = 'region' )
#region_state_merge
```


```{r}
## Compute child amount
child_state <- Indigenous_state_file %>%
  filter(Age < 15) %>%
  select(State, time = Year, Value)

child_groupby_state <- child_state %>%
  group_by(State,time) %>%
  summarise_all(sum)
#child_groupby_state

## To compute the total population in each state
total_state <- Indigenous_state_file %>%
  select(State, time = Year, Value)

total_groupby_state <- total_state %>%
  group_by(State, time) %>%
  summarise_all(sum)
#total_groupby_state

##To compute the child rate
total_groupby_state$child_rate <- child_groupby_state$Value / total_groupby_state$Value
total_groupby_state <- total_groupby_state %>%
  select(-Value)
#total_groupby_state
```


```{r}
## To merge
region_state_data <- merge(total_groupby_state, region_state_merge, by = c("State", "time"))
region_state_data

motion_chart <- region_state_data %>%
  gvisMotionChart(xvar = 'Value', yvar = 'child_rate', colorvar = 'State',
                  sizevar = 'Value', timevar = 'time', idvar = 'region') %>%
  plot()
```

### 4. Using the Motion Chart, answer the following questions, supporting your answers with
    relevant R code and/or Motion Charts


​    
    a. Which region’s population overtakes that of another region in the same state/territory?
    In which year/s does this happen?

```{r}
cat("=========\n")
cat('Anwser:\n')
cat('From obversing motion chart, Apatula’s population overtakes that of another region in the same state/territory.\n')
```


​      
    b. Is there generally a relationship between the Indigenous Australian population size and
    percentage of children in the population? If so, what kind of relationship? Explain
    your answer.

```{r}
cat("=========\n")
cat('Anwser:\n')
cat('From 2016-2031, the total population has increased, but the child rate has a reverse trend, decreasing over years.')
```



    c. Colour is commonly used in data visualisation to help understand data. Which aspect of
    this data would you use colour for in your plot and why?

```{r}
cat("=========\n")
cat('Anwser:\n')
cat('I use state as color, because color do not have lots of values and it can show different class.')
```



    d. Are there any other interesting things you notice in the data or any changes you would
    recommend for the Motion Chart?

```{r}
cat("=========\n")
cat('Anwser:\n')
cat('From log plot, I can clearly find the trajectory of change for the value.')
```


##============================================================================================
# Task B

```{r}
library(dplyr)

library(formattable)

library(ggplot2)

library(tidyverse)

library(googleVis)
```

```{r}
## Read data1
Indigenous_regions_file <- read.csv("FIT5145_S2_2020_A1_Data1_IndigAusPopData_byregion.csv")
#head(Indigenous_regions_file)
```

```{r}
##Read data2
Indigenous_state_file <- read_csv("FIT5145_S2_2020_A1_Data2_IndigAusPopData_bystate.csv")
#head(Indigenous_state_file)

## Pre-processing -- to gather columns "Year"
Indigenous_state_file <- gather(Indigenous_state_file, key = "Year", value = "Value", -Age, -State)
#head(Indigenous_state_file)
#nrow(Indigenous_state_file)

## To change the typeof
Indigenous_state_file$Value <- as.integer(Indigenous_state_file$Value)
Indigenous_state_file$Year <- as.integer(Indigenous_state_file$Year)
#typeof(Indigenous_state_file$Value)
#head(Indigenous_state_file)
```

## B1. Values and Variables

```{r}
aus_data <- read_csv("FIT5145_S2_2020_A1_Data3_AusImmunisationData.csv")
```

### 1. How many PHN areas does the data cover?

```{r}
## To use factor
#colnames(aus_data)
amount_PHN <- nlevels(as.factor(aus_data$`PHN area name`))
#amount_PHN
cat("There are", amount_PHN,"PHN areas.")
```



### 2. What are the possible values for 'PHN code'?

```{r}
PHN_code_value <- levels(as.factor(aus_data$`PHN code`))
PHN_code_value
```



### 3. For each row, calculate the percentage of Australian children that are fully immunised (this is
    the immunisation rate). What are the average, maximum and minimum immunisation rates?
    
    Calculate the same for the group that are Indigenous Australian children. Do all of those
    values seem statistically reasonable to you?


```{r}
## To create a column to show the percentage
immuised_data <- aus_data
immuised_data$immunised_percent <- immuised_data$`Number fully immunised` / immuised_data$`Number of registered children`

cat('The maximum immunisation rates for Australian children is',max(immuised_data$immunised_percent),'\n')
cat('The minimum immunisation rates for Australian children is',min(immuised_data$immunised_percent),'\n')
cat('The average immunisation rates for Australian children is',mean(immuised_data$immunised_percent),'\n')
cat('========================\n')



## NP: Not available for publication as the area has fewer than 26 registered children,
##     or the number of children not fully immunised is between one and five inclusive.
## To delete NP rows
immuised_indigenous_data <- aus_data %>%
  filter(aus_data$`Number IndigAus fully immunised` != 'NP')

## To replace ',', ''
immuised_indigenous_data$`Number IndigAus fully immunised` <- gsub(',','',immuised_indigenous_data$`Number IndigAus fully immunised`)
## To change the type
immuised_indigenous_data$`Number IndigAus fully immunised` <- as.numeric(immuised_indigenous_data$`Number IndigAus fully immunised`)
  

## To replace ',', '' 
immuised_indigenous_data$`Number of registered IndigAus  children` <- gsub(',','',immuised_indigenous_data$`Number of registered IndigAus  children`)
## To change the type
immuised_indigenous_data$`Number of registered IndigAus  children` <- as.numeric(immuised_indigenous_data$`Number of registered IndigAus  children`)


## To create a column to show the percentage
immuised_indigenous_data$immunised_indigenous_percent <- immuised_indigenous_data$`Number IndigAus fully immunised`/ immuised_indigenous_data$`Number of registered IndigAus  children`
cat('The maximum immunisation rates for Indigenous Australian children is',max(immuised_indigenous_data$immunised_indigenous_percent),'\n')
cat('The minimum immunisation rates for Indigenous Australian children is',min(immuised_indigenous_data$immunised_indigenous_percent),'\n')
cat('The average immunisation rates for Indigenous Australian children is',mean(immuised_indigenous_data$immunised_indigenous_percent),'\n')
cat('============================\n')
cat('Answer:\n')
cat('The percentage of Australian children that are fully immunised is higher than the same \
for the group that are Indigenous Australian children.\n')

```


## B2. Variation in rates over Time, Age and Location

    Generate boxplots (or other plots) of the immunisation rates versus year and age to
    answer the following questions:


### 1. Have the immunisation rates improved over time? Are the median immunisation rates
    increasing, decreasing or staying the same?

```{r}
immu_rate_year <- immuised_data %>%
  ggplot(aes(x=`Reporting Year`, y=`immunised_percent`)) +
  geom_boxplot()

immu_rate_year
cat('=============\n')
cat('Answer:\n')
cat('The immunisation rates improved over time. But the median immunisation rates has\
decreased before "2014-15", and then continued to increased.')
```



### 2. How do the immunisation rates vary with the age of the child?

```{r}
#colnames(immuised_data)
immu_rate_age <- immuised_data %>%
  ggplot(aes(x=`Age group`, y=`immunised_percent`)) +
  geom_boxplot()

immu_rate_age
cat('=============\n')
cat('Answer:\n')
cat('The immunisation rates has decreased before "2 years", and then continued to increased.\
The median immunisation rates has the similar tendency.')
```


### 3. What is the median rate per state/territory?

```{r}
## To group by the state
median_rate_by_state <- immuised_data %>%
  group_by(State) %>%
  summarise(median_rate = median(`immunised_percent`))

## To round the median
median_rate_by_state$median_rate <- round(median_rate_by_state$median_rate,4)

immuised_data %>%
  ggplot(aes(x=`State`, y=`immunised_percent`)) +
  geom_boxplot() +
  geom_text(data=median_rate_by_state, aes(State, median_rate, label=median_rate),
            position=position_dodge(width = 0.6), size = 3, vjust = 1.1)

```



### 4. Which states or territories seem most consistent in their immunisation rates?


```{r}
cat('=============\n')
cat('Answer:\n')
cat('From the boxplot, we can know ACT has most consistent in their immunisation rates.')
```