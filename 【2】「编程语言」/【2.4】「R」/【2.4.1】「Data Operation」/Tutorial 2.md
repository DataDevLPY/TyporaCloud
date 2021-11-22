```R
library(tidyverse)
ped_wide <- read_csv("melb_walk_wide.csv")
```

![截屏2021-02-04 下午8.57.30](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220054098.png?token=AWS37JJHRNR5POJOP6BZTDTBTJ464)

```R
ped_wide %>% head(3)
```

![截屏2021-02-04 下午8.58.46](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220054699.png?token=AWS37JNG7XAK2WGIYJDVWKLBTJ47A)



```R
## First and last date in the data
  # Take ped_wide and pipe in the arrange() or select() function
  # Arrange the data by column Date
  # Pipe in the summarise() function and return the first and last date with the first() and last() function

ped_wide %>%
    select(Date) %>%
    summarise(first_Date = first(Date),
              last_Date = last(Date)
             )
```

![截屏2021-02-04 下午9.06.24](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220054451.png?token=AWS37JNH7D6HYHDXMG6XZFLBTJ47O)



**Convert to long term**

  * It is helpful to think of a data set as either wide or long.
    reshaping the data
  * Why should we reshape the data into a long form?
    A data set that is represented in a long form is considered a tidy data set and
    allows us to use all the tools from the tidyverse.
    
    The tools created in the tidybverse are designed for us to work in a principled
    and consistent way but they require that the data be represented the tidy way (long term).
    
    dplyr functions is to wrangle the data.
    ggplot2 package is to produce graphics (both part of the tidyverse)

```R
ped <- ped_wide %>%
    gather(key=Sensor, value=Count, -Date_Time, -Date, -Time) %>%
    select(Sensor, everything(), Count)
ped %>% head(3)
```

![截屏2021-02-04 下午9.13.52](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220054183.png?token=AWS37JP5MU4GWN7XC3MM6K3BTJ47W)



* We will explore pedestrian activity around the State Library on the 1st of Jan, 2019.
    To do this, we will need to filter ped for the State Library sensor on the 1st of Jan,
    2019.

```R
## Filter for State Library data on Jan 1, 2019
  # Take ped and pipe in the filter() function
  # Filter Date to "2019-01-01" and Sensor to "State Library"
  # Store this filtered Data in an object named state_lib_jan_one

state_lib_jan_one <- ped %>%
  filter(Date == "2019-01-01", Sensor == "State Library")

state_lib_jan_one
```

![截屏2021-02-04 下午9.18.44](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220054673.png?token=AWS37JI5JWARWTQZ3GAQWFLBTJ476)



 * How many rows and columns are in state_lib_jan_one?
   
    24,5

```R
dim(state_lib_jan_one)
```



* Explain why there are this many rows. (THis may seem obvious, but if you develop
  a checking mechanism like this, you'll be able to spot data quality or coding 
  issues much sooner, which can save you a lot of time.)
* In which hour is pedestrian count highest? Explain whether or not this makes sense.

```R
state_lib_jan_one[order(state_lib_jan_one$Count, decreasing = TRUE),]
```

```R
state_lib_jan_one %>% 
    select(Count) %>%
    max()
```



**Line plot**

  * Line plots are typically used to visulise time-series data sets, with the x-axis
    representing the time or date (or both) and the y-axis representing some time-series
    process.

```R
## Line plot of State Library pedestrian count
  # Take state_lib_jan_one and pipe in the ggplot() function
  # Specify the aesthetics layer, what should be placed in the x and y-axis.
    # This goes inside the aes(), which goes inside of ggplot().
  # Add the geometric (or geom) layer to tell R that the visual element we
    # need for our plot is the line.

state_lib_jan_one %>%
  ggplot(aes(x=Time, y=Count)) +
  geom_line()
```

![截屏2021-02-04 下午9.31.04](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220054890.png?token=AWS37JJTHHYPKGTRJFITFI3BTJ5AK)



## Bar plot

```R
# Similar to line plot
state_lib_jan_one %>%
  ggplot(aes(x=Time, y=Count)) +
  geom_bar(stat = "identity")
```

![截屏2021-02-04 下午9.30.50](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220054542.png?token=AWS37JNCTXTSFCGHW4GGMRDBTJ5AQ)



## Side-by-side box plot

```R
state_lib <- ped %>%
  filter(Sensor == "State Library")

## Side-by-side box plot of pedestrian count for each hour of the day
  # Take state_lib and pipe in the ggplot() function
  # Add the aesthetic layer, which should have Time, Count and Time
    # specified in the x,y and group arguement inside of aes(). note that aes()
    # goes inside of ggplot()
  # Add the geom layer to tell R that the visual element we need for our plot
    # is the boxplot.

state_lib %>%
  ggplot(aes(x=Time, y=Count, group = Time)) +
  geom_boxplot()
```

![截屏2021-02-04 下午9.31.18](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220054732.png?token=AWS37JNNFHM2KA6JPYEGP7TBTJ5AY)



* Note that the group aesthetic will group the data by each hour of the day(Time).
    Then create a box plot for each of these groups. Without the group aesthetic, 
    ggplot will produce a single box plot of pedestrain count and use the Time variable 
    as the width of the boxplot

```
state_lib %>%
  ggplot(aes(x=Time, y=Count)) +
  geom_boxplot()
```

![截屏2021-02-04 下午9.31.44](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220054198.png?token=AWS37JMHCCGN2GKWCFZTDU3BTJ5BI)









```R
# Filter for multiple sensors
mc_sl <- ped %>%
    filter(Sensor %in% c("Melbourne Central", "Sate Library"))
  # filter(Sensor == "Melbourne Central" | Sensor == "State Library")
mc_sl %>% head(3)
```

![截屏2021-02-04 下午9.34.58](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220054966.png?token=AWS37JOHSHHQGDWRHEEYLQTBTJ5BY)

```R
# How many rows and columns are in the data mc_sl?
dim(mc_sl)
```

  * Explain why there are this many rows in mc_sl
  * How would you filter for all sensors except Melbourne Central and State
    Library?

```R
other_sensor <- ped %>%
  filter(!(Sensor %in% c("Melbourne Central", "State Library")))
```



## Facetted side-by-side box plots

  * To divide a plot into the different categories/ measurements of a column
    in the data, we simply add the facet_wrap() layer onto our ggplot()
    call.

```R
## Side-by-side box plot of pedestrian count for each hour of the day facetted by Sensor
  # Take mc_sl and pipe in the ggplot() function
  # Add the aesthetic layer, which should have Time, Count and Time
    # Specified in the x,y and group argument inside of aes(). Hote that
    # aes() goes inside of ggplot().
  # Add the geom layer to tell R that the visual element we need for our plot
    # is the boxplot.
  # Add the facet_wrap() layer to split the plot by Sensor

mc_sl %>%
  filter(Count < 5000) %>%
  ggplot(aes(x=Time,y=Count,group=Time)) +
  geom_boxplot() +
  facet_wrap(~ Sensor)
```

![截屏2021-02-04 下午9.42.33](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220055526.png?token=AWS37JMA4QRYDQRKDBNC36LBTJ5CE)



--------------

-----------

## Using summarise()
  * USe a wrangling verb, to count the number of sensors in the ped.
    Do all the sensors have the same number of measurements?

```R
ped %>%
  summarise(num_sensors = n_distinct(Sensor))
```



## Grouping the data

  * For each sensor, compute the total count for Jan.
    Which sensor had the largest count?
    Which sensor had the samllest count?

```R
ped %>%
  group_by(Sensor) %>%
  summarise(sum=sum(Count, na.rm = TRUE)) %>%
  ungroup() %>%
  arrange(desc(sum))
```



## Sum of missing values with sum(is.na())

  * For each sensor, compute the total number of missing counts.
    Which snesor had the most missing counts?
    Why might this be?

```R
ped %>%
  group_by(Sensor) %>%
  summarise(total_missing=sum(is.na(Count))) %>%
  ungroup() %>%
  arrange(desc(total_missing))
```



## Filtering multiple sensors and reshaping the data

  * Filter ped to contain the counts from Melbourne Central and State Library
    Sensors only, then use a tidying function to create two columns that contain 
    their counts. 

```R
ped %>%
  filter(Sensor %in% c("Melbourne Central", "State Library")) %>%
  spread(Sensor, Count)
```



## Producing a 100 per cent chart

  * Create the following 100 per cent chart to compare the foot traffic at Melbourne
    Central and the State Library during different hours of the day. We can change
    the dimensions of our plot by changing the code chunk option.

```R
ped %>%
  filter(Sensor %in% c("Melbourne Central", "State Library")) %>%
  ggplot(aes(x=Time, y=Count, fill=Sensor)) +
  geom_bar(stat="identity", position = "fill") +
  facet_wrap(~Date, ncol = 7) +
  labs(title = "Comparing foor traffic at Melbourne Central and the State Library during different hours of the day",
       subtitle = "Greater proportion of foot traffic at the State Library than Melbourne Central during the afternoon")
```

![截屏2021-02-05 下午1.32.11](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220055975.png?token=AWS37JOLQZBIVA7AE7L574TBTJ5CS)

