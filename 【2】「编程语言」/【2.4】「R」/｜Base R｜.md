

# Base R

## Getting help

**Accessing the help files**

```R
# Get help of a particular function.
?mean
# Search the help files for a word or phase.
help.search('weighted mean')
# Find help for a package
help(package = 'dplyr')
```

**More about an object**

```R
# Get a summary of an object's structure
str(iris)
# Find the class an object belongs to.
class(iris)
typeof(iris)
```



## Using Libraries

```R
# Download and install a package from CRAN.
install.packages('dplyr')
# Load the package into the session, making all its functions available to use.
library(dplyr)
# Use a particular function from a package.
dplyr::select
# Load a built-in dataset into the environment
data(iris)
```



## Working Directory

```R
# Find the current working directory (where inputs are found and outputs are sent).
getwd()
# Change the current working directory.
setwd()
```



## Vectors

**Creating Vectors**

![截屏2021-02-04 上午11.06.15](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 上午11.06.15.png)

```R
v7 <- vector(mode = "numeric", length = 5)
print(v7)
```

![截屏2021-02-04 下午1.18.05](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午1.18.05.png)



**Vector Functions**

![截屏2021-02-04 上午11.05.52](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 上午11.05.52.png)

```R
x <- c(2,5,3,9,4,1)
sort(x, decreasing = True)
```



**Selecting Vector Elements**

![截屏2021-02-04 上午11.16.48](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 上午11.16.48.png)



## Programming

**For Loop**

![截屏2021-02-04 上午11.53.37](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 上午11.53.37.png)



**while Loop**

![截屏2021-02-04 上午11.54.06](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 上午11.54.06.png)



**if statments**

![截屏2021-02-04 下午12.22.36](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午12.22.36.png)

```R
# ifelse function
x <- seq(10)
ifelse(x %% 2 == 0, "even", "odd")
```

![截屏2021-02-04 下午8.34.01](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午8.34.01.png)

```R
if(x > 2)
    {
    print("Greater")
} else if(x < 2)
    {
    print("Smaller")
} else
    {
    print("Equal")
}
```



```R
x <- 1:10
if(any(x > 4)) print("Well done")
```



**Functions**

![截屏2021-02-04 下午12.23.01](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午12.23.01.png)



![截屏2021-02-04 下午8.40.55](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午8.40.55.png)



## Reading and Writing Data

![截屏2021-02-04 下午12.24.02](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午12.24.02.png)

**Conditions**

![截屏2021-02-04 下午12.24.37](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午12.24.37.png)



## Types

![截屏2021-02-04 下午12.26.41](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午12.26.41.png)



## Maths Functions

![截屏2021-02-04 下午12.27.10](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午12.27.10.png)



## R Logical Operators

- 𝑥&𝑦x&y for (x and y): Element-wise logical AND
- 𝑥&&𝑦x&&y : Logical AND
- 𝑥|𝑦x|y for (x or y) Element-wise logical OR
- 𝑥||𝑦x||y: Logical OR
- !𝑥!x for (not x): Logical NOT



## The Environment

![截屏2021-02-04 下午12.27.59](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午12.27.59.png)



## Matrics

![截屏2021-02-04 下午12.28.42](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午12.28.42.png)

```R
# two other methods to create matricesx
x <- c(1,11,111)
y <- c(2,22,222)
m1 <- cbind(x,y)   # column-binding
m2 <- rbind(x,y)    # raw-binding
print(m1)
print(m2)
```

![截屏2021-02-04 下午8.28.38](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午8.28.38.png)



## Lists

![截屏2021-02-04 下午12.30.22](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午12.30.22.png)

```R
L2 <- list(c(1,2,3),c("One", "Two"),TRUE)
L2
```

![截屏2021-02-04 下午1.21.19](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午1.21.19.png)



![截屏2021-02-04 下午8.21.45](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午8.21.45.png)





## Data Frame

![截屏2021-02-04 下午12.32.05](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午12.32.05.png)

## Strings

![截屏2021-02-04 下午12.33.46](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午12.33.46.png)



## Factors

![截屏2021-02-04 下午12.34.23](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午12.34.23.png)



![截屏2021-02-04 下午8.00.48](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午8.00.48.png)

![截屏2021-02-04 下午8.02.53](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午8.02.53.png)



## Missing values

A variable might not have a value, or its value might missing. In R missing values are displayed by the symbol NA (not avaiable).

- NA, mot avaiable
- Makes certain calculations impossibel
- is.na()
- is.nan()
- NA values have class

```R
mean(x1, na.rm = TRUE)
mean(x1, na.rm = T)

which(is.na(x1))    # Give index number
```







## Statistics

![截屏2021-02-04 下午12.35.20](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午12.35.20.png)



## Distributions

![截屏2021-02-04 下午12.36.37](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午12.36.37.png)



## Plotting

![截屏2021-02-04 下午12.37.52](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-04 下午12.37.52.png)



