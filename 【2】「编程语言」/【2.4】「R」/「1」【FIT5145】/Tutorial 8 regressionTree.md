# Regression trees
It's now your turn to further hone your skills in applying regression trees by using information about
the Melbourne housing market to predict auction prices.

Before you begin, download the housing data from Moodle, then read it directly into your R session by
running the following code.

```{r}
library(tidyverse)
```

```{r}
houses_raw <- read_csv("/Users/peiyang/Documents/GitHub/desktop-tutorial/Melbourne_housing_FULL.csv")
```

# Conduct a preliminary analysis

This data set contains 34857 observations and 21 variables. It's good to conduct a preliminary
analysis to develop a better understanding of property prices and their associated factors.

Among these variables, Landsize, Type, and Distance are of great interest because of their 
impact on price. A brief explanation of the variables:
  * Landsize: the size of the land.
  * Type: a house (h), townhouse (t), and unit (u).
  * Distance: how far the property is from the Melbourne Central Business District (CBD).


# Produce a generalised pair plot

When you're ready, copy and run the following code to produce a generalised
pair plot.
```{r}
library(GGally)
```

```{r}
houses_raw %>%
  select(Price, Landsize, Distance, Type) %>%
  drop_na() %>%
  ggpairs()
```


# What can you learn from the plot?
  * Briefly describe what has been plotted in thediagonal , upper and lower panels.
  * What can you learn from the plot?



# Copy and run

Copy and run the following code chunk, which now focuses on 11 variables instead of 21.

```{r}
houses_full <- houses_raw %>%
  mutate(Suburb = as_factor(Suburb)) %>%
  filter(!is.na(Price)) %>%
  select(Price, Suburb, Rooms, Type, Distance, Bedroom2, Bathroom, 
    Car, Landsize, YearBuilt, Propertycount)
```


# There are missing values in the data!

Interestingly, tree models use only a single variable at a time for any split, which means missing data
becomes a less concerning issue for your growing trees. However, the presence of outliers may influence
the robustness of the tree.
  * Are there any outliers in the data?
  * Why do you think they are outliers?



# Model fits
Copy and run the following code chunk to fit a regression tree (using all explanatory 
variables indicated by .) on house prices. It’s worth noting that all the data 
is used in this first attempt.

```{r}
library(rpart)
houses_rp_fit <- rpart(Price ~ ., data = houses_full)
```

```{r}
houses_rp_fit
```


```{r}
length(names(houses_rp_fit$variable.importance))
```


# Copy and complete
Copy, complete and then run the following code chunk to extract variable.importance, 
which contains information about variable importance from houses_rp_fit 
(the fitted model), and then plot it.

  * What are the 3 most important variables?


```{r}
var_importance <- tibble(
  variable = names(houses_rp_fit$variable.importance),
  importance = houses_rp_fit$variable.importance)

var_importance %>%
  mutate(variable = fct_reorder(variable, importance)) %>%
  ggplot(aes(x=importance, y=variable)) +
  geom_segment(aes(x=0, y=variable, xend= importance, yend=variable)) +
  geom_point(colour = "red")
```

# Classification
You’ve explored categorical response variables and classification trees and found out how you 
can use them to help answer a broader range of problems.

It’s now your turn to build-on your work with the Melbourne housing market to further develop 
your skills and understanding of tree models.

# What you need to do
We could use regression trees to predict housing auction prices in Melbourne, which is a 
continuous variable. However, in this exercise, your task is to determine the location of 
the house.

Since the response variable is a categorical variable, classification trees are the best model 
for you to use.

# About the data
A subset of the data, that includes two suburbs (Carlton and Brighton) and five variables is 
used for this exercise. The popular split criteria for classification trees are Gini and 
Entropy.


# Copy and run
Copy and run the following code chunk in RStudio to find out how the model responds to the 
two criteria.

```{r}
houses_raw <- read_csv("/Users/peiyang/Documents/GitHub/desktop-tutorial/Melbourne_housing_FULL.csv")
```

```{r}
houses_suburb2 <- houses_raw %>%
  select(Suburb, Price, Landsize, Rooms, Type) %>%
  filter(Suburb %in% c("Carlton","Brighton"))

houses_suburb2
```


# Copy and complete
Copy, complete and then run the following code chunk to use Gini to split the tree.

  * Which variable is your response variable?

```{r}
library(rpart)
library(rpart.plot)
rp_fit_gini <- rpart(Suburb ~ ., data = houses_suburb2, parms = list(split = 'gini'))
rp_fit_gini
```


# Identify the number of terminal nodes
Look at the following print output.

  * How many terminal nodes does the model produce?
  * What’s the first split for the tree?

```{r}
printcp(rp_fit_gini)
```

```{r}
rpart.plot(rp_fit_gini)
```


# Identify the number of splits
The previous print output displays the ‘CP’ table for the model fit, which contains information 
about the model’s goodness of fit.

  * How many splits are performed during the model fitting?
  * After two splits, what is the R2? Interpret that R2.


# Copy and complete
The alternative criterion to use is Entropy. Copy, complete and then run the following code chunk. 
List the equations used for these two criteria.

  * Do they produce different trees? If yes, describe the differences.

```{r}
rp_fit_entropy <- rpart(Suburb ~ ., data = houses_suburb2, parms = list(split = "information"))
rpart.plot(rp_fit_entropy)
```

# Compute the confusion table
The other way to examine the goodness of fit is to compute the confusion table: 
the accuracy of the predictions.

Copy, complete and then run the following code chunk.


```{r}
pred_gini <- predict(rp_fit_gini, houses_suburb2, type = "class")
pred_entropy <- predict(rp_fit_entropy, houses_suburb2, type = "class")
table(houses_suburb2$Suburb, pred_gini)
table(houses_suburb2$Suburb, pred_entropy)
```