```R
ggpairs()

vis_dat() 

vis_miss()

library(naniar)

miss_var_summary(houses_sub)


impute_mean_at(vars(c(Bathroom, Bedroom2)))


#Run a linear regression model of bedroom on room

br2 <- lm(Bedroom2 ~ Rooms, data = houses_sub_shadow)

#Run a linear regression model of bathroom on room

ba <- lm(Bathroom ~ Rooms, data = houses_sub_shadow)

library(dplyr)

#Impute missing values for bedroom and bathroom based on above regression

houses_sub_shadow_linreg <- houses_sub_shadow %>%
  mutate(Bedroom2 = ifelse(is.na(Bedroom2),predict(br2, new=houses_sub_shadow),Bedroom2),
         Bathroom = ifelse(is.na(Bathroom),predict(ba, new=houses_sub_shadow),Bathroom))
```



## Read data

```{r}
# Load the tidyverse, which contains the read_csv() function to read .csv files in R.
# Reading in Melbourne_housing_FULL.csv:
  # Go to the Melbourne_housing_FULL.csv then click Downnload or View Raw to open the 
    # file (as raw text) in your web browser. The URL on this page will be used 
    # to read the data into R.
  # Copy the URL and paste it inside the read_csv() function.
  # Store the data in an object named houses.

library(tidyverse)

houses <- read_csv("/Users/peiyang/Documents/GitHub/desktop-tutorial/Melbourne_housing_FULL.csv")
```



```R
# Head of houses
head(houses)

# Glimpse of houses
glimpse(houses)
```



      * How many rows and columns are in the houses data?

```R
dim(houses)
```



  * What dose each observation represent?


  * Subset the data to keep only the variables Price, Rooms, Type, Distance,
    Bedroom2 and Bathroom



```R
# Take houses, then pipe in the select() function
# Inside of select() include variables Price, Rooms, Type, Distance, Bedroom2 and 
  # Bathroom. Store data subset in an object named houses_sub.
houses_sub <- houses %>%
  select(Price, Rooms, Type, Distance, Bedroom2, Bathroom)

houses_sub
```



 * The code chunk provide and describe the distribution of the continuous numerical
    variables in the data houses_sub

```R
library(GGally)

# Distribution of continuous numerical variables
houses_sub %>%
  select(Distance, Price) %>%
  ggpairs()
```

![截屏2021-02-05 下午7.52.59](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220052941.png?token=AWS37JNPOTV2DITQFKOCTQLBTJ4ZK)



  * The code provide and describe the bar plots of the discrete numerical
    variables in the data houses_sub.

```R
houses_sub %>%
  select(-Distance, -Price, -Type) %>%
  gather(key = Variable, value = Value) %>%
  ggplot(aes(x=Value)) +
  geom_bar() +
  facet_wrap(~Variable, scales = "free")
```



## Missingness plot

```R
  # Load the visdat package, which contains the functions vis_dat() and vis_miss
    # to produce a bird's eye view of the missingness in the data.
  # Produce missingness plots with the above functions from the visdat package.
library(visdat)

# Map of data colored by variable type and NA
houses_sub %>%
  vis_dat() + 
  ggplot2::theme(legend.position = "bottom")
# Missingness Map
houses_sub %>%
  vis_miss() +
  ggplot2::theme(legend.position = "bottom")
```



![截屏2021-02-05 下午7.55.44](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220052092.png?token=AWS37JNJ25QJJEVETJU5ILLBTJ4ZY)



![截屏2021-02-05 下午7.55.56](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220052356.png?token=AWS37JIFFBGPXXLQTB2ILRTBTJ4Z4)



  * Which variables have missing values?
    Price, Bedroom2, Bedroom
  * Which statement is correct?
      * 11.5% of the observations are missing.



## Missingness summary table

```R
# Load the naniar package, which contains a function to produce summary
  # tables of missing variables
# Use this function to produce missingness summary of the variables in houses_sub
library(naniar)

# Missing Variables summary table
miss_var_summary(houses_sub)
```

  * How many variables contain missing values? Is this same as your previous answer?
    4 variables
  * What proportion of observations are missing on Price?
  * How might you filter the houses_sub data for houses with missing
    information about their distance from the Melbourne CBD? provide 
    details about this house.



```R
houses_sub %>%
  filter(is.na(Distance))
```



## Removing missing values

  * remove the observations with missing house prices bescuase this is the response
    variable that we want to ultimately predict.

```R
# Remove missing house price values
houses_sub <- houses_sub %>%
  filter(!is.na(Price))
```



## Dealing with remaining missing values

1. Bind the shadow matrix of houses_sub to itself and call this data frame houses_sub_shadow
  * Using houses_sub_shadow, produce a scatter plot of Bathroom against
    Bedroom2 colored by missingness on Bedroom2. Why do not missing values
    show up?

```R
library(naniar)
library(visdat)
# Bind the shadow matrix houses_sub
houses_sub_shadow <- houses_sub %>%
  bind_shadow()

# Scatter plot of bath vs. bed colored by missingess in bed
houses_sub_shadow %>%
  ggplot(aes(x=Bathroom,y=Bedroom2,color=Bedroom2_NA)) +
  geom_point(alpha = 0.4) +
  scale_color_brewer("Miss bedroom", palette = "Dark2")

# Missingness map with just bathroom and bedroom
houses_sub_shadow %>%
  select(Bathroom, Bedroom2) %>%
  vis_miss() +
  ggplot2::theme(legend.position = "bottom")

# Missingness map based on missings in bedroom
houses_sub_shadow %>%
  select(Bathroom, Bedroom2) %>%
  filter(is.na(Bedroom2)) %>%
  vis_miss()
```



![截屏2021-02-05 下午8.02.15](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220052149.png?token=AWS37JOWZWESHHMMTWHN3N3BTJ42C)



![截屏2021-02-05 下午8.02.34](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220052048.png?token=AWS37JJPMT4CFIASVBQTXQDBTJ42G)



![截屏2021-02-05 下午8.02.49](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220053616.png?token=AWS37JNGFOWGYLDJJPSXG5DBTJ42Y)



2. Impute the missing values for Bedroom2 and Bathroom, by using mean imputation.

```R
# Impute missing values in bed and bath with their mean
houses_sub_shadow_mean <- houses_sub_shadow %>%
  impute_mean_at(vars(c(Bathroom, Bedroom2)))
```



```R
houses_sub_shadow %>%
  summarise(mean_bed = mean(Bedroom2, na.rm=TRUE),
            mean_bath = mean(Bathroom, na.rm=TRUE))
```

![截屏2021-02-05 下午8.05.19](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220053730.png?token=AWS37JJR77OQWWDH27MK7HTBTJ43E)



```R
houses_sub_shadow %>%
  mutate(Bedroom2 = ifelse(is.na(Bedroom2), mean(Bedroom2, na.rm=TRUE),Bedroom2),
         Bathroom = ifelse(is.na(Bathroom), mean(Bathroom, na.rm=TRUE),Bathroom))
```



3. Produce a scatter plot of the two variables with the imputed values colored.

```R
# Scatter plot of path vs. bed coloured by imputed values
houses_sub_shadow_mean %>%
  ggplot(aes(x=Bathroom, y=Bedroom2, color = Bathroom_NA)) +
  geom_point(alpha=0.4) +
  scale_color_brewer("Missingness",palette = "Dark2")
```

![截屏2021-02-05 下午8.07.57](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220053057.png?token=AWS37JLRK47RMCSCXKXXPV3BTJ43M)

  * Based on the scatter plot, describe whether each statement is true:
    * The number of bedrooms and bathrooms in a house are positively
      correlated, as the number of bathrooms increase, the number of
      bedrooms tend to increase (at a fairly linear/constant way).
    
      TRUE
    
    * All houses in the data without information about the number of bedrooms of their 
      houses are also missing information about the number of bathrooms.
      
      FALSE



```R
# Another hacky way to help us understand this is with the distinct() function
houses_sub_shadow_mean %>%
  filter(Bathroom >1.1, Bathroom <1.9) %>%
  distinct(Bedroom2, Bathroom)
```



* This scatter plot is quick to generate and intended for a specialised audience.
      Many improvements are required to turn this plot into one that can be communicated
      to a boarder audience.



```R
# Plot title, y_axis  title, lengend name, control limit and break of x-axis, perhaps
  # a theme too:)
# scatter plot of bath vs. bed colored by imputed values

houses_sub_shadow_mean %>%
  ggplot(aes(x= Bathroom, y=Bedroom2, color=Bathroom_NA)) +
  geom_point(alpha=0.4) +
  labs(title = "Relationship between no. of bedrooms and bathrooms",
       y = "Bedroom") +
  scale_x_continuous(limits=c(0,9), breaks=seq(0,9,1)) +
  scale_color_brewer(palette="Dark2", name="", labels=c("Not imputed","Imputed")) +
  theme_bw()

```

![截屏2021-02-05 下午8.10.02](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220053428.png?token=AWS37JL2RC7AW3A5JSQXZCDBTJ43W)

4. Impute the missing values for Bedroom2 and Bathroom using a linear regression
    model on the variable Rooms

```R
# Linear regression

# Use houses_sub_shadow and not houses_sub_shadow_mean because houses_sub_shadow_mean
# has imputed missing values in bath and bed with the mean

# Run a linear regression model of bedroom on room
br2 <- lm(Bedroom2 ~ Rooms, data = houses_sub_shadow)

# Run a linear regression model of bathroom on room
ba <- lm(Bathroom ~ Rooms, data = houses_sub_shadow)

br2
ba
```



* Rooms does not represent the total number of rooms in the house but it
  does take on values that are very close to Bedroom2. What do you think is 
  the difference between Rooms and Bedroom2?
* Given the similarity between Rooms and Bedrooms2, suggest another 
  reasonable imputation over the missing values in Bedroom2



5. Produce a scatter plot of the two variables with the imputed values colored.

```R
library(dplyr)
# Impute missing values for bedroom and bathroom based on above regression
houses_sub_shadow_linreg <- houses_sub_shadow %>%
  mutate(Bedroom2 = ifelse(is.na(Bedroom2),predict(br2, new=houses_sub_shadow),Bedroom2),
         Bathroom = ifelse(is.na(Bathroom),predict(ba, new=houses_sub_shadow),Bathroom))
```





```R
# Scatter plot of bath vs. bed coloured by imputed values
houses_sub_shadow_linreg %>%
  ggplot(aes(x = Bathroom, y = Bedroom2, colour = Bathroom_NA)) +
  geom_point(alpha = 0.4) + 
  scale_colour_brewer("Miss bathroom", palette = "Dark2")
```

![截屏2021-02-05 下午8.13.05](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220053914.png?token=AWS37JI4QULRDW675QK7HMLBTJ44E)



```R
houses_sub_shadow_linreg %>%
  ggplot(aes(x=Bathroom, y=Bedroom2, color=Bathroom_NA)) +
  geom_point(alpha=0.4) +
  labs(title = "Relationship between no. of bedrooms and bathrooms",
       y = "Bedroom") +
  scale_x_continuous(limits = c(0,9),breaks = seq(0,9,1)) +
  scale_color_brewer(palette = "Dark2", name="", labels =c("Not imputed","imputed")) +
  theme_bw()
```

![截屏2021-02-05 下午8.12.49](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220053747.png?token=AWS37JLKP4LH3NLH2HL6EDDBTJ44K)