# System Recommender

Recommender systems encompass a class of techniques and algorithms which are able to suggest "relevant" items to users. Items are ranked according to their relevancy. The relevancy is something that the recommender system must determine and is mainly based on historical data.

## Basics

They are generally divided into 8 main categories: (1) collaborative filtering, (2) content-based filtering, (3) Multi-criteria recommender, (4) Risk-aware recommender, (5) Mobile recommender, (6) Hybrid recommender, (7) Session-based recommender, and (8) Reinforcement learning recommender systems.

### Collaborative Filtering Systems

It is method that are solely based on the past interactions between users and the target items. Thus, the input to a collaborative filtering system will be all historical data of user interactions with target items. This data is typically stored in a matrix where the rows are the users, and the columns are the items.

The core idea behind such systems is that the historical data of the users should be enough to make a prediction, i.e. we don't need anything more than the historical data, no extra push from the user or presently trending information, etc.

It is further divided into 2 sub groups: (1) Memory-based and (2) model-based.

#### Memory-based

The most simple since they do not use model whatsoever. The prediction comes from pure "memory" of past data, employed with a simple distance-measurement approach such as nearest neighbor.

Data collection can be done explicitly (users rating an item, searching, rank collection of items, etc.) or implicitly (observing items viewed, analyzing item/user viewing times, record of items purchased, social network, etc.)

A well-known approach is the user-based algorithm (UB-CF): https://medium.com/@cfpinela/recommender-systems-user-based-and-item-based-collaborative-filtering-5d5f375a127f.

##### User-Based Collaborative Filtering (UB-CF)

Recommend items by finding similar users to the active user by employing the Nearest Neighbor algorithm (https://en.wikipedia.org/wiki/K-nearest_neighbors_algorithm). 

1. Find the K-nearest neighbors (KNN) to the user a, using similarity function to measure the distance between each pair of users.
2. Predict the rating that user a will give to all items the k neighbors have consumed but a has not.

In other words, we are creating a user-item matrix, predicting the ratings on items the active user has not see, based on the other similar users.

Pros:
- Easy to implement
- Context independent
- Compared to other techniques, such as content-based, it is more accurate

Cons:
- Sparsity: the percentage of people who rate items is really low
- Scalability: the more K neighbors we consider, the better the classification. However, the more users, the greater the cost of finding the nearest K neighbors will be
- Cold-start: new users will have no to little information about them to be compared with other users.
- New item: new items will lack of ratings to create solid ranking.

##### Item-Based Collaborative Filtering (IB-CF)

Instead of focusing on similar users, focus on similar items.

1. Calculate similarity among the items:
  a. Cosine-based similarity
  b. Correlation-based similarity
  c. Adjusted cosine similarity
  d. 1-Jaccard distance
2. Calculation of prediction:
  a. Weighted sum
  b. Regression

The difference between UB-CF and IB-CF is that we directly pre-calculate the similarity between co-rated items, skipping KNN search.

#### Model-based

Always assume some kind of underlying machine learning model and basically try to make sure that predictions that came out fits the model well.

A well-known approach is the Kernel-Mapping Recommender: https://www.sciencedirect.com/science/article/abs/pii/S0020025512002587.

### Content-Based Systems

Use additional information about the user and or items to make predictions. For example, a content-based system might consider the age, sex, occupation, and other personal user factors when making the predictions.

This is why when you sign up for many online websites and services, they ask you to give your date of birth, gender, and ethnicity to better serve relevant content to you.

The system input is then the features of the user and the features of the item. The system output is the prediction whether or not the user would like or dislike the item.

Basically, these methods use an item profile (i.e., a set of discrete attributes and features) characterizing the item within the system. To abstract the features of the items in the system, an item presentation algorithm is applied. A widely used algorithm is the tf–idf (https://en.wikipedia.org/wiki/Tf%E2%80%93idf) representation (also called vector space representation). The system creates a content-based profile of users based on a weighted vector of item features. The weights denote the importance of each feature to the user and can be computed from individually rated item vector.

Machine learning techniques such as Bayesian Classifiers, cluster analysis, decision trees, and artificial neural networks can be used in order to estimate the probability taht the user is going to like the item.

One key issue with content-based filtering is whether the system is able to learn user preferences from users' actions regarding one content source and use them across other content types.

Content-based recommender systems can also include opinion-based recommender systems where item reviews can be fed into the system for it to generate improved metadata of the items.

### Multi-criteria recommender systems (MCRS)

Can be defined as recommender systems that incorporate preference information upon multiple criteria. Instead of recommending based on a single criterion value, the system uses the overall preference of the user to predict a rating for unexplored items.

For more in depth detail: https://web.archive.org/web/20140630021251/http://ids.csom.umn.edu/faculty/gedas/NSFCareer/MCRS-chapter-2010.pdf

### Risk-aware recommender systems

The approaches discussed above focus on recommending the most relevant content using contextual information, yet do not take into account the risk of disturbing the user with unwanted notifications. One option to manage this issue is DRARS, a system which models the context-aware recommendation as a bandit problem (https://en.wikipedia.org/wiki/Multi-armed_bandit). This system combines a content-based technique and a contextual bandit algorithm.

#### Bandit algorithm

Also known as Multi-armed bandit problem in probability theory. It is a problem in which a fixed limited set of resources must be allocated between competing (alternative) choices in a way that maximized their expected gain. This is a classic reinforcement learning problem that exemplifies the exploration-exploitation tradeoff dilemma.

The name comes from imagining a gambler at a row of slot machines, who has to decide which machines to play, home many times to play each machine and in which order to play them, and whether to continue with the current machine or different machine. The crucial tradeoff the gambler faces at each trial is between "exploitation" of the machine that has the highest expected payoff and "exploration" to get more information of the expected payoffs of the other machines.

https://en.wikipedia.org/wiki/Multi-armed_bandit
