# Basic Text Pre-Processing Technologies

The aim of this tutorial is to demonstrate the basic technologies used to pre-process text data in the text mining, Information Retrieval (IR) and Natural Language Processing (NLP) communities. Those technologies include

- Tokenizing text
- Removing stop words
- Stemming & Lemmatization
- Sentence segmentation

The ultimate goal of pre-processing text is to convert unstructured and free language text into structured data so that text analysis algorithms can directly take the structured data as input. For example, the UCI machine learning database provides free download of the bag-of-words datasets that contain ENRON emails, NIPS articles, the New York Times news articles, [PubMed](https://www.ncbi.nlm.nih.gov/pubmed) articles. Those are the bench mark datasets used in text analysis. Lets have a look at one of the datasets, PubMed. The image below shows a screenshot of the first 15 lines in the data set

```python
raw_text = """Previous right upper lobe nodule? Fungal question resolution change. Findings: Comparison is made to prior CT dated November 30, 2004. Significant resolution in the previously noted fluid overload status. Ectasia of the thoracic aorta measuring 4.2 cm. Features of generalised centrilobular emphysema. Resolution of right upper lobe nodule. There is now presence of a nodule within the medial segment of the right lower lobe which measures 5.4 mm and is non-specific in nature. Given the interval development of this fungal/inflammatory aetiology is likely. There is a 13 mm right axillary node which is a new finding since the prior study. No significant mediastinal or hilar adenopathy. Conclusion: Nodule in the right lower lobe in keeping with fungal/inflammatory aetiology."""
raw_text = raw_text.lower()
```

## 1. Word Tokenization

```python
from nltk.tokenize import RegexpTokenizer
tokenizer = RegexpTokenizer(r"\w+(?:[-.]\w+)?")
unigram_tokens = tokenizer.tokenize(raw_text)
print(unigram_tokens)
```

![截屏2021-01-28 上午11.26.26](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8A%E5%8D%8811.26.26.png?token=AWS37JIOOBM4IMYTKP4WHPDBTIBZU)

```python
uni_voc = list(set(unigram_tokens))
uni_voc.append(('generalised', 'centrilobular', 'emphysema'))
uni_voc.append(('inflammatory', 'aetiology'))
uni_voc.append(('hilar', 'adenopathy'))
uni_voc.append(('lobe', 'nodule'))
uni_voc.append(('axillary', 'node'))
uni_voc.append(('thoracic', 'aorta', 'measuring'))
```



```python
from nltk.tokenize import MWETokenizer

mwe_tokenizer = MWETokenizer(uni_voc)
mwe_tokens = mwe_tokenizer.tokenize(unigram_tokens)
print(mwe_tokens)
```

![截屏2021-01-28 上午11.32.42](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8A%E5%8D%8811.32.42.png?token=AWS37JMYILT7W3VRGRAMRS3BTIBZY)

## 2. Stop Words Removal

```python
from nltk.corpus import stopwords
stopwords_list = stopwords.words('english')
stopwords_set = set(stopwords_list)
stopwords_set.add('ct')
stopwords_set.add('mm')
stopwords_set.add('cm')
stopwords_set.add('fungal')
stopwords_set.add('conclusion')
stopped_tokens = [ w for w in mwe_tokens if w not in stopwords_set]
```



## 3. Stemming, Lemmatization, setence segmentation and POS tagging

```python
import nltk.data
sent_detector = nltk.data.load('tokenizers/punkt/english.pickle')
### write your code below
sentences = sent_detector.tokenize(raw_text.strip())
for sent in sentences:
    print (sent)
```

![截屏2021-01-28 上午11.57.48](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-28 上午11.57.48.png)

```python
tagged_sents = []
for sent in sentences:
  	uni_sent = tokenizer.tokenize(sent)
    mwe_text = mwe_tokenizer.tokenize(uni_sent)
    tagged_sent = nltk.tag.pos_tag(mwe_text)
    stopped_tagged_sent = [x for x in tagged_sent if x[0] not in stopwords_set]
    tagged_sents.append(stopped_tagged_sent)
    
print(tagged_sents)
```

![截屏2021-01-28 下午12.22.42](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-28 下午12.22.42.png)





```python
# The code in this cell is adapted from the following website
# http://stackoverflow.com/questions/15586721/wordnet-lemmatization-and-pos-tagging-in-python
#
from nltk.corpus import wordnet

def get_wordnet_pos(treebank_tag):

    if treebank_tag.startswith('J'):
        return wordnet.ADJ
    elif treebank_tag.startswith('V'):
        return wordnet.VERB
    elif treebank_tag.startswith('N'):
        return wordnet.NOUN
    elif treebank_tag.startswith('R'):
        return wordnet.ADV
    else:
        return wordnet.NOUN
```



```python
from nltk.stem import WordNetLemmatizer
lemmatizer = WordNetLemmatizer()
final_tokens =[]
### write your code below
for tagged_set in tagged_sents:
    final_tokens = final_tokens + [lemmatizer.lemmatize(w[0], get_wordnet_pos(w[1])) for w in tagged_set ]
```

```python
from nltk.stem import PorterStemmer
stemmer = PorterStemmer()
final_tokens =[]
### write your code below
for tagged_set in tagged_sents:
    final_tokens = final_tokens + [stemmer.stem(w[0]) for w in tagged_set ]
```

