# Classifying oranges vs. grapefruit

Consider the problem of determining whether a fruit is either an orange or a grapefruit. Since the two look different, we can probably come up with a simple and effective way of classifying them by means of their color and size. Oranges will tend to be smaller and more orange, and grapefruits will tend to be larger and redder.

The problem is now how to choose the boundaries of the classification system (i.e., how much "red" is "red enough" to be considered a grapefruit). For this, we can use the *k-nearest neighbor* (KNN) algorithm, which classifies new instances of data based on a consensus of their nearest *k* neighbors. In this case, we would consider "distance" based on two features, size and color.

# Building a recommendation system

Let us how consider an apparently very different problem: a recommendation system which suggests movies that users might like. At a high level, we can frame the problem as something very similar to the classification problem: if two (or more) users have similar tastes, they will probably like the same movies too! (if two fruits have similar size and color...)

In the previous example, we used features, such as size and color, to compute a measure of *similarity*. How can we figure out whether the tastes in movies of two people are similar?

We have to find a way to represent movie watchers. We could take the average rating that they gave to movies of different genres, and thus express how much they like comedy, action, drama, horror and romance, as a number between 0 (not at all) and 10 (they love it). Here, we would be representing each data point (user) in a 5-dimensional space. However much complicated that would be to represent (check out PCA!), computing the distance between vectors in any N-dimensional space is straightforward, since we can simply apply the euclidean distance formula

$$d = \sqrt{\sum_{i=1}^{n} (x_i - y_i)^2}$$

With this in mind, recommending movies simply involves finding users with similar tastes and then suggesting the movies that those users have liked but the target user has not watched yet!

# Regression

We have framed the task of movie recommendation as a classification task (will the user like this movie or not?). With KNN, we can also do regression though (how much will the user like this movie?).

The principle is similar, in that we use the information of the neighbours to interpolate the rating of the target user (we could take the average rating that neighbors gave to a given movie).

Another example: we could want to estimate how many products (food items, for instance) we should prepare for a given day. We could estimate this based on how many items we sold on similar days (weather, weekday, month...) by computing the average of items sold in days that share features with that day.

***
**Cosine similarity**

Besides euclidean distance, a popular similarity metric is the cosine similarity. Instead of measuring the distance between two vectors, it compares their angle. It is better at dealing with situations where one of the vectors is significantly longer than the other. KNN can be run with cosine similarity metric, instead of the euclidean distance!
***

# Picking good features

When working with KNN, it is really important to pick the right features for the task at hand. This entails:
* Features that directly correlate to the recommendation system
* Features that do not have a bias (data is uniformly distributed across categories)

This is not a trivial problem, and it can take an iterative effort to find the most effective features.

# Introduction to machine learning

KNN is an example of a machine learning (ML) algorithm. In broad term, machine learning is the study and design of algorithms that are able to learn the properties of complex data, and thus are able to perform a variety of tasks, such as classification and regression.

The classical example is OCR or *optical character recognition*. This is the task of detecting the character which is *represented* in a computer image. These characters could be from pictures, video, hand-drawn, machine-generated... It is a very easy problem to understand, but writing a computer program to solve it is not trivial. Instead, we can apply many types of ML techniques to labelled data (this is, examples of picture-character pairs) in order to learn how to classify them. A similar idea can be broadly applied to speech- or face- recognition problems.

Another typical example is spam filters. Many email programs have filters to detect spam emails. These filters can be learned and tuned through example, thus becoming better at their job, and tailored to the user.

Predicting the stock market is a task which does not quite work with classical ML (or any) algorithms, since there is no guarantee that past performance is a good predictor for future stock market values.

# A high-level overview of training an ML model

This is a broad overview of a typical pipeline to train an ML model:
* We start by gathering data, which we think can be used to solve the problem at hand
* We clean the data, by checking its shape, dealing with missing/wrong values, and so on
* We train our model, or better yet, a variety of them (KNN, SVM, neural networks) on something like 80% of our data
* We evaluate the models (and their hyperparameters) on another 10% of the data, and we choose the best one
* To estimate the real-world performance of the model, we validate it by applying it to the remaining 10% of the data

It is important to check that there is not *data bleed*, this is, a case where by separating data in a particular way, there is some information which can *leak* from the validation dataset to the training.

**Recap**
* KNN is used for classification and regression and involves looking at the k-nearest neighbors.
* Classification $\rightarrow$ categorization into a group
* Regression $\rightarrow$ predicting a feature/value
* Feature extraction means representing an item into a numerical vector
* Picking good features is an important part of a succesful ML algorithm

# Exercises

**12.1**: In the Netflix example, you calculated the distance between two different users using the distance formula. But not all users rate movies the same way. Suppose you have two users, Yogi and Pinky, who have the same taste in movies. But Yogi rates any movie he
likes as a 5, whereas Pinky is choosier and reserves the 5s for only the best. They’re well matched, but according to the distance algorithm, they aren’t neighbors. How would you take their different rating strategies into account?

**Answer**: The problem here is that both users show the same tendency towards rating some categories higher than others, but on *average*, one of them tends to rate everything higher. Here, we could attempt data *normalization*, where we try to preserve the overall structure of the data while removing sources of bias between how the users rate the movies. For example, we could divide the 5-dimensional vector by the total numbers of points that they assigned to each movie genre.

**12.2**: Suppose Netflix nominates a group of “influencers.” For example, Quentin Tarantino and Wes Anderson are influencers on Netflix, so their ratings count for more than a normal user’s. How would you change the recommendations system so it’s biased toward the
ratings of influencers?

**Answer**: We could add a term to the distance function which weighs the contribution of certain influencer data points higher than other users. For example, we could introduce a term which effectively *reduces* the distance between a target user and an influencer. In other words, if the user is *somewhat* aligned with the preferences of Wes Anderson, but *very* aligned to some people who have no idea about film making, perhaps we should recommend what Wes Anderson likes!

**12.3**: Netflix has millions of users. The earlier example looked at the five closest neighbors for building the recommendations system. Is this too low? Too high?

**Answer**: The balance we are trying to strike is between noisy classification (taking a handful of users might be not robust to small variations in the data, for example over time), and simply returning and average of the whole platform (very robust, but useless). We could attempt to graph the classifications as a function of the k neighbours, or we could rank each recommendation by robustness (how many of the neighbors that are being considered agreed on the movie).
