## Cricket data

  * Cricketdata is an R package from rOpenSci, which contains data on all international
    cricket matches is provided by ESPNCricinfo. We will install the cricketdata
    package via GitHub
  
  * By using the double colon operator :: , we do not have to load the devtool
    package to use the install_github() function.



```R
# install.packages("devtools")
  ## Use install_github() function from devtools package to install cricketdata via GitHub
devtools::install_github("ropenscilabs/cricketdata")
```



## Men's T20 batting

```R
  # Load tidyverse and cricketdata package
  # Run fetch_cricinfo("T20","Men","Batting") to extract the T20 batting data and
    # store this in an object named mt20
  # Filter the data for you two favorite countries. Here we have chosen Australia
    # and India.

library(tidyverse)
library(cricketdata)

mt20 <- fetch_cricinfo("T20","Men","Batting")

mt20_aus_ind <- mt20 %>%
  filter(Country %in% c("India","Australia"))
```

* How many rows and columns in mt20_aus_ind?

```R
dim(mt20_aus_ind)
```



* What does each row in mt20_aus_ind represent?
  Each row contains batting information about an Australia or Indian cricket player.
* What function returns the top of mt20_aus_ind the first 6 rows?

```R
mt20_aus_ind %>% head(6)
```



* Convert the data into a tidy long form

```R
## Convert mt20_aus_ind to long form
  # Take mt20_aus_ind and select only variables Player, Country, NotOuts, 
    # HighScore, Average, StrikeRate, Hundreds, Fifties, Ducks, Fours, Sixes
  # Convert this data into a tidy long form using gather()
  # Gather all columns in mt20_aus_ind except Player and Country,
    # Specifying the key and value as Bat_Stats and Value
  # Store this tidy long form data of men's T20 batting statistics in an object
    # named mt20_aus_ind_long

mt20_aus_ind_long <- mt20_aus_ind %>%
	select(Player, Country, NotOuts, HighScore, Average, StrikeRate, Hundreds, Fifties,
         Ducks, Fours, Sixes) %>%
	gahter(key = Bat_Stats, value = Value, -Player, -Country)
```



  * How many rows and columns ar in mt20_aus_ind_long?
  * What information does the column Bat_Stats contain?
  * What information does the column Value contain?

```R
## Box plots of countries by batting statistics
  # Take mt20_aus_ind_long and pipe in the ggplot() function.
  # Add layers to the ggplot call:
    # 1st layer: The x aesthetic should be Country and the y aesthetic the values of the battling statistics.
    # 2nd layer: Box plots are the visual elements we want to use for our graph, so add geom_boxplot().
    # 3rd layer: Another visual element we want to include is jittered data, so add geom_jitter().
    # 4th layer: Facet the graph by the variable that represents batting statistics.
    # 5th layer: Add labels to your graph with labs().

mt20_aus_ind_long %>%
	ggplot(aes(x=Country, y=Value)) +
	geom_boxplot() +
	geom_jitter() +
	facet_wrap(~Bat_Stats, scales="free") +
	labs(title = "Distribution of Australian and Indian batting statistics",
       caption = "Data source: https://github.com/ropenscilabs/cricketdata")
```



* Explain what do the warning messages tell us about our data?

```R
mt20_aus_ind_long %>%
  filter(is.na(Value))
```



```R
mt20_aus_ind_long %>%
  summarise(sum = sum(is.na(Value)))
```



  * Based on the above 9 numerical variables that contain batting statistics
    from Australian and Indian cricket players, what do you conclude about
    each countries batting performance?
    
    The distribution of runs from Australian and Indian players are very similar

## Computing grouped statistics

```R
mt20_aus_ind %>%
  group_by(Country) %>%
  summarise(total_runs = sum(Runs, na.rm=TRUE),
            total_matches = sum(Matches, na.rm=TRUE),
            totalruns_totalmatches = total_runs/total_matches) %>%
  ungroup()
```



  * Which country has players with more experience in T20 cricket, has
    played more matches?
  * Which country has the higher average of total runs by total matches?
    Can you confidently conclude that this country's batting performing
    in T20 cricket is superior?



## Relationship between average runs and strike rate

```R
# Scatter plot of average runs and strike rate
mt20_aus_ind %>%
  ggplot(aes(x=StrikeRate, y=Average, color=Country)) +
  geom_point(alpha = 0.5)
```



  * What does the warning message tell you about the data?
    27 missing values
  * What is the relationship between average run and strike rate?
    Does this differ by country?
  * How might you inspect values of average run along the upper limit of the 
    graph(above 50 runs)?



```R
mt20_aus_ind %>%
  filter(Average > 50) %>%
  arrange(desc(Average))
```



## rvest package for web scraping

```R
# Load the rvest package
# Store each URL as a object
  # ODI URL can be stored as an object named odi_url
  # T20 URL can be stored as an object named t20i_url
# Use the read_html() function from the rvest package to scrape data from odi_url and t20i_url
  # ODI scraped data can be stored as a object named odi_page
  # T20 scraped data can be stored as a object named t20i_page

library(rvest)
odi_url <- "https://www.icc-cricket.com/rankings/mens/team-rankings/odi"
t20i_url <- "https://www.icc-cricket.com/rankings/mens/team-rankings/t20i"

odi_page <- read_html(odi_url)
t20i_page <- read_html(t20i_url)
```



  * HTML table element inside of odi_page and t20i_page can be extracted
    and returned as a data frame with the html_table() function from the rvest
    package

```R
# Extract ODI and T20I ratings from odi_page and t20i_page
odi_tables <- odi_page %>%
  html_table(fill=TRUE)

t20i_tables <- t20i_page %>%
  html_table(fill=TRUE)

# Store ODI and T20I ratings in object named odi and t20i
odi <- odi_tables[[1]]
t20i <- t20i_tables[[1]]
```





* Below are bar plots of each countries' rating in ODI and T20I cricket, arranged
    from highest to lowest. To replicate this plot, you will need to add an x and
    y aesthetic (with the variable in the x aesthetic ordered using the fct_reorder()
    function), add a geom layer that tells R to use bars as visual elements for the plot,
    add a layer to flip the x and y axis (coord_flip()) and add the last layer to lab 
    the titles of the plot.

```R
  # fct_reorder(Team, Rating) turns Team into an ordered factor variable based on the variable Rating 
  # Unfortunately the website has slightly changed its formatting which has caused issues for the
  # names of the countries.

odi %>%
  ggplot(aes(x=fct_reorder(`Team
                            T`,`Rating
                            R`), y=`Rating
                            R`)) +
  geom_bar(stat = "identity", alpha = 0.5) +
  coord_flip() +
  labs(title = "Country rating for ODI cricket")
```

```R
t20i %>%
  ggplot(aes(x=fct_reorder(`Team
                            T`,`Rating
                            R`), y=`Rating
                            R`)) +
  geom_bar(stat="identity", alpha =0.5) +
  coord_flip() +
  labs(title = "Country rating for T20I cricket")
```

