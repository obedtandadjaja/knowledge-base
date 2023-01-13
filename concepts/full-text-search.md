# Full Text Search

Full Text Searching (or just text search) provides the capability to identify natural-language documents that satisfy a query, and optionally to sort them by relevance to the query. 

The most common type of search is to find all documents containing given query terms and return them in order of their similarity to the query. Notions of query and similarity are very flexible and depend on the specific application. 

Full text indexing allows documents to be preprocessed and an index saved for later rapid searching. Preprocessing includes:

- Parsing documents into tokens. It is useful to identify various classes of tokens, e.g., numbers, words, complex words, email addresses, so that they can be processed differently.

- Converting tokens into lexemes. A lexeme is a string, just like a token, but it has been normalized so that different forms of the same word are made alike. 
  - For example, normalization almost always includes folding upper-case letters to lower-case, and often involves removal of suffixes (such as s or es in English). This allows searches to find variant forms of the same word, without tediously entering all the possible variants. 
  - Also, this step typically eliminates stop words, which are words that are so common that they are useless for searching. (In short, then, tokens are raw fragments of the document text, while lexemes are words that are believed useful for indexing and searching.) 
  - Language-specific stemmers are used to do this.

- Storing preprocessed documents optimized for searching. 
  - Each document can be represented as a sorted array of normalized lexemes.
  - Store positional information to use for proximity ranking, so that a document that contains a more “dense” region of query words is assigned a higher rank than one with scattered query words.

Dictionaries are also used to allow fine-grained control over how tokens are normalized:

- Define stop words that should be ignored.
- Map synonyms using Ispell (https://en.wikipedia.org/wiki/Ispell).
- Map phrases to a single word using thesaurus.
- Map words to its base form.

## Vector representation

Data structure: `Map<String, List<String>>`. The key is the lexeme, and the value is the list of positional indexes of the words followed by weights, used for proximity ranking and weighted search.

The position is represented as string, while the weight is represented as A, B, C, and D. The format is the following `{position}{weight || 'D'}`. D is the default hence it is not shown in the output below.

```
'a':1,6,10 'and':8 'ate':9 'cat':3A 'fat':2B,11 'mat':7 'on':5 'rat':12A 'sat':4C
```

## Lingo

- Document: the unit of searching in a full-text search system.