## 1. Regular expression

```
# [0-9] Matches a single digit
# [a-z0-9] Matches a single character that must be a lower case letter or a digit.
# [A-Za-z] Matches a single character that much be a upper/lower case letter
# \d Matches any decimal digit; equivalent to the set [0-9].
# \D Matches characters that are not digits, which is equivalent to [^0-9] or [^\d]
# \w Matches any alphanumeric character, which is equivalent to [a-zA-Z0-9].
# \W Matches any non-alphanumeric character; which is equivalent to [^a-zA-Z0-9] or [^\w].
# \s Matches any whitespace character; which is equivalent to [\t\n\r\f\v], where \t indicates taps, \n line feeds, \r carriage returns, \f form feeds and \v vertical tabs.
# \S: Matches any non-whitespace character; which is equivalent to [^ \t\n\r\f\v].
# ˆ Matches the start of the line.
# $ Matches the end of the line.
# . Matches any character (a wildcard).
# * Matches when the preceding character occurs zero or more times
# ? Matches when the preceding character occurs zero or one times
# + Matches when the preceding character occurs one or more times
# x|y Matches x or y
# [xyz] Matched any one of the characters in the brackets. charecter set
# [^xyz] Matches a negated or complemented character set.
# \t Matches a tab
# \n Matches a line feed
# {m} Matches exactly m occurances of the preceeding expression.
# {n,m} Where n and m are positive integers and n<= m. Matches at least n and at most occurances of the preceeding expression.
# (x) Matches 'x' and remembers the match.:capturing parentheses.
# {?:x} Matches 'x' and does not remember the match.: non-capturing parentheses.
# (.*) Matches the remainder charcter and symbol.
# (.+) Matches everything.

```

```python
import re # library for regular expression
import pandas as pd
```

## 2. Backslash

```python
print('Tab')
print('\tTab')
print(r'\tTab')
```

![截屏2021-01-25 下午9.02.17](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午9.02.17.png)

```python
print('This is a \nnew line')
print(r'This is a \nnew line')
```

![截屏2021-01-25 下午9.03.03](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午9.03.03.png)

```python
str1 = re.findall('\\t','Please find \t')
print(str1)

str2 = re.findall('\t','Please find \t')
print(str2)
```

![截屏2021-01-25 下午9.04.42](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午9.04.42.png)

```python
urls = '''
https://www.google.net
http://examplewebsite.com
https://youtube.com
https://www.examplewebsite.net
'''
pattern = re.compile(r'https?://(www\.)?(\w+)\.(\w+)')

matches = pattern.finditer(urls)
for match in matches:
    print(match.group(3))
    
    # match.group(1)
```

![截屏2021-01-25 下午9.53.23](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午9.53.23.png)

```python
def date(pattern, m):
    if re.match(pattern, m):
        print (m + " is a date")
    else:
        print (m + " is NOT a date")
```

```python
regex = r'''(?x)
    (?:
    # February (29 days every year)
      ([12][0-9]|0?[1-9])[/-](0?2)
    # 30-day months
      |([12][0-9]|0?[1-9]|30)[/-](0?[469]|11)
    # 31-day months
      |([12][0-9]|0?[1-9]|3[01])[/-](0?13578|1[02])
    ) 
    # Year
    [/-]((?:\d{2})?\d{2})
'''
```

```python
#date(r"((31[/-](0?[13578]|1[02]))|(30[/-](0?[469]|11))|(28[/-]02))[/-]((?:\d{2})?\d{2})", "28/02/2019")
date(regex, "28/02/2019")
date(regex, "31/04/2019")
date(regex, "29/05/2019")
date(regex, "31/06/2019")
```

![截屏2021-01-25 下午10.06.02](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午10.06.02.png)

## 3. Extract IPs, dates, and email address with regular expressions

### 3.1 Find IP addresses

```python
with open('mbox-short.txt','r') as infile:
    text = infile.read()
```

```python
ip_addr = re.findall(r'\b(?:[0-9]{1,3}\.){3}[0-9]{1,3}',text)
print(ip_addr)
```

### 3.2 Extract All date time

```python
datetime = re.findall(r'(\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2})',text)
print(set(datetime))
```

### 3.3 Extract author's email address

```python
email_addr = re.findall(r'[a-zA-Z0-9-+._]+@[a-zA-Z0-9-+]+\.[a-zA-Z0-9-+.]+',text)
print(email_addr)
```











