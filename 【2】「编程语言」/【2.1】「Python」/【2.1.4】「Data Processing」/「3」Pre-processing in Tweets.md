## Reading and Processing Tweets in JSON formats

### Loading Tweets from a Dump File

```python
# Import the necessary package to process data in JSON format
try:
    import json
except ImportError:
    import simplejson as json
```

```python
import codecs
tweets = []
f = codecs.open("tweetSamples.json", "r", "utf-8")
for line in f:
    tweet = json.loads(line)
    tweets.append(tweet) 
f.close()
```

````python
tweets[0].keys()
````

![截屏2021-01-28 下午4.58.23](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220022619.png?token=AWS37JKTZV25OO2JMSL6PADBTJZG6)

```python
import pandas as pd
tweets_pddf = pd.DataFrame()
tweets_pddf['id'] = list(map(lambda tweet: tweet['id'], tweets))
tweets_pddf['user_id'] = list(map(lambda tweet: tweet['user']['id'], tweets))
tweets_pddf['user_name'] = list(map(lambda tweet: tweet['user']['name'], tweets))
tweets_pddf['user_sname'] = list(map(lambda tweet: tweet['user']['screen_name'], tweets))
tweets_pddf['created_at'] = list(map(lambda tweet: tweet['created_at'], tweets))
tweets_pddf['text'] = list(map(lambda tweet: tweet['text'], tweets))
```

### Looking for Emoticons

```python
tweets_text = list(map(lambda tweet: tweet['text'], tweets))

t =''
for text in tweets_text:
    if "Hats off all" in text:
        t = text
print (t)
```

![截屏2021-01-28 下午5.01.28](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220022715.png?token=AWS37JJPV2U3XOWB6VUEVKDBTJZHK)

```python
print(t.encode("raw_unicode_escape").decode("utf-8"))
```

![截屏2021-01-28 下午5.01.52](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-28 下午5.01.52.png)



```python
import emoji 

print(emoji.emojize('simle :smile:', use_aliases=True))
print(emoji.emojize('heart_eyes :heart_eyes:', use_aliases=True))
print(emoji.emojize('smiling_imp :smiling_imp:', use_aliases=True))
print(emoji.emojize('book :book:', use_aliases=True))
```

![截屏2021-01-28 下午5.02.21](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220022693.png?token=AWS37JNHLZUI35P63Y6AMODBTJZHW)

To extract all the emoticons in a tweet, we are going to use the `emoji.get_emoji_regexp()` method that returns a compiled regular expression that matches all the emoticons defined in `emoji`, and then pass this regular expression to the `findall` method as follows:

```python
import re

emoticon_regexp = emoji.get_emoji_regexp() # get the regular expressions for all emoticons
ems = re.findall(emoticon_regexp, t) # find all emoticons
for e in ems:
    print (e, ":", e.encode("'unicode_escape'").decode("utf-8"))
```

![截屏2021-01-28 下午5.03.07](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220022758.png?token=AWS37JNWNEPBUYRV2WYC353BTJZIA)





```python
def findEmoticons(text):
    emoticon_regexp = emoji.get_emoji_regexp()
    emoticons = re.findall(emoticon_regexp, text) 
    return emoticons
```

```python
count = 0
for tweet in tweets_text:
    emoticons = findEmoticons(tweet)
    if len(emoticons) > 0:
        print (tweet)
        print (', '.join(emoticons))
        count = count + 1
print ("\n#tweets containing emoticon: ", count)
```

![截屏2021-01-28 下午5.04.40](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220022139.png?token=AWS37JMHOICWUBGJUPYPQGLBTJZJG)



```python
import collections
em_list = []
for tweet in tweets_text:
     em_list += findEmoticons(tweet)
em_counter = collections.Counter(em_list)
em_counter.most_common(20)
```

![截屏2021-01-28 下午5.06.02](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220022057.png?token=AWS37JO4TP6VLAAXMZCHE2DBTJZJI)





```python
from prettytable import PrettyTable
pt = PrettyTable(field_names=['Emoticons','Count'])
[pt.add_row(kv) for kv in em_counter.most_common()[:10]]
pt.align['Emoticon'], pt.align['Count'] = 'l', 'r'
print (pt)
```

![截屏2021-01-28 下午5.07.42](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220022728.png?token=AWS37JOEP2BXT7UI6J5GBETBTJZJ6)



```python
import numpy as np
emoticon_list = []
for tweet in tweets_text:
    em = findEmoticons(tweet)
    if len(em) is 0:
        em = np.nan
    emoticon_list.append(em)
tweets_pddf['emoticons'] = emoticon_list
#view tweet record in the dataframe, which contain at least one emoticon.
tweets_pddf.dropna(subset=['emoticons'])
```

![截屏2021-01-28 下午5.09.13](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-28 下午5.09.13.png)

