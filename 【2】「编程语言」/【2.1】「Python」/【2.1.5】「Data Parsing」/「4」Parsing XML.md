# Parsing XML files with BeautifulSoup

```python
from bs4 import BeautifulSoup
btree = BeautifulSoup(open("./Melbourne_bike_share.xml"), "lxml-xml")
```

You can alo print out the Beautifulsoup object by calling the prettify() function.

```python
print(btree.prettify())
```

![截屏2021-01-28 上午10.05.31](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8A%E5%8D%8810.05.31.png?token=AWS37JJAS6ZWD745OCVDSYLBTICJ6)

It is easy to figure out information we would like to extract is stored in the following tags

- id
- featurename
- terminalname
- nbbikes
- nbemptydoc
- uploaddate
- coordinates



Each record is stored in " ". To extract information from those tags, except for "coordinates", we use the find_all() function. Its documentation can be found [here](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#find-all).

```python
featuretags = btree.find_all("featurename")
```

![截屏2021-01-28 上午10.06.42](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8A%E5%8D%8810.06.42.png?token=AWS37JLQNC5DU6STTHNKMPTBTICKA)

```python
for feature in featuretags:
		print(feature.string)
```

![截屏2021-01-28 上午10.08.26](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8A%E5%8D%8810.08.26.png?token=AWS37JPBDF6SO3APRIZJ2P3BTICKS)

```python
featurenames = [feature.string for feature in btree.find_all("featurename")]
# featurenames = [featurename.append(elem.text) for elem in tree.iter(tag='featurename')]
nbbikes = [feature.string for feature in btree.find_all("nbbikes")]
NBEmptydoc = [feature.string for feature in btree.find_all("nbemptydoc")]
TerminalNames = [feature.string for feature in btree.find_all("terminalname")]
UploadDate = [feature.string for feature in btree.find_all("uploaddate")]
ids = [feature.string for feature in btree.find_all("id")]
lattitudes = [coord["latitude"] for coord in btree.find_all("coordinates")]
longitudes = [coord["longitude"] for coord in btree.find_all("coordinates")]
```

```python
import pandas as pd 
dataDict = {}
dataDict['Featurename'] = featurenames
dataDict['TerminalName'] = TerminalNames
dataDict['NBBikes'] = nbbikes
dataDict['NBEmptydoc'] = NBEmptydoc
dataDict['UploadDate'] = UploadDate
dataDict['lat'] = lattitudes
dataDict['lon'] = longitudes
df = pd.DataFrame(dataDict, index = ids)
df.index.name = 'ID'
df.head()
```

![截屏2021-01-28 上午10.11.36](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8A%E5%8D%8810.11.36.png?token=AWS37JIKNC5SHRFQCMUANGLBTICK2)

## Loading and Exploring an XML file

Python can parse XML files in many ways. You can find several Python libraries for parsing XML from [" XML Processing Modules"](https://docs.python.org/2/library/xml.html). Here we will show you how to use the following Python libraries to parse our XML file.

- ElementTree
- lxml
- beautifulsoup

There are a couple of good materials worth reading

- The office ElementTree [API](https://docs.python.org/2/library/xml.etree.elementtree.html#module-xml.etree.ElementTree) documentation, which provides not only the API reference but also a short tutorial on using ElementTree. 📖
- [Parsing XML](http://www.diveintopython3.net/xml.html#xml-parse), Section 12.4 in Chapter 12 of "**Dive into Python**" does a good job on elaborating the process of parsing an example XML file with ElementsTree. 📖

If you are a visual learner, we suggest the following YouTube video

- [Parsing XML files in Python](https://www.youtube.com/watch?v=c2qlCZhkwtE)

We strongly suggest that you read these materials, although we are going to reproduce some of their content along with our own XML file.

Let's start with ElementTree. There are several ways to import the data, which depends on how the data is stored. Here we will read the file from disk.

```python
import xml.etree.ElementTree as etree
tree = etree.parse("./Melbourne_bike_share.xml")
```

```python
root = tree.getroot()     
root.tag
```



```python
for child in root[0][0]:
		print(child)
```

![截屏2021-01-28 上午10.25.59](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8A%E5%8D%8810.25.59.png?token=AWS37JILGIDIWOVY3U62S3LBTICLE)

```python
for elem in tree.iter():
    print (elem.tag, elem.text, elem.attrib)
```

![截屏2021-01-28 上午10.27.17](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-28 上午10.27.17.png)



### The lxml package

```python
from lxml import etree
ltree = etree.parse("./Melbourne_bike_share.xml")
for el in ltree.xpath('descendant-or-self::text()'):
    print (el)
```

![截屏2021-01-28 上午10.31.19](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-28 上午10.31.19.png)





















