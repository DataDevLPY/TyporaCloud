# Prepare the data
  * Read the hourly pedestrian counts data from 2016 to 2018 for sensors located
    in Melbourne Central, Southbank and Bourke Street Mall(North).

```{r}
library(tidyverse)
library(rwalkr)

  # Load the tidyverse and rwalkr package
  # Using the melb_walk_fast() function from the rwalkr package, read in the
    # pedestrian count data from 2016 to 2018 based on the above sensors
  # Store each data set into an object. The objects should be named
    # ped_melbcent, ped_southbank, ped_bourkest.mallnorth
  # Row bind these three data frames using the bind_rows() function and
    # store this data in an object named ped.melb.south.bourke

## Read in pedestrian data from 2016 to 2018 for 3 locations
ped_melbcent <- melb_walk_fast(year=2016:2018, sensor = "Melbourne Central")
ped_southbank <- melb_walk_fast(year = 2016:2018, sensor = "Southbank")
ped_bourkest.mallnorth <- melb_walk_fast(year = 2016:2018, sensor = "Bourke Street Mall (North)")

# Row bind 3 data frames
ped_melb.south.bourke <- bind_rows(ped_melbcent, ped_southbank, ped_bourkest.mallnorth)  
```


# Look at the data

  * What does each observation represent?
  
  * What does each variable represent?

```{r}
# Look at the data
glimpse(ped_melb.south.bourke)


# * __`Sensor`__ - location of the sensor device
# * __`Date_Time`__ - date and time of the reading
# * __`Date`__ - date (yyyy-mm-dd)
# * __`Time`__ - hour of the day 
# * __`Count`__ - total sensor count of pedestrians

    # It is important that the students understand that this is a temporal/time-series data. 
      # You can explain the difference between a temporal and cross-sectional data by comparing 
      # this data set with the space lauches data used in tutorial 4. 
```

# Create 'time' variables by decomposing the date
```{r}
library(lubridate)
# Create 'Time' variables
  # year - year
  # month - month of the year
  # wday - day of the week
  # day - day of the month
ped_melb.south.bourke <- ped_melb.south.bourke %>%
  mutate(year = year(Date),
         month = month(Date, label = TRUE, abbr = TRUE),
         wday = wday(Date, label = TRUE, abbr = TRUE, week_start = 1),
         day = day(Date))
```



# Exploring time gaps

  * Check for missing values/ time gaps in the data using what you have learned
    about visualising missing values
```{r}
library(naniar)

# Melbourne Central time gaps
ped_melb.south.bourke %>%
  filter(Sensor == "Melbourne Central") %>%
  ggplot(aes(x=Date_Time, y=Count)) +
  geom_miss_point(size = 0.7) +
  facet_wrap(~year, scales = "free_x", nrow = 3) +
  labs(title = "Melbourne Central", y = "Count", x = "Date_Time")
```
```{r}
# Southbank time gaps
ped_melb.south.bourke %>%
  filter(Sensor == "Southbank") %>%
  ggplot(aes(x=Date_Time, y=Count)) +
  geom_miss_point(size=0.7) +
  facet_wrap(~year, scales = "free_x", nrow = 3) +
  labs(title = "Southbank", y = "Count", x = "Date_Time")
```


```{r}
# Bourke Street Mall (North) time gaps
ped_melb.south.bourke %>%
  filter(Sensor == "Bourke Street Mall (North)") %>%
  ggplot(aes(x=Date_Time, y=Count)) +
  geom_miss_point(size = 0.7) +
  facet_wrap(~year, scales = "free_x", nrow = 3) +
  labs(title = "Southbank", y = "Count", x = "Date_Time")
```


  * During which period from 2016 to 2018 do you observe time gaps for each location?

  * Which location contains the least number of time gaps?
  
  * Provide some reasons to explain why you have observed time gaps in this data set.

  * How can time gaps become problematic when analysing the data?


# Distribution of count

  * It is useful to be able to quantitative describe the central tendency 
    of numerical variables in the data. Running the following code chunk 
    will return the mean and median hourly pedestrian counts in Melbourne 
    Central, Southbank and Bourke Street Mall (North).
```{r}
# Table of mean and median pedestrian count

ped_bourkest.mallnorth %>%
  group_by(Sensor) %>%
  summarise( meanCount = mean(Count, na.rm = TRUE),
             medianCount = median(Count, na.rm = TRUE)) %>%
  ungroup()
```


  * Notice that the median is lower than the mean, which indicates that the 
    distribution of hourly pedestrian counts is positively skewed.
    
    
    
```{r}
# Histogram of pedestrian count
ped_melb.south.bourke %>%
  ggplot(aes(x=Count)) +
  geom_histogram() +
  labs(title = "Distribution of hourly pedestrian count", 
       x = "Pedestrians detected",
       y = "Frequency") +
  facet_wrap(~Sensor, scales = "free", nrow = 3)
```


  * Based on the distribution of pedestrian count which statistic would provide 
    a representative measure of central tendency of pedestrian count? Why?

  * Use this measure of central tendency to compute the ‘typical’ pedestrian 
    count for each month and location. Once you have done this, convert 
    the data into a wide form.
    
```{r}
ped_melb.south.bourke %>%
  group_by(Sensor, year) %>%
  summarise(medianCount = median(Count, na.rm=TRUE)) %>%
  ungroup() %>%
  spread(Sensor, medianCount)
```


# Line plot of median hourly pedestrian count

```{r}
# Challenge: Line plots median hourly pedestrian count
ped_melb.south.bourke %>% 
  group_by(Sensor, month) %>%
  summarise(medianCount = median(Count, na.rm=TRUE)) %>%
  ungroup() %>%
  ggplot(aes(x=month, y=medianCount, color = Sensor, group=Sensor)) +
  geom_line() +
  geom_point() +
  labs(title = "Median Hourly Pedestrian Counts, 2016-2018", 
       subtitle = "Generally more pedestrians detected in Southbank across all months.",
       x = "Month", 
       y = "Median Counts")
```

# Box plots of pedestrian counts
  * You can use box plots to help visualise how the distribution of pedestrian 
    counts change from hour to hour.

```{r fig.height = 8, fig.weight = 12}
ped_melb.south.bourke %>%
  ggplot(aes(x = as.factor(Time), y = Count, color = Sensor)) +
  geom_boxplot(alpha = 0.5) +
  facet_wrap(~year, nrow = 3) +
  theme(legend.position = "bottom") + # change the legend position
  labs(title = "Distribution of pedestrian counts at each hour of the day", y = "Pedestrian Counts", x = "Hour of the day")
```


  * In the box plot, the interquartile range (IQR) is the difference between 
    edges of the box, i.e., the 3rd quartile minus the 1st quartile. The larger
    the box, the greater the IQR, and hence the greater the variability of the 
    variable. Explore the box plots of pedestrian counts at Southbank. During 
    which hour of the day is the IQR largest? Explain why this might be the case.

  * During which hours of the day and at what location did the sensor detect 
    the highest pedestrian count?

  * The highest detected pedestrian count is approximately 9,000. Approximately 
    how many times larger is the highest detected pedestrian count to the overall 
    median pedestrian count in this location?

  * Provide an explanation for the high frequency of pedestrian count in 
    Southbank during the later hours of the day.

```{r}
# Answer

# 1. 8am hour - sudden jump because of people going to work + along the yarra river so joggers and bikers may also contribute to jump
# 2. Later hour of the day, Southbank
# 3. 9000/1654
# 4. Single event each year i.e. NYE fireworks
```


# Pedestrian count prior to NYE fireworks
  * A reasonable explanation for the large number of pedestrians detected prior 
    to midnight is that these observations occurred on New Year’s Eve.

  * It would be reasonable to expect the city’s New Year’s Eve festivities, 
    which include entertainment, activities and fireworks, to attract many 
    locals and tourists to the city. Confirm your hypothesis by filling in 
    the code chunk to produce the below line plots of pedestrian count during 
    the days prior to New Year’s Eve. 
```{r}
ped_melb.south.bourke %>%
  filter(month == 12, day %in%(24:31)) %>%
  ggplot(aes(x=Date_Time, y=Count, colour = Sensor)) +
  geom_line(alpha = 0.5) +
  facet_wrap(~ year, scales = "free_x", nrow = 3) +
  theme(legend.position = "bottom") + # change the legend position
  labs(title = "Pedestrian count at each hour of the day leading up to NYE", y = "Pedestrian Count", x = "Hour of the day")

```