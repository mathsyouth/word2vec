#Notice

The word2vec algorithms has been ported to the TeensorFlow architecture. See:
https://www.tensorflow.org/versions/master/tutorials/word2vec/index.html

Below is the deprecated word2vec implementation.

##Introduction

This tool provides an efficient implementation of the continuous bag-of-words and skip-gram architectures for computing vector representations of words. These representations can be subsequently used in many natural language processing applications and for further research.

##Quick start
* Run 'make' to compile word2vec tool
* Run the demo scripts: ./demo-word.sh and ./demo-phrases.sh
* For questions about the toolkit, see http://groups.google.com/group/word2vec-toolkit

##How does it work
The word2vec tool takes a text corpus as input and produces the word vectors as output. It first constructs a vocabulary from the training text data and then learns vector representation of words. The resulting word vector file can be used as features in many natural language processing and machine learning applications.

A simple way to investigate the learned representations is to find the closest words for a user-specified word. The distance tool serves that purpose. For example, if you enter 'france', distance will display the most similar words and their distances to 'france', which should look like:

```bash
                 Word       Cosine distance
-------------------------------------------
                spain              0.678515
              belgium              0.665923
          netherlands              0.652428
                italy              0.633130
          switzerland              0.622323
           luxembourg              0.610033
             portugal              0.577154
               russia              0.571507
              germany              0.563291
            catalonia              0.534176
```

There are two main learning algorithms in word2vec : continuous bag-of-words and continuous skip-gram. The switch -cbow allows the user to pick one of these learning algorithms. Both algorithms learn the representation of a word that is useful for prediction of other words in the sentence. These algorithms are described in detail in [1,2].

##Interesting properties of the word vectors
It was recently shown that the word vectors capture many linguistic regularities, for example vector operations vector('Paris') - vector('France') + vector('Italy') results in a vector that is very close to vector('Rome'), and vector('king') - vector('man') + vector('woman') is close to vector('queen') [3, 1]. You can try out a simple demo by running demo-analogy.sh.

##How to measure quality of the word vectors
Several factors influence the quality of the word vectors:
* amount and quality of the training data
* size of the vectors
* training algorithm

The quality of the vectors is crucial for any application. However, exploration of different hyper-parameter settings for complex tasks might be too time demanding. Thus, we designed simple test sets that can be used to quickly evaluate the word vector quality.

For the word relation test set described in [1], see ./demo-word-accuracy.sh, for the phrase relation test set described in [2], see ./demo-phrase-accuracy.sh. Note that the accuracy depends heavily on the amount of the training data; our best results for both test sets are above 70% accuracy with coverage close to 100%.

##Word clustering
The word vectors can be also used for deriving word classes from huge data sets. This is achieved by performing K-means clustering on top of the word vectors. The script that demonstrates this is ./demo-classes.sh. The output is a vocabulary file with words and their corresponding class IDs, such as:

```shell
carnivores 234
carnivorous 234
cetaceans 234
cormorant 234
coyotes 234
crocodile 234
crocodiles 234
crustaceans 234
cultivated 234
danios 234
.
.
.
acceptance 412
argue 412
argues 412
arguing 412
argument 412
arguments 412
belief 412
believe 412
challenge 412
claim 412
```

##Performance
The training speed can be significantly improved by using parallel training on multiple-CPU machine (use the switch '-threads N'). The hyper-parameter choice is crucial for performance (both speed and accuracy), however varies for different applications. The main choices to make are:
* architecture: skip-gram (slower, better for infrequent words) vs CBOW (fast)
* the training algorithm: hierarchical softmax (better for infrequent words) vs negative sampling (better for frequent words, better with low dimensional vectors)
* sub-sampling of frequent words: can improve both accuracy and speed for large data sets (useful values are in range 1e-3 to 1e-5)
* dimensionality of the word vectors: usually more is better, but not always
* context (window) size: for skip-gram usually around 10, for CBOW around 5

##Where to obtain the training data
The quality of the word vectors increases significantly with amount of the training data. For research purposes, you can consider using data sets that are available on-line:
* [First billion characters from wikipedia](http://mattmahoney.net/dc/enwik9.zip) (use the pre-processing perl script from the bottom of [Matt Mahoney's page](http://mattmahoney.net/dc/textdata.html))
* [Latest Wikipedia dump](http://dumps.wikimedia.org/enwiki/latest/enwiki-latest-pages-articles.xml.bz2) Use the same script as above to obtain clean text. Should be more than 3 billion words.
* [WMT11 site](http://www.statmt.org/wmt11/translation-task.html#download): text data for several languages (duplicate sentences should be removed before training the models)
* [Dataset from "One Billion Word Language Modeling Benchmark"](http://www.statmt.org/lm-benchmark/1-billion-word-language-modeling-benchmark-r13output.tar.gz) Almost 1B words, already pre-processed text.
* [UMBC webbase corpus](http://ebiquity.umbc.edu/redirect/to/resource/id/351/UMBC-webbase-corpus) Around 3 billion words, more info [here](http://ebiquity.umbc.edu/blogger/2013/05/01/umbc-webbase-corpus-of-3b-english-words/). Needs further processing (mainly tokenization).
* Text data from more languages can be obtained at [statmt.org](http://statmt.org/) and in the [Polyglot project](https://sites.google.com/site/rmyeid/projects/polyglot#TOC-Download-Wikipedia-Text-Dumps).

##Pre-trained word and phrase vectors
We are publishing pre-trained vectors trained on part of Google News dataset (about 100 billion words). The model contains 300-dimensional vectors for 3 million words and phrases. The phrases were obtained using a simple data-driven approach described in [2]. The archive is available here: [GoogleNews-vectors-negative300.bin.gz](https://drive.google.com/file/d/0B7XkCwpI5KDYNlNUTTlSS21pQmM/edit?usp=sharing).

An example output of ./distance GoogleNews-vectors-negative300.bin:

```bash
Enter word or sentence (EXIT to break): Chinese river

                Word       Cosine distance
------------------------------------------
       Yangtze_River              0.667376
             Yangtze              0.644091
      Qiantang_River              0.632979
   Yangtze_tributary              0.623527
    Xiangjiang_River              0.615482
       Huangpu_River              0.604726
      Hanjiang_River              0.598110
       Yangtze_river              0.597621
         Hongze_Lake              0.594108
             Yangtse              0.593442
```

The above example will average vectors for words 'Chinese' and 'river' and will return the closest neighbors to the resulting vector. More examples that demonstrate results of vector addition are presented in [2]. Note that more precise and disambiguated entity vectors can be found in the following dataset that uses Freebase naming.
