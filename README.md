# Movie recommendation engine development: a hybrid approach combinding collaborative filtering and matrix factorization

## Introduction

  In this project, I built a movie recommendation system using a hybrid approach combinding collaborative filtering and matrix factorization. 
The recommendation engine will evaluate whether user has an account on file, namely, whether the existing database contains information on certain user's movie ratings history,
if so, the engine will recommend movies based on users'rating history. Otherwise, the engine will ask user to input his/her facourite movie, then the engine will recommend similar movies using item-based collaborative filtering.
The recommendation list will combine the results from item-based collaborative filtering using KNN and the results from matrix factorization using SVD, in order to work around the potential downsides of collaborative filtering.   


## Data

I used [MovieLens 20 M Datasets](https://grouplens.org/datasets/movielens/). This dataset describes 5-star rating from MovieLens, a movie recommendation service. It contains 20000263 ratings from 138493 users on 26744 movies. These data were created between 4/2015 and 10/2016. 

