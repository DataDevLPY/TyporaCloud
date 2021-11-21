# Tutorial 5 - Unix

**Lab Activity 5.1: Stream Processing of Big Data**

1. Starting a shell

2. Running the shell

   command -options arguments



​		**1). ls  (list files)**

```unix
# 列出可读文件和文件夹详细信息
ls -lh 
ls -lh filename
```

```unix
# list all files
ls -a

# colored list
ls --color

# list with long format
ls -l

# list with reverse order
ls -r

```



​		**2). cd**

```unix
# To move to the parent directory of current directory
cd ..

# To move to inside a subdirectory
cd directory-name
cd dir\ name

# To move to the home directory
cd ~

```



​		**3). pwd**

```unix
# path to the working directory
```



​		**4). cp**

```unix
# copy the first file to the current directory
cp directory-path/file .
cp [source][destination]
```



​		**5). unzip**

```unix
# 解压
unzip tutorial_data_shell.zip
```

​		

​		**6). 清理桌面**

```unix
clear/ cls/ ctrl+l	/ command + k
```



​		**7). head/ tail**

```unix
# 获取文件第一行，直接观察分隔符
head -1 filename 

# Print the first/last few lines of a file
head myfile.txt
tail myfile.txt
```



​		**8). mkdir**

```unix
# build file
mkdir filename
```



## Practice 1  -- less

![截屏2021-02-01 下午9.15.54](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 下午9.15.54.png)



## Practice 2 -- grep

The asterisk "*"is a wild-card and matches against all the files book1.txt through to book10.txt, and the results is the same as if we called grep on each of those files in turn.

```unix
# Find lines in a file containing a keyword
grep "Title" book2.txt
grep "Title" book*.txt
grep "Arthur" book[1-5].txt
```



## Practice 3 -- wc

```unix
# 文件行数、单词数、字节数
wc book2.txt
```



## Practice 4 -- cat

```unix
# Print the contents of a file to the screen
cat myfile.txt

# In order to avoid listing large number of files, we can use the wildcard syntax to specify all files in a directory with a certain pattern

cat book*.txt
```



## Practice 5 -- sort

![截屏2021-02-01 下午9.39.22](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 下午9.39.22.png)



## Mutiple

```unix
cat hourly_44201_2014-06.csv.gz | gunzip | wc
cat hourly_44201_2014_06.csv.gz | gunzip | less
cat hourly_44201_2014_06.csv.gz | gunzip | head
cat hourly_44201_2014_06.csv.gz | gunzip | tail
```



**could use the head command to do this by appending the flag -n1000. We could then save these lines to a file rather than printing them out on the screen by doing a redirect with the ">" symbol**

```unix
cat hourly_44201_2014_06.csv.gz | gunzip | head -n1000 > first_1000_lines.txt
```



```unix
cat hourly_44201_2014_06.csv.gz | gunzip | awk -F ',' '{print $6, $7, $14}' | less
```

```unix
cat hourly_44201_2014_06.csv.gz | gunzip | awk -F ',' 'NR>1000 && NR<=1500 {print $6,$7,$14}' | less
```



**Often we'd like a random sample of the data. Again this is very simple awk. If we want to sample 1% of the rows, we can use the random number function "rand()" to produce a value in the range 0 to 1 and only output a line if the value is less than 1/100**

```unix
cat hourly_44201_2014_06.csv.gz | gunzip | awk -F ',' 'rand()<1/100 {print $6,$7,$14}' |less
```

**We can also select only rows that have a particular value in one of the columns. For instance, we could select measurements around the Los Angeles area (34.0522, -118.2437) by restricting the latitude and longitude coordinates, as follows**

```unix
cat hourly_44201_2014-06.csv.gz | gunzip | awk -F',' '$6>=34 && $6<=34.5 && $7>=-118.5 && $7<=-118 {print $6,$7,$14}' | less
```

**There are many other programs available in the Unix shell. Particularly useful is the "sort" command, which can sort a column of values either lexicographically (i.e. alphabetically) or numerically (by using the -n flag)**

```unix
cat hourly_44201_2014-06.csv.gz | gunzip | awk -F',' '{print $14}' | sort -n | les
```



**Note that the strings in the State Name column are wrapped in double quotes characters ("), so to match the value "California" (quotes included) we need to escape the double quotes using the backslash character as follows: "\"California\"". We also want to print out the column names to the file, so we'll pass through the first line of the file by checking if NR==1.**

```unix
unzip -p hourly_WIND_2015.zip | awk -F',' '$22=="\"California\"" || NR==1 {print $6,$7,$14}' > california_wind.txt
```













