# Investigating Facebook Data using shell commands

**1). Decompress the file. How big is it (bytes)?**

```unix
unzip FacebookNews
```

**2). What delimiter is used to separate the columns in the file and how many columns are there?**

```unix
head -1 abc-news-86680728811.csv 

cat abc-news-86680728811.csv | head -1 | awk ',' -F '{for (i=1; i<=NF; i++) print i, $i}'
```



**3) To see the line number in each file**

```unix
wc -l *.csv
```



**4) What is the date range for the articles in this file? (Assume that the data is in order)**

```unix
cat abc-news-86680728811.csv | awk -F "\",\"" 'NR==2 {print $20}'

cat abc-news-86680728811.csv | tail -1 | awk -F "\",\"" '{print $20}'
```



**5) How many unique titles are there?**

```unix
# 将 NULL 替换为 “NULL”
sed 's/NULL/"NULL"/g' abc-news-86680728811.csv | awk -F "\",\"" '{print $3}' | sort | uniq | wc -l 
```



**6) How many articles don’t have a title?**

```unix
sed 's/NULL/"NULL"/g' abc-news-86680728811.csv | awk -F "\",\"" '{print $3}' | grep -c "NULL"   
```



**7) When was the first mention in the files regarding “Italian food” and what was the title of the post?**

```unix
sed 's/NULL/"NULL"/g' *.csv | grep "Italian food" *.csv | head -1 |awk -F "\",\"" '{print $3}'
```



**8) How many times is “Hillary Clinton” mentioned in the articles? How did you find this?**

```unix
grep -c "Hillary Clinton" *.csv

grep "Hillary Clinton" *.csv | wc -l
```



**9) Select the posts where “Trump” (Ignore the case) is mentioned in the post content and number of likes for those posts are greater than 100. Generate a new file with post_id and sorted like_count and name it “trump.txt”. (In the output, you need to show the headers as well) [Hint: Find Trump in message column, i.e., 10th column]. Then copy and paste the first 5 lines of trump.txt in your answer.**

```unix
grep -i "Trump" *.csv | awk -F "\",\"" 'NR==1 {print "FileName","post_id","likes_count"}{if($9>100)print $1,$9}'| sort -nk 2 -t ' ' > trump.txt
```



**10) Find the total number of love_count and angry_count for “Donald Trump” and “Hillary Clinton” separately. Who has more positive feeling among people? Justify your answer. [Hint 1: you will need to search online to find how to sum a column of numbers using awk.**

```unix
sed 's/NULL/"NULL"/g' *.csv | grep -i "Clinton" | awk -F "\",\"" '{sum1 += $9}END{print "1). like_count: "sum1} {sum2 += $17}END{print "2). angry_count: " sum2}{final = sum1 - sum2}END{print "3). Final positive feeling: " final}'
```





**11) How many articles discussed Trump and Putin? How many discussed Trump but not Clinton?**

```unix
grep "Trump" *.csv | grep "Putin" | sed 's/NULL/"NULL"/g'| awk -F "\",\"" '{print $3}' | sort | uniq | wc -l


grep "Trump" *.csv | grep -v "Clinton" | sed 's/NULL/"NULL"/g'| awk -F "\",\"" '{print $3}' | sort | uniq | wc -l
```



**12) For each publication in trump.txt, find out which month had the most articles about Trump. Try to do this without using grep**

```unix
sed 's/NULL/"NULL"/g' *.csv | awk "/Trump/" | awk -F "\",\"" '{print $20}' | awk -F " " '{print $1}' | awk -F "-" '{print $2}' | awk '{count[$1]++}END{for(word in count) print word, count[word]}'
```

















