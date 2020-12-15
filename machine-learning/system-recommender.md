# System Recommender

Recommender systems encompass a class of techniques and algorithms which are able to suggest "relevant" items to users. Items are ranked according to their relevancy. The relevancy is something that the recommender system must determine and is mainly based on historical data.

## Basics

They are generally divided into 2 main categories: (1) collaborative filtering and (2) content-based systems.

### Collaborative Filtering Systems

It is method that are solely based on the past interactions between users and the target items. Thus, the input to a collaborative filtering system will be all historical data of user interactions with target items. This data is typically stored in a matrix where the rows are the users, and the columns are the items.

The core idea behind such systems is that the historical data of the users should be enough to make a prediction, i.e. we don't need anything more than the historical data, no extra push from the user or presently trending information, etc.

It is further divided into 2 sub groups: (1) Memory-based and (2) model-based.

#### Memory-based

The most simple since they do not use model whatsoever. The prediction comes from pure "memory" of past data, employed with a simple distance-measurement approach such as nearest neighbor.

#### Model-based

Always assume some kind of underlying machine learning model and basically try to make sure that predictions that came out fits the model well.

### Content-Based Systems

Use additional information about the user and or items to make predictions. For example, a content-based system might consider the age, sex, occupation, and other personal user factors when making the predictions.

This is why when you sign up for many online websites and services, they ask you to give your date of birth, gender, and ethnicity to better serve relevant content to you.

The system input is then the features of the user and the features of the item. The system output is the prediction whether or not the user would like or dislike the item.
