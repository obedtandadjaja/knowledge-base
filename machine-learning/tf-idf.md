# Term Frequency - Inverse Document Frequency

A numerical statistic that is intended to reflect how important a word is to a document in a collection. It is often used as a weightin factor in searches of information retrieval, word mining and user modeling.

The tf-idf value increases proportionally to the number of times a word appears in the document and is offset by the number of documents in the collection that contain the word, which helps to adjust for the fact that some words appear more frequently in general.

tf-idf is one of the most popular weighting schemes today. A survey conducted in 2015 showed that 83% of text-based recommender systems in digital libraries use tf-idf.

One of the simplest ranking functions is computed by summing the tf-idf for each query term; many more sophisticated ranking functions are variants of this simple model.

## Motivations

### Term frequency

To best determine whether a particular document is more relevant than the other with respect to a certain word(s) query, a good approach is to count the frequency of these words in the document. The weight of a term that occurs in a document is simply proportional to the term frequency.

In the case where the length of documents varies greatly, adjustments are often made.

### Inverse document frequency

In the case of popular words such as "the", they can tend to incorrectly emphasize documents which happen to use the word "the" more frequently. The term "the" is not a good keyword to distinguish relevant and non-relevant documents and terms, unlike other less-common words.

The inverse document frequency factor is incorporated which dimished the weight of terms that occur very frequent in the document set and increases the weight of terms that occur rarely.

The specificity of a term can be quantified as an inverse function of the number of documents in which it occurs.

https://en.wikipedia.org/wiki/Tf%E2%80%93idf
