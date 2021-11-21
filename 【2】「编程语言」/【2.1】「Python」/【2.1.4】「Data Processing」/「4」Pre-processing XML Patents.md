# Pre-processing XML Patents

## 1. Introduction

This tutorial forcus on preprocessing a set of patents documents stored in XML format and generating the sparse representations for those patents. The final output file should be exactly the same as the one stored in "patents.txt".

In order to finish this task, you should

1. Exatract the abstract and claims for each patent from its xml file. Use Beautiful soup
2. Tokenise the patents
3. Generate 100 bigram collocations
4. Re-tokenize the patents with those bigram collocations
5. Generate the TF-IDF vectors for those re-tokenized patents
6. save the vectors in the form shown in "patents.txt"

## 2. Import libraries

Here we will focus on using the existing packages as possible as we can

```python
from bs4 import BeautifulSoup as bsoup
import re
import os
import nltk
from nltk.collocations import *
from itertools import chain
import itertools
from nltk.tokenize import RegexpTokenizer
from nltk.tokenize import MWETokenizer 
```



## 3. Exatract Patent's abstract and Claims

The first task is to parse each patent stored in the "xml_files" folder. The information to be extracted includes

1. patent document number (doc-number) stored in "publication-reference"
2. patent's abstract
3. patent's claims

Hint: you can use a dictionary to save patents, where the key is the doc-number, the value is a long string contains both abstracts and all claims.

```python
xml_file_path = "./xml_files"
```

```python
def parsing(t):

    xmlSoup = bsoup(t,"lxml-xml")
    
    pid = xmlSoup.find("publication-reference").find('doc-number').string 
    
    text = ""
    
    #Extract text in "abstract"    
    abt = xmlSoup.find('abstract')
    for p in abt.findAll('p'):
        text = text + p.text + " "
    
    #Extract Claims 
    for tag in xmlSoup.find_all('claim-text'):
        text = text + tag.text
        
    return (pid, text)
```

```python
patents_raw = {}
for xfile in os.listdir(xml_file_path): 
    xfile = os.path.join(xml_file_path, xfile)
    if os.path.isfile(xfile) and xfile.endswith('.XML'): 
        (pid, text) = parsing(open(xfile))
        patents_raw[pid] = text
```



## 4. Tokenize the patents[¶](http://localhost:8888/notebooks/Desktop/FIT5196 Data Wrangling/week 5/Tutorial/tutorials/tutorial_05_answer-1.ipynb#4.-Tokenize-the-patents)

After finish extract the texts, you now need to tokenize the patents with regular expression tokenizer implemented in NLTK.

```python
tokenizer = RegexpTokenizer(r'[a-zA-Z]{2,}') 
```

```python
def tokenizePatent(pid):
    """
        the tokenization function is used to tokenize each patent.
        The one argument is patent_id.
        First, normalize the case.
        Then, use the regular expression tokenizer to tokenize the patent with the specified id
    """
    raw_patent = patents_raw[pid].lower() 
    tokenized_patents = tokenizer.tokenize(raw_patent)
    return (pid, tokenized_patents) # return a tupel of patent_id and a list of tokens

  
patents_tokenized = dict(tokenizePatent(pid) for pid in patents_raw.keys())  
```

## 5. Generate the 100 bigram collocations

The next task is go generate the bigram collocations, given the tokenized patents.

The first step is to concatenate all the tokenized patents using the chain.frome_iterable function. The returned list by the function contains a list of all the words seprated by white space.

```python
all_words = list(chain.from_iterable(patents_tokenized.values()))
```



The second step is to generate the 100 bigram cllocations. The functions you need include

- BigramAssocMeasures()
- BigramCollocationFinder.from_words()
- apply_freq_filter(20)
- apply_word_filter(lambda w: len(w) < 3)
- nbest(bigram_measures.pmi, 100)

Please do not change the parameters given in the last three function. More information about generating collocation with NLTK can be found http://www.nltk.org/howto/collocations.html.

```python
bigram_measures = nltk.collocations.BigramAssocMeasures()
bigram_finder = nltk.collocations.BigramCollocationFinder.from_words(all_words)
bigram_finder.apply_freq_filter(20)
bigram_finder.apply_word_filter(lambda w: len(w) < 3)# or w.lower() in ignored_words)
top_100_bigrams = bigram_finder.nbest(bigram_measures.pmi, 100) # Top-100 bigrams
```



## 6. Re-tokenize the patents again.

Task in Section 4 takenise the patents with only unigrams. Now, we introduce 100 collcations. we need to make sure those collocations are not split into two individual words. The tokenizer that you need is [MWEtokenizer](http://www.nltk.org/api/nltk.tokenize.html).

```python
mwetokenizer = MWETokenizer(top_100_bigrams)
#mwe_tokens = mwetokenizer.tokenize()
colloc_patents =  dict((pid, mwetokenizer.tokenize(patent)) for pid,patent in patents_tokenized.items())
all_words_colloc = list(chain.from_iterable(colloc_patents.values()))
colloc_voc = list(set(all_words_colloc))
#colloc_voc
```



## 7. Generate the TF-IDF vectors for all the patents.

```python
pids = []
patent_words = []
for pid, tokens in colloc_patents.items():
    pids.append(pid)
    txt = ' '.join(tokens)
    patent_words.append(txt)
```

```python
from sklearn.feature_extraction.text import TfidfVectorizer
tfidf_vectorizer = TfidfVectorizer(input = 'content', analyzer = 'word')
tfidf_vectors = tfidf_vectorizer.fit_transform(patent_words)
tfidf_vectors.shape
```



## 8. Save the TF-IDF vector into the specified format

Hint: you can use

- the [tocoo()](https://docs.scipy.org/doc/scipy-0.14.0/reference/generated/scipy.sparse.csc_matrix.tocoo.html) function
- itertools.zip_longest()

```python
save_file = open("patent_student.txt", 'w')

vocab = tfidf_vectorizer.get_feature_names()
#########please write the missing code below#######
cx = tfidf_vectors.tocoo() # return the coordinate representation of a sparse matrix
for i,j,v in itertools.izip_longest(cx.row, cx.col, cx.data):
    save_file.write(pids[i] + ',' + vocab[j] + ',' + str(v) + '\n')
    
save_file.close()
```





























