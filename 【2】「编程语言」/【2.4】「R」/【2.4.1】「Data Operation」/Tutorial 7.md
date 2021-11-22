# Load the data

Whether your current working directory(running getwd() in your R console will
return your current working directory) is the project directory or a subdirectory
within the project directory, you can load the data into R using the here()
function from the here package. The here() function is a common way to access files
files in a folder outside of your current working directory.

```{r}
getwd()
```

```{r}
# Load PISA data file (stored in folder named data)
load("/Users/peiyang/Semester 2/FIT5145 Intro data science/Notes/pisa_au.rda")
```


# Data Wrangling and cleaning

  1. Create a variable called science, which represents the student's science
     score based on the average of 10 plausible value(PV) scores in science.
  
  2. Rename the following variables so that they better reflect what they represent:

    * ST094Q01NA* represents the student’s enjoyment of science.
    * SMINS represents the student’s time spent studying science per week.
    * W_FSTUWT represents the student’s weighting, which is an indication of how 
      many other students they represent in Australia, relative to their socioeconomic 
      and demographic characteristics.
    * ST004D01T represents the student’s gender (NA is also a category here but 
      there are no NAs for this variable in this data set). Any ‘missingness’ 
      in this variable is information and should not be removed or corrected.
    * ANXTEST represents the student’s test anxiety level.

  3. Filter out anomalies and missing values, and then convert the variables into
     appropriate classes.

```{r}

library(tidyverse)

# Create data frame of science scores
  # Create science score based on the 10 PV science scores
  # Select and rename ambiguous names
  # Recode gender variable
  # Filter out missing values in science_time
  # Convert science_fun into a factor
  # Filter for science_time greater than 0

pisa_au_science  <- pisa_au %>%
  mutate(science = (PV1SCIE + PV2SCIE + PV3SCIE + PV4SCIE + PV5SCIE + 
    PV6SCIE + PV7SCIE + PV8SCIE + PV9SCIE + PV10SCIE) / 10) %>%
  select(science, science_fun = ST094Q01NA, 
         science_time = SMINS,
         stuweight = W_FSTUWT,
         gender = ST004D01T,
         anxtext = ANXTEST) %>%
  mutate(gender = factor(gender, levels = c(1,2), labels = c("girl", "boy")), exclude = NULL) %>%
  filter(!is.na(science_fun), !is.na(science_time)) %>%
  mutate(science_fun = as.factor(science_fun), 
         science_time = as.numeric(science_time)) %>%
  filter(science_time > 0)
```


# Wrangling challenge
  * Notice that science score was created by taking the average of the 10 PV 
    of science scores:

  * How might we calculate the mean across columns in a smarter way, i.e., 
    without typing out each individual column? The following functions 
    can help us:

    * rowMeans()
    * select_at()
    * vars()
    * starts_with()
    * ends_with()
```{r}
pisa_au %>% 
  mutate(science = rowMeans(select_at(., vars(starts_with("PV") & ends_with("SCIE"))))) %>%
  select(science, everything())

# Notes for tutor

# The best way that I can think of to do this involves the rowMeans() function as well as some select helper functions https://rdrr.io/cran/tidyselect/man/select_helpers.html, which go inside of the select_at() function. It seems like a common operation to perform on the data and should have an obvious elegant solution, but it is actually a little tricky to do.
# Here, what is happening is that we are taking the mean by rows, but we have to use select_at() to specify the variables that we want to be involved in the row-wise mean computation. Inside of select_at(), we're basically telling R that the variables that need to be selected for the row-wise mean computation are those with start with "PV" and ends with "SCIE".
# The final select() function just places the science variable at first column for the data frame.
```




# relationship score & time spend studying

  * A scatter plot of science score agianst time spent studying science per week.
```{r}
pisa_au_science %>%
  ggplot(aes(x=science_time, y=science)) +
  geom_point(alpha=0.5)
```


  * What is the relationship between science score and time spent studying science?
    
  * Is this what you expected?

  * Are there any unusual points in the scatter plot?

  * What do these points do to the graph?

```{r}
# We notice immediately that there are outliers, i.e., some students in the data 
  # spent a large proportion of their time studying science each week. 
  # These outliers stand out in the scatter plot, stretching the x-axis 
  # range to accommodate for these large values. This makes it difficult 
  # to visualise the relationship between a student's student score and 
  # the amount of time they spend studying science each week. 
```


  * Below is a density plot (similar to a histogram in that it provides a
    visualisation of the distribution of a continuous numerical variable, 
    but the y-axis represents the density, not the count) of time spent studying
    science.

```{r}
pisa_au_science %>%
  ggplot(aes(x=science_time)) +
  geom_density()
```


# Log transformation I
  * Applying a log transformation on a numerical variable (note: we cannot 
    take the log of variable that contains zeros or negative values) will 
    scale back any large numbers. The code chunk below shows the effect on 
    the distribution of time spent studying science before and after the 
    log transformation.

```{r}
dens_sci_time <- pisa_au_science %>%
  ggplot(aes(x=science_time)) +
  geom_density() +
  geom_histogram(aes(y= ..density..), alpha = 0.5) +
  labs(title = "Distribution of time spent studying science **before** log transformation")

dens_log_sci_time <- pisa_au_science %>%
  ggplot(aes(x=science_time)) +
  geom_density() +
  geom_histogram(aes(y= ..density..), alpha = 0.5) +
  scale_x_log10() +
  labs(title = "Distribution of time spent studying science **after** log transformation")

gridExtra::grid.arrange(dens_sci_time, dens_log_sci_time, nrow = 2)
  
```


  * What was layered into the ggplot call to apply a log transformation to 
    time spent studying science (the x-axis log-scaled)?
    
  * Describe the difference in the distribution of time spent studying science 
    before and after the log transformation.
    
    Positively skewed to normally distributed
    
  * What should we expect to see when we produce a scatter plot of science 
    score against the log of time spent studying science?
    
    The scatter plot will be more interpretable and a relationship between 
    science score and the log of time spent studying will be clear


  * To produce a scatter plot of science score against the log of time 
    spent studying science.
    
```{r}
pisa_au_science %>% 
  ggplot(aes(x = science_time, y = science)) + 
  geom_point(alpha = 0.1) + 
  scale_x_log10() +
  labs(title = "Relationship between science score and time spent studying it",
       caption = "*x-axis is plotted on a log scale",
       x = "Time spent studying science per week*",
       y = "Science score"
       )
```


# Controlling for science enjoyment
  * By grouping students based on their enjoyment of science (using the facet_wrap() function), 
    you are controlling for the impact that the student’s enjoyment of science 
    has on their science score (as students whose enjoyment of science are the 
    same are grouped together).

    To plot science score against time spent studying science.
    
```{r}
pisa_au_science %>%
  ggplot(aes(x=science_time, y=science, color=science_fun)) +
  geom_point(alpha = 0.1) +
  facet_wrap(~science_fun, ncol=2) +
  scale_color_brewer("Enjoy science", palette = "Dark2") +
  scale_x_log10() +
  theme(legend.position = "bottom") + 
  labs(title = "Relationship between science score and time spent studying it",
       subtitle = "Students are grouped based on how much they enjoy science",
       caption = "*x-axis is plotted on a log scale",
       x = "Time spent studying science per week*",
       y = "Science score")
```


  * Based on the scatter plot of science score against (log) time spent 
    studying science per week, conditional on the enjoyment of science:

    * If you did not apply a log transformation to study time, 
      how would you describe its distribution?
      
      heavily positively skewed
      
    * Based on the log scaled x-axis, how would you describe the 
      relationship between science score and time spent studying?
      
      weak
      
    * Are students who enjoy science more likely to receive higher 
      scores, given they spend an equal amount of time studying science?

      yes
      
```{r}
# There should be a cluster of data points on the left side of the plot as a handful of students spend a lot of time studying.

# For tutor: What the plot looks like if we do not apply a log transformation to science_time

pisa_au_science %>% 
  ggplot(aes(x = science_time, y = science, colour = science_fun)) + 
  geom_point(alpha = 0.1) + 
  facet_wrap(~ science_fun, ncol = 2) + 
  scale_colour_brewer("Enjoy science", palette = "Dark2") +
  # log-scale the x-axis
  #scale_x_log10() +
  theme(legend.position = "bottom") + 
  ggtitle("Relationship between science score and time spent studying it") +
  labs(subtitle = "Students are grouped based on how much they enjoy science") +
  xlab("Time spent studying science per week (mins)") + 
  ylab("Science score")
```


# Modelling science score


# Training the model
  * to train the 2 models of student’s science scores using the 
    pisa_au_science data.

  * In the PISA survey, each student was given a different weighting that 
    depended on their school and other factors. You’ll notice that each 
    student’s weighting is taken into account by specifying the variable 
    stuweight in the weights argument of the lm() function.
    
```{r}
# Fit both models of student's science score
mod1 <- lm(formula = science ~ log10(science_time) + science_fun,
  data = pisa_au_science, weights = stuweight)
mod2 <- lm(formula = science ~ log10(science_time) * science_fun,
  data = pisa_au_science, weights = stuweight)

```


  * Now load the broom package and print out the output of both trained/fitted 
    models using the tidy() and glance() function:
    
```{r}
# Load broom, which contains tidy() and glance()
library(broom)

# Output of first fitted model
tidy(mod1)
glance(mod1)

# Output of second fitted model
tidy(mod2)
glance(mod2)
```



  * Generate the above scatter plot of student’s science score against a 
    measure of their test anxiety level (anxtest), coloured by gender, by 
    appropriately filling out ??? in the code chunk below.

      * How many missing values were omitted from the plot?
        25
      * Does it look like an interaction term between anxiety 
        level and gender could improve the model of students’ 
        science score?
        
        According to this data, girls are more anxious than boys. 
        Therefore, the model will have to account for this differing 
        bias between the genders if it wants to model the overall 
        relationship between the science score vs anxiety level.
        
```{r}
# Plot science score against anxiety level
pisa_au_science %>% 
  ggplot(aes(x = anxtext, y = science, colour = as.factor(gender))) +
  geom_point(alpha = 0.3) +
  geom_smooth(method = "lm") +
  theme(legend.position = "bottom") + 
  ggtitle("Relationship between science score and anxiety level") +
  labs(colour = "Gender") +
  xlab("Anxiety level") + 
  ylab("Science score")
```