---
layout: post
title:  "Preparing text for Machine Learning"
date:   2019-09-15 23:25:04 -0500
categories: ml
---
There are plenty of tensorflow machine learning tutorials are about analyzing MNIST image dataset for classifying images. But what about text? How do we approach sparse datasets? How to imagine texts as numbers and why do we take that approach? I have tried to oversimplify the answers to these questions, the way I have understood things.
Any constructive feedback will be appreciated, any comments showing off your actual or fake theoretical expertise will also be appreciated.

This article will look like an FAQ. You have been warned.

#### What kinds of Machine Learning things can I do with text data?

- I have found some applications in clustering and searching texts, and predicting labels from text. I will be publishing another article on the same.

#### What kind of preparation is needed for texts? Why can’t we just use them as it is?

- We need to convert the text into numbers, because we make calculations with numbers, not words.
We can’t use them as it is because the text we are concerned about have varying lengths and not super standardized for any algorithm to understand random text efficiently.

#### Lets get on with it…

#### The mechanics of ML

- First we need to understand the mechanics of machine learning. It will be fast, don’t reach for that pillow yet.
We will use the example of a simple regression problem.
- In an oversimplified manner we try to figure out which set of coefficients when multiplied with certain numbers, give us approximates of the required outcome.
So lets say,
{% highlight python %}
2 * w1 + 5 * w2 = 27
7 * w1 + 3 * w2 = 23
{% endhighlight %}

`w1 and w2 are called weights and denoted by a vector w`

- This is a simple two variable two equation problem, which you can solve by hand, but we will need a lot of hands when we have to evaluate a bigger dataset (more equations) with larger number of features (more corresponding variables).
- Solving it using a proper deterministic method would require really insane machines (supercomputers maybe?). So Machine Learning techniques are basically heuristic methods to solve these problems (so that your tiny MacBook will still be okay to solve some relatively large problems).
- We will tell an algorithm to set w1=1 and w2=1 and start the solving process. In the solving process it will evaluate the LHS, compare it with RHS and figure out the difference. This difference is then summed up in different ways (called loss functions) to evaluate a measure called the Loss (duh uh!).
- One such loss function is the Sum Squared Error (SSE). For out initial starting condition the sum squared error will be `(27–(2*1+5*1))² + (23–(7*1+3*1))² =569`
- This is a huge loss, so the algorithm will adjust the w1 and w2 values a little to reduce this loss. This adjustment is done by a function called the Optimizer.
- The algorithm will keep on adjusting these values till w1 and w2 come somewhere close to (1, 5). That should give a tiny loss and model is now ready. Here our model is simply a sequence of numbers (0.99,5.1) or something like that. Any new data will go through a matrix multiplication with this sequence of numbers and will give out a result (the prediction!).

#### But that example had numbers in them already!

When we work on text data, we don’t have the values laid out for the algorithm to make the calculations and
`Swarup*w1 + Sahoo*w2 = sad`
won't make any sense.

#### So how do we make sense of it?

- By converting the words into numbers.

#### How do we do that?

- By performing multiple operations, which will include but not limited to:
1. Punctuation removal
2. Lower Case Conversion
3. Tokenization
4. Stemming
5. Lemmatization (kinda optional?)
6. Stop Word removal
7. Creating ngrams
8. Stripping accents
9. Creating a numerical vector using TF or TF-IDF

#### Punctuation removal

- For a lot of the text analysis we perform, punctuations don’t contribute much value. They might be relevant in other cases and you might choose to keep them.
- To remove punctuations we use a python library function called string and inbuilt function for str objects in python called translate.
{% highlight python %}
import string
#create a dictionary of characters to set to None
p_map = dict((ord(char),None) for char in string.punctuation)
text = "Oh wow! My life is boring..."
print(text.translate(p_map))
{% endhighlight %}
OUT:

`Oh wow My life is boring`

#### Lower Case Conversion

- For most analyses upper case letters are as useful as Captain Marvel in Endgame (I personally think she was pretty useless). So we will convert our text to lower case. So that ‘Dell’, ‘DELL’ and ‘dell’ will mean the same thing. To convert stuff to lower case we just use `str.lower()`
{% highlight python %}
text = "Oh wow! My life is boring..."
print(text.lower())
{% endhighlight %}
OUT:

`oh wow! my life is boring...`

#### Tokenization

- This is basically breaking a sequence of words (a phrase or sentence or some non-sensical sequence) into words (tokens). We can also use the tokenizer to break paragraphs into sentences or into individual letters.
{% highlight python %}
import nltk
nltk.download('punkt')
print(nltk.word_tokenize(text.lower().translate(p_map)))
{% endhighlight %}
OUT:

`['oh', 'wow', 'my', 'life', 'is', 'boring']`

We used a tokenizer called punkt.
- The documentation states:
`This tokenizer divides a text into a list of sentences by using an unsupervised algorithm to build a model for abbreviation words, collocations, and words that start sentences.  It must be trained on a large collection of plaintext in the target language before it can be used.`

- We don’t have to worry about training it cause we are using a pre-trained model. We now have a list of separate entities which can be identified individually.

#### Stemming

This operation trims the tokens to some sort of a root form where the entire meaning of the word is not lost.

{% highlight python%}
from nltk.stem.snowball import SnowballStemmer
stemmer = SnowballStemmer('english')
text = "Oh wow! My life is boring..."
def stem_tokens(tokens):
    return [stemmer.stem(item) for item in tokens]
stem_tokens(nltk.word_tokenize(text.lower().translate(p_map)))
{% endhighlight %}
OUT:

`['oh', 'wow', 'my', 'life', 'is', 'bore']`

- As we see, the word ‘boring’ has been converted to ‘bore’.
- For most known words it will convert the word to its root form.
- For unknown words it will try it’s best to trim it properly. For example if I change the word from ‘boring’ to ‘borring’ it will trim a lot more than expected.

{% highlight python %}
from nltk.stem.snowball import SnowballStemmer
stemmer = SnowballStemmer('english')
text = "Oh wow! My life is borring..."
def stem_tokens(tokens):
    return [stemmer.stem(item) for item in tokens]
stem_tokens(nltk.word_tokenize(text.lower().translate(p_map)))
{% endhighlight %}

OUT:

`['oh', 'wow', 'my', 'life', 'is', 'bor']`

- You might notice that the word was stemmed to ‘bor’ instead of ‘bore’.

#### Lemmatization

This is primarily an action to bring a broken stemmed word to its dictionary form. But as you might have noticed, the Snowball Stemmer is powerful enough for most of our stemming activities. So we will skip this bit.

#### Stop Word Removal

Stop words are frequently occurring words which do not contribute to the learning process for the algorithm. Words like a, an, for, is, etc are removed to keep our set of words a little more lean.
Note: This operation is an in-built capability for sklearn vectorizors. So we will bundle the code for it in the final step.

#### Creating ngrams

ngrams are simply phrases of n words (not the n word). A sequence of 2 words would be called a bigram, and three words a trigram and so on. Example:
{%highlight python%}
def ngrams(tokenized_text, n):
    grams = []
    for i in range(len(tokenized_text)-2):
        grams.append(' '.join(tokenized_text[i:i+n]))
    return grams
{% endhighlight %}
OUT:

`['oh wow my', 'wow my life', 'my life is', 'life is bore']`

- As you can see, there are overlapping words in each gram. That’s because we are trying to cover all word sequences.

#### Stripping Accents

- This one’s easy, we just gotta kill the French and welcome the plain bland English. So, ‘déll’ will become ‘dell’.
- This action can be performed using the strip_accents parameter in sklearn’s vectorizer functions.
Note: This operation is an in-built capability for sklearn vectorizors. So we will bundle the code for it in the final step.

#### Creating a numerical vector using TF or TFIDF

#### TF (Term Frequency) Vectorization

- Here we take a look at every word or ngram we have identified in a sentence and count the number of times it occurs in it. Then we divide the count by total number of words/ngrams in the sentence.
- So, the the term frequency for the word ‘dell’ will be in the sentence ‘Dell laptops suck! and I want to throw my dell laptop away…’ would be (2/12) (assuming that we haven’t done any of the prior cleanup). The Term frequency for ‘laptop away’ would be (1/11).
- Now we start to see how the text is getting converted to values. A csv might look like this (after some cleanup of words… [‘I’, ‘to’, ‘my’, ‘and’] removed and ‘laptops’ stemmed to laptop)
{% highlight python %}
dell,  laptop, suck,  want,  any,   away
0.25,  0.25,   0.125, 0.125, 0.125, 0.125
{% endhighlight %}

#### TF-IDF(Term Frequency-Inverse Document Frequency) Vectorization

- It is an extension of the TF method. It takes another measure called IDF, which evaluates the weight of the word in relation to the entire dataset we have.
- The formula is

`log(total number of sentences/count of sentences which contain a word)`

- You might notice that we are not concerned about how many times a word occurs in a sentence but rather we just check the sentences containing a specific word.
- An IDF is constant per corpus, and accounts for the ratio of documents that include a specified word. If we have a corpus of two thousand documents and all of them include the word “this”. Then the IDF of that word would be

`log(2000/2000) = log(1) = 0`

- The implication is that the word “this” is useless for us, because it’s repeated everywhere, so it won’t give us any differenciating characteristic.
- Now we will see how to implement the full code:

{% highlight python %}
import string
import nltk
from nltk.stem.snowball import SnowballStemmer
from sklearn.feature_extraction.text import TfidfVectorizer
nltk.download('punkt')
stemmer = SnowballStemmer('english')
p_map = dict((ord(char),None) for char in string.punctuation)
def stem_tokens(tokens):
    return [stemmer.stem(item) for item in tokens]
def normalize(text):
    return stem_tokens(nltk.word_tokenize(text.lower().translate(p_map)))
a = "oh wow dell sucks so much, dell is the worst oh wow"
b = "dell laptops are so good, much better than those dirt cheap macbooks"
tfidf = TfidfVectorizer(stop_words='english', use_idf=True, ngram_range=(1,3), strip_accents='unicode', tokenizer=normalize)
tfidf_score = tfidf.fit_transform([a, b])
def matrix_to_list(matrix):
    matrix = matrix.toarray().round(decimals=3)
    return matrix.tolist()
score_list = matrix_to_list(tfidf_score)
print(score_list)
{% endhighlight %}
OUT: (manually spaced out for better viewing)
{% highlight python %}
[
[0.0, 0.0, 0.0, 0.0, 0.0, 0.274, 0.0, 0.0, 0.192, 0.192, 0.192, 0.192, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.385, 0.385, 0.192, 0.192, 0.192, 0.192, 0.192, 0.192, 0.192, 0.385, 0.192, 0.192],
[0.239, 0.239, 0.239, 0.239, 0.239, 0.17, 0.239, 0.239, 0.0, 0.0, 0.0, 0.0, 0.239, 0.239, 0.239, 0.239, 0.239, 0.239, 0.239, 0.239, 0.239, 0.239, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]
]
{% endhighlight %}

- And that’s how I̶ ̶m̶e̶t̶ ̶y̶o̶u̶r̶ ̶m̶o̶t̶h̶e̶r̶ I converted my texts to numbers.

- Next up… running a tensorflow classification on text data.
