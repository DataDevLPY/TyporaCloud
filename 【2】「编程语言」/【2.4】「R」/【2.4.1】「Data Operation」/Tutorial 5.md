# Read the data

  * Read agencies.csv and launches.csv data with the rea_csv() function form
    the tidyverse
```{r}
# Load the tidyverse, which contains the read_csv() function to read .csv files in R

library(tidyverse)

agencies <- read_html("https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2019/2019-01-15/agencies.csv")
launches <- read_html("https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2019/2019-01-15/launches.csv")
```

# Look at the agencies and launches

  * The glimpse() function, which comes from the tidyverse, gives us a glimpse
    of each variable in the data.
    
```{r}
glimpse(agencies)

glimpse(launches)
```


  * How many rows/ obseravtions and columns/ variables are in the agencies data?
  * How many in the launches?
```{r}
dim(agencies)
dim(launches)
```

# Conducting a preliminary analysis
  * How many distinct agencies are in the agencies data?
```{r}
distinct(agencies)
```

  * Which variable in agencies uniquely describes each agency?
```{r}
nrow(distinct(agencies))
```
  * Is there an agency that has more than one organisation phase code?
    If so, which agency?
```{r}
agencies %>%
  distinct(agency)
```

  * Why migght agency have more than one organisation phase code?
```{r}
nrow(agencies %>%
       distinct(name))
```


# Complications with real-data: duplicates
  * To count the number of times each agency name is observed in the data:
```{r}
# Take agencies then %>% in the count() function
# Inside of count(), specify the variable whose elements we want to count, i.e., name
```

```{r}
# Method 1:
agencies %>%
  group_by(name) %>%
  count(name) %>%
  arrange(desc(n))
```

```{r}
# Method 2:
agencies %>%
  count(name, sort=TRUE)
```

  * Which space agency appears twice in the data?
  
  * Filter the data for this space agency and explain what has happened
```{r}
agencies %>%
  filter(name == "Sea Launch Limited Partnership")
```


# Joining the data
```{r}
# Number of agencies that provided a space launch
launches %>%
  summarise(num_agencies = n_distinct(agency))
```

```{r}
nrow(launches %>%
distinct(agency))
```


# Left join

  * Using the appropriate connecting variable, join agencies to launches with 
    a left join and assign this data the name launches_agencies.     
    Explain why information about the agency e.g. number of launches, 
    name, location, is missing for some observations.
```{r}
launches_agencies <- left_join(launches, agencies, by = "agency")
```

# inner join

```{r}
# Inner join
launches_agencies <- inner_join(launches, agencies, by = "agency")
# Print launches_agencies
launches_agencies
```


# Analysis of joined data

  * Bar plot 1
```{r}
# Overlapping bar plot of successful and failed launches for each space agency
launches_agencies %>%
  ggplot(aes(x=name, fill=category)) +
  geom_bar(position = position_dodge(width=0.2), alpha=0.5) +
  labs(title = "Launch Outcomes", x = "Space Agency", y = "Frequency") +
  scale_fill_discrete(name="Outcome", labels = c("Failure","Success")) +
  theme(legend.position = "bottom") +
  coord_flip()
```

  * Bar plot 2
```{r}
# Wrangling: Create tidy data frame of count of successful and failed launches
success_fail_count <- launches_agencies %>%
  group_by(short_name, category) %>%
  summarise(n_success_fail = n()) %>%
  ungroup() %>%
  spread(category, n_success_fail) %>%
  rename(Success = O, Fail = `F`) %>%
  mutate(Success = replace_na(Success, 0),
         Fail = replace_na(Fail, 0),
         Total_Launch = Fail + Success) %>%
  mutate(short_name = fct_reorder(short_name, Total_Launch)) %>%
  gather(Outcome, Count, -short_name)
```



```{r}
# Plotting: Overlapping bar plot of successful and failed launches for each space agency
success_fail_count %>%
  filter(Outcome != "Total_Launch") %>%
  ggplot(aes(x = short_name, y = Count, fill = Outcome)) +
  geom_bar(stat = "identity") + 
  labs(title = "Launch Outcomes", x = "Space Agency", y = "Frequency") +
  # colours based on Dark2 colour palette
  scale_fill_manual(values = c("#D95F02", "#1B9E77")) +
  facet_wrap(~ Outcome, scales = "free_x") +
  coord_flip()
```


```{r}
# Variable from agencies: name 
# Variable from launches: category

# Number of successful and failed launches by each agency
launches_agencies %>%
  group_by(name) %>%
  summarise(num_success = sum(category == "O"),
            num_failure = sum(category == "F")) %>%
  ungroup() %>%
  mutate(perc_success = round(100*num_success/(num_success + num_failure), 2))
```


# Labelled plots with text

  * Suppose you are interested in the launch vehicles with the highest number of 
    launches. In RStudio, fill out the following code chunk to plot the launch 
    vehicle frequency from each space agency.

  * Which launch vehicles are in the top five highest number of launches 
    and which space agency do they come from?

```{r}
# Plot of launch vehicle by frequency
launches_agencies %>%
  group_by(short_name, state_code.y, type.x) %>%
  summarise(freq_launchvehicle = n()) %>%
  # Return vehicle with highest launch number
  top_n(1, freq_launchvehicle) %>%
  ungroup() %>% 
  ggplot(aes(x = fct_reorder(short_name, freq_launchvehicle), y = freq_launchvehicle, colour = state_code.y)) +
  geom_text(aes(label = type.x), size = 3) +
  labs(title = "Number of times vehicle was launched", x = "Space Agency", y = "Frequency") +
  scale_colour_discrete(name = "State Responsible", labels = c("Cayman Island", "France", "Japan", "Russia", "USA")) +
  coord_flip()
```