

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

![截屏2021-02-04 上午11.06.15](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220055167.png?token=AWS37JL2267EDWKUS6PHDTDBTJ5FW)

```R
v7 <- vector(mode = "numeric", length = 5)
print(v7)
```

![截屏2021-02-04 下午1.18.05](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220056300.png?token=AWS37JONCA7B4KM6IEVQR2DBTJ5GA)



**Vector Functions**

![截屏2021-02-04 上午11.05.52](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220056448.png?token=AWS37JM74SYYAZEE3BGZXR3BTJ5GK)

```R
x <- c(2,5,3,9,4,1)
sort(x, decreasing = True)
```



**Selecting Vector Elements**

![截屏2021-02-04 上午11.16.48](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220056392.png?token=AWS37JPOTDLLKBOQOXILXADBTJ5GS)



## Programming

**For Loop**

![截屏2021-02-04 上午11.53.37](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220056507.png?token=AWS37JMAXCPDZTW3F3W3HPTBTJ5HG)



**while Loop**

![截屏2021-02-04 上午11.54.06](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220056096.png?token=AWS37JPFB3CULMEOQRN3VSTBTJ5HK)



**if statments**

![截屏2021-02-04 下午12.22.36](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220056812.png?token=AWS37JPIMF3IWORBB35L2J3BTJ5HU)

```R
# ifelse function
x <- seq(10)
ifelse(x %% 2 == 0, "even", "odd")
```

![截屏2021-02-04 下午8.34.01](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220056909.png?token=AWS37JPMJIALAJU7SXHKM4TBTJ5IK)

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

![截屏2021-02-04 下午12.23.01](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220056368.png?token=AWS37JMI66OWTK3PI2KFD33BTJ5IS)



![截屏2021-02-04 下午8.40.55](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220056915.png?token=AWS37JOI6WCDWGHQ2LZ6MS3BTJ5IW)



## Reading and Writing Data

![截屏2021-02-04 下午12.24.02](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220056018.png?token=AWS37JNWSLIGXGNN7QDK3U3BTJ5JC)

**Conditions**

![截屏2021-02-04 下午12.24.37](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220056328.png?token=AWS37JIROGMALOHOSSA3OQLBTJ5JM)



## Types

![截屏2021-02-04 下午12.26.41](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220057068.png?token=AWS37JJTUUCIEHQPLUYBZQ3BTJ5JU)



## Maths Functions

![截屏2021-02-04 下午12.27.10](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220057560.png?token=AWS37JNWBJCEGBGVAJL3GKTBTJ5KA)



## R Logical Operators

- 𝑥&𝑦x&y for (x and y): Element-wise logical AND
- 𝑥&&𝑦x&&y : Logical AND
- 𝑥|𝑦x|y for (x or y) Element-wise logical OR
- 𝑥||𝑦x||y: Logical OR
- !𝑥!x for (not x): Logical NOT



## The Environment

![截屏2021-02-04 下午12.27.59](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220057746.png?token=AWS37JNAIZKZUG7OQTGSK3DBTJ5KK)



## Matrics

![截屏2021-02-04 下午12.28.42](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220057080.png?token=AWS37JOVRKBY3VRKMNS5KS3BTJ5KS)

```R
# two other methods to create matricesx
x <- c(1,11,111)
y <- c(2,22,222)
m1 <- cbind(x,y)   # column-binding
m2 <- rbind(x,y)    # raw-binding
print(m1)
print(m2)
```

![截屏2021-02-04 下午8.28.38](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220057077.png?token=AWS37JIZ2WUU6FKBFOR7QGLBTJ5K6)



## Lists

![截屏2021-02-04 下午12.30.22](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220057990.png?token=AWS37JMXO4IA7M2OYKYOW7DBTJ5LC)

```R
L2 <- list(c(1,2,3),c("One", "Two"),TRUE)
L2
```

![截屏2021-02-04 下午1.21.19](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220057581.png?token=AWS37JI3ELEZULFYN3OCBELBTJ5LO)



![截屏2021-02-04 下午8.21.45](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220057198.png?token=AWS37JPIYHJXFB22UYMOIZLBTJ5LY)





## Data Frame

![截屏2021-02-04 下午12.32.05](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220057602.png?token=AWS37JOKBWUUCPVMJFXYKNTBTJ5MG)

## Strings

![截屏2021-02-04 下午12.33.46](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220057365.png?token=AWS37JIAX2G6XUJOAI5TWQ3BTJ5MK)



## Factors

![截屏2021-02-04 下午12.34.23](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220057995.png?token=AWS37JKML72WOUTFL2EJ4OTBTJ5MW)



![截屏2021-02-04 下午8.00.48](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220057582.png?token=AWS37JOOI2COMJMDCA6MAVTBTJ5M6)

![截屏2021-02-04 下午8.02.53](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220058410.png?token=AWS37JPFNDQWTOA4XIYBKIDBTJ5PU)



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

![截屏2021-02-04 下午12.35.20](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220058116.png?token=AWS37JKX56KEREKYZ3WY4UDBTJ5NO)



## Distributions

![截屏2021-02-04 下午12.36.37](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220058701.png?token=AWS37JKFSIH6VAICHXBUJQDBTJ5N2)



## Plotting

![截屏2021-02-04 下午12.37.52](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220058194.png?token=AWS37JMZEUJKCJVIZCTJTCLBTJ5OS)



