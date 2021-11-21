## Introduction to Web Scraping 

We cover in this part scraping data from the web. Data can be presented in HTML, XML and API etc. Web scraping is the practice of using libraries to sift through a web page and gather the data that you need in a format most useful to you while at the same time preserving the structure of the data.

There are several ways to extract information from the web. Use of APIs being probably the best way to extract data from a website. Almost all large websites like Twitter, Facebook, Google, Twitter, StackOverflow provide APIs to access their data in a more structured manner. If you can get what you need through an API, it is almost always preferred approach over web scrapping. However, not all websites provide an API. Thus, we need to scrape the HTML website to fetch the information.

Non-standard python libraries needed in this tutorial include

- urllib
- beatifulsoup
- requests

```python
from urllib.request import urlopen
from bs4 import BeautifulSoup
import re
```

### Task 1 Extract a list of links on a Wikipedia page.

Instead of retrieving all the links existing in a Wikipedia article, we are interested in extracting links that point to other article pages. If you look at the source code of the following page

```
https://en.wikipedia.org/wiki/Kevin_Bacon
```

in your browser, you fill find that all these links have three things in common:

- They are in the *div* with id *set* to *bodyContent*
- The URLs do not contain semicolons
- The URLs begin with */wiki/*

We can use these rules to construct our search through the HTML page.

Firstly, use the urlopen() function to open the wikipedia page for "Kevin Bacon",

Then, find and print all the links. In order to finish this task, you need to

- find the *div* whose *id = "bodyContent"*

- find all the link tags, whose href starts with "/wiki/" and does not ends with ":". For example

  ```html
  see <a href="/wiki/Kevin_Bacon_(disambiguation)" class="mw-disambig" title="Kevin Bacon (disambiguation)">Kevin Bacon (disambiguation)</a>
  <a href="/wiki/Philadelphia" title="Philadelphia">Philadelphia</a>
  ```

Hint: regular expression is needed.

```python
bsobj = BeautifulSoup(html, "lxml")
for link in bsobj.find("div", {"id": "bodyContent"}).findAll("a", href=re.compile("^(/wiki/)((?!:).)*$")):
    if 'href' in link.attrs:
        print(link.attrs['href'])
```

![截屏2021-01-31 上午10.20.12](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-31 上午10.20.12.png)

### Task 2 Perform a random walk through a given webpate.

Assume that we will find a random object in Wikipedia that is linked to "Kevin Bacon" with, so-called "Six Degrees of Wikipedia". In other words, the task is to find two subjects linked by a chain containing no more than six subjects (including the two original subjects).

```python
import datetime
import random

random.seed(datetime.datetime.now())
def getLinks(articleUrl):
    html = urlopen("http://en.wikipedia.org"+articleUrl)
    bsObj = BeautifulSoup(html, "html.parser")
    return bsObj.find("div", {"id":"bodyContent"}).findAll("a", href=re.compile("^(/wiki/)((?!:).)*$"))
links = getLinks("/wiki/Kevin_Bacon")
```

The details of the random walk along the links are

- Randomly choosing a link from the list of retrieved links
- Printing the article represented by the link
- Retrieving a list of links
- repeat the above step until the number of retrieved articles reaches 5.

```python
count = 0
while len(links) > 0 and count < 5:
    newArticle = links[random.randint(0, len(links)-1)].attrs["href"]
    print(newArticle)
    links = getLinks(newArticle)
    count = count + 1
```

![截屏2021-01-31 上午10.35.39](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-31%20%E4%B8%8A%E5%8D%8810.35.39.png?token=AWS37JPD547SQFGOX3WTD33BTIBEU)

```python
links[random.randint(0, len(links)-1)].attrs["href"]
```

![截屏2021-01-31 上午10.36.16](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-31%20%E4%B8%8A%E5%8D%8810.36.16.png?token=AWS37JKA23PWFGQPF3W5GCDBTIBE2)

```python
random.randint(0, len(links)-1)
```

![截屏2021-01-31 上午10.37.02](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-31%20%E4%B8%8A%E5%8D%8810.37.02.png?token=AWS37JP6BP63XKYNMOB5HJ3BTIBE6)

### Task 3 Crawl the Entire Wikipedia website

The general approach to an exhaustive site crawl is to start with the root, i.e., the home page of a website. Here, we will start with

```
https://en.wikipedia.org/
```

by retrieving all the links that appear in the home page. And then traverse each link recursively. However, the number of links is going to be very large and a link can appear in many Wikipedia article. Thus, we need to consider how to avoid repeatedly crawling the same article or page. In order to do so, we can keep a running list for easy lookups and slightly update the getLinks() function.

```python
pages = set()
```



## Note: add a terminating condition in your code, for example,

```python
    len(pages) < 10
```

Otherwise, the script will run through the entire Wikipedia website, which will take a long time to finish. So please avoid that in the tutorial class.

```python
def getLinks(pageUrl):
    global pages
    html = urlopen("http://en.wikipedia.org"+pageUrl)
    bsObj = BeautifulSoup(html, "html.parser")
    for link in bsObj.findAll("a", href=re.compile("^(/wiki/)")):
        if 'href' in link.attrs:
            if link.attrs['href'] not in pages and len(pages) < 10:
                #We have encountered a new page
                newPage = link.attrs['href']
                print("----------------\n"+newPage)
                pages.add(newPage)
                getLinks(newPage)
```

```python
getLinks("")
```

![截屏2021-01-31 上午10.40.48](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-31%20%E4%B8%8A%E5%8D%8810.40.48.png?token=AWS37JPM2JSH3IHOWXO4ARLBTIBFK)

### Task 4 Collect data across the Wikipedia site

One purpose of traversing all the the links is to extract data. The best practice is to look at a few pages from the side and determine the patterns. By looking at a handful of Wikipedia pages both articles and non-articles pages, the following pattens can be identified:

- All titles are under h1 span tags, and these are the only h1 tags on the page. For example,

  ```html
  <h1 id="firstHeading" class="firstHeading" lang="en">Kevin Bacon</h1>
  ```

  ```html
  <h1 id="firstHeading" class="firstHeading" lang="en">Main Page</h1>    
  ```

- All body text lives under the *div#bodyContent* tag. However, if we want to get more specific and access just the first paragraph of text, we might be better off using div#mw-content-text -> p.

- Edit links occur only on article pages. If they occur, they will be found in the *li#ca-edit tag*, under *li#ca-edit -> span -> a*

Now, the task is to further modify the getLink() function to print the title, the first paragraph and the edit link. The content from each page should be separated by

```pyhon
print("----------------\n"+newPage)
```



```python
html = urlopen("http://en.wikipedia.org")
bsObj = BeautifulSoup(html, "html.parser")
```

```python
bsObj
```

![截屏2021-01-31 上午10.43.20](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-31%20%E4%B8%8A%E5%8D%8810.43.20.png?token=AWS37JPTCT6JUIQC7CFTGEDBTIBFQ)

```python
def getLinks(pageUrl):
    global pages
    html = urlopen("http://en.wikipedia.org"+pageUrl)
    bsObj = BeautifulSoup(html, "html.parser")
    try:
        print(bsObj.h1.get_text())
        print(bsObj.find(id ="mw-content-text").findAll("p")[0])
        print(bsObj.find(id="ca-edit").find("span").find("a").attrs['href'])
    except AttributeError:
        print("This page is missing something! No worries though!")
    
    for link in bsObj.findAll("a", href=re.compile("^(/wiki/)")):
        if 'href' in link.attrs:
            if link.attrs['href'] not in pages and len(pages) < 5:
                #We have encountered a new page
                newPage = link.attrs['href']
                print("----------------\n"+newPage)
                pages.add(newPage)
                getLinks(newPage)
```



### Task 5 API access

In addition to HTML format, data is commonly found on the web through public APIs. We use the 'requests' package ([http://docs.python-requests.org](http://docs.python-requests.org/)) to call APIs using Python. In the following example, we call a public API for collecting weather data.

** You need to sign up for a free account to get your unique API key to use in the following code. register at** [http://api.openweathermap.org](http://api.openweathermap.org/)

```python
#Now we  use requests to retrieve the web page with our data
import requests
url = 'http://api.openweathermap.org/data/2.5/forecast?id=524901&cnt=16&APPID=1499bcd50a6310a21f11b8de4fb653a5'
#write your APPID here#
response= requests.get(url)
response
```

![截屏2021-01-31 上午10.45.58](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-31%20%E4%B8%8A%E5%8D%8810.45.58.png?token=AWS37JJJUTHQS4N636T53PLBTIBF2)

The response object contains GET query response. A successfull one has a value of 200. we need to parse the response with json to extract the information.

```python
#Check the HTTP status code https://en.wikipedia.org/wiki/List_of_HTTP_status_codes
print (response.status_code)
```

![截屏2021-01-31 上午10.46.44](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-31%20%E4%B8%8A%E5%8D%8810.46.44.png?token=AWS37JJKK5KTEAFRQWSJS7LBTIBGA)

```python
# response.content is text
print (type(response.content))
```

![截屏2021-01-31 上午10.46.56](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-31%20%E4%B8%8A%E5%8D%8810.46.56.png?token=AWS37JL2ZD264BIZDHUUU7TBTIBGK)

```python
#response.json() converts the content to json 
data = response.json()
print (type(data))
```

![截屏2021-01-31 上午10.47.18](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-31%20%E4%B8%8A%E5%8D%8810.47.18.png?token=AWS37JKAXSKJ6Q6DKSCLQLLBTIBGO)

```python
data.keys()
```

![截屏2021-01-31 上午10.47.54](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-31%20%E4%B8%8A%E5%8D%8810.47.54.png?token=AWS37JNXO2YDTUVB476TP7DBTIBGU)

```python
data
```

![截屏2021-01-31 上午10.48.16](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-31%20%E4%B8%8A%E5%8D%8810.48.16.png?token=AWS37JLQ2JZXOTL2XGG5BMDBTIBG4)









