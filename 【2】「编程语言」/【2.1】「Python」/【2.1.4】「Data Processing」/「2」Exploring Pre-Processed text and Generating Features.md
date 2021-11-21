## 1. Counting Vocabulary by Selecting Tokens of Interest

```python
import matplotlib as plt
@matplotlib inline
import nltk
from nltk.corpus import reuters
```

```python
def tokenizeRawData(fileid):
  	raw_article = reuters.raw(fileid).lower() # cover all words to lowercase
    tokenised_article = nltk.tokenize.word_tokenize(raw_article) # tokenize each Reuters articles
    return (fileid, tokenised_article) 
```



```python
import multiprocessing as mp
pool = mp.Pool(processes=4) # Build a pool of 4 processess 
tokenized_reuters = dict(pool.map(tokenizeRawData, reuters.fileids()))
#tokenized_reuters =  dict(tokenizeRawData(fileid) for fileid in reuters.fileids())
```

### 1.1. Removing Words with Non-alphabetic Characters

The NLTK's built-in word_tokenize function tokenizes a string to split off punctuation other than periods. Not only does it return words with alphanumerical characters, but also punctuations. Let's take a look at one Reuters articles,

```python
for key, value in tokenized_reuters.items():
  	tokenized_reuters[key] = [word for word in value if word.isalpha()]
```



```python
from __future__ import division
from itertools import chain

words = list(chain.from_iterable(tokenized_reuters.values()))
vocab = set(words)
lexical_diversity = len(words)/len(vocab)
print ("Vocabulary size: ",len(vocab),"\nTotal number of tokens: ", len(words), \
"\nLexical diversity: ", lexical_diversity)
```

![截屏2021-01-28 下午2.30.32](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-28 下午2.30.32.png)

### 1.2. Removing the Most and Less Frequent Words

```python
from nltk.probability import *
fd_1 = FreqDist(words)
```

```python
fd_1.plot(25, cumulative=True)
```

![截屏2021-01-28 下午2.44.58](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-28 下午2.44.58.png)

```python
words_2 = list(chain.from_iterable([set(value) for value in tokenized_reuters.values()]))
fd_2 = FreqDist(words_2)
```

#### Ignoring Stopwords

As discussed in section 3 of chapter 1, we often remove function words from the text completely for most text analysis tasks. Instead of using the built-in stopword list of NLTK, we use a much rich stopword list that has been downloaded in Chapter 2.

```python
stopwords = []
with open('./stopwords_en.txt') as f:
    stopwords = f.read().splitlines()
    #stopwords = f.read().split('\n')
```

```python
tokenized_reuters_1 = {}
for fileid in reuters.fileids():
    tokenized_reuters_1[fileid] = [w for w in tokenized_reuters[fileid] if w not in stopwords]
```

```python
stopwordsSet = set(stopwords)
for fileid in reuters.fileids():
    tokenized_reuters[fileid] = [w for w in tokenized_reuters[fileid] if w not in stopwordsSet]
```

```python
words_3 = list(chain.from_iterable(tokenized_reuters.values()))
fd_3 = FreqDist(words_3)
list(vocab - set(fd_3.keys()))
```

#### Remove Less Frequent Words

```python
ffd = FreqDist(fd_3.values())
from pylab import *
y = [0]*14
for k, v in ffd.items():
    if k <= 10:
        y[k-1] = v
    elif k >10 and k <= 50:
        y[10] =  y[10] + v
    elif k >50 and k <= 100:
        y[11] =  y[11] + v
    elif k > 100 and k <= 500:
        y[12] =  y[12] + v
    else:
        y[13] =  y[13] + v
x = range(1, 15) # generate integer from 1 to 14
ytks =list(map(str, range(1, 11))) # covert a integer list to a string list
ytks.append('10-50')
ytks.append('51-100')
ytks.append('101-500')
ytks.append('>500')
barh(x,y, align='center')
yticks(x, ytks)
xlabel('Frequency of Frequency')
ylabel('Word Frequency')
grid(True)
```

![截屏2021-01-28 下午4.48.53](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-28 下午4.48.53.png)

```python
lessFreqWords = set(fd_3.hapaxes())

def removeLessFreqWords(fileid):
    return (fileid, [w for w in tokenized_reuters[fileid] if w not in lessFreqWords])
```

```python
tokenized_reuters = dict(removeLessFreqWords(fileid) for fileid in reuters.fileids())
```

```python
import numpy as np
words = list(chain.from_iterable(tokenized_reuters.values()))
vocab = set(words)
print ("Vocabulary size: ",len(vocab))
print ("Total number of tokens: ", len(words))
print ("Lexical diversity: ", lexical_diversity)
print ("Total number of articles:", len(tokenized_reuters))
lens = [len(value) for value in tokenized_reuters.values()]
print ("Average document length:", np.mean(lens))
print ("Maximun document length:", np.max(lens))
print ("Minimun document length:", np.min(lens))
print ("Standard deviation of document length:", np.std(lens))
```

![截屏2021-01-28 下午4.49.44](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-28 下午4.49.44.png)

## 3. Saving Pre-processed Text to a File

```python
out_file = open("./reuters_1.txt", 'w')
for d in tokenized_reuters.values():
    out_file.write(' '.join(d) + '\n')
out_file.close()
```

```python
out_file = open("./reuters_2.txt", 'w')
vocab = list(vocab)

vocab_dict = {}
i = 0
for w in vocab:
    vocab_dict[w] = i
    i = i + 1

for d in tokenized_reuters.values():
    d_idx = [vocab_dict[w] for w in d]
    for k, v in FreqDist(d_idx).items():
        out_file.write("{}:{} ".format(k,v))
    out_file.write('\n')
out_file.close()
```

