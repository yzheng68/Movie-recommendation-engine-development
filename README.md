# Movie recommendation engine development: a hybrid approach combinding item-based collaborative filtering and matrix factorization

## Introduction

  In this project, I built a movie recommendation system using a hybrid approach combinding item-based collaborative filtering and matrix factorization. 
The recommendation engine will evaluate whether user has an account on file, namely, whether the existing database contains information on certain user's movie ratings history,
if so, the engine will recommend movies based on users'rating history. Otherwise, the engine will ask user to input his/her facourite movie, then the engine will recommend similar movies using item-based collaborative filtering.
The recommendation list will combine the results from item-based collaborative filtering using KNN and the results from matrix factorization using ALS.   

The process can be visualized in the following workflow chart:
![Image](https://github.com/yzheng68/Movie-recommendation-engine-development/blob/master/pics/output-graph.gv.png)

## Data

I used [MovieLens 20 M Datasets](https://grouplens.org/datasets/movielens/). This dataset describes 5-star rating from MovieLens, a movie recommendation service. It contains 20000263 ratings from 138493 users on 26744 movies. These data were created between 4/2015 and 10/2016. 

## Models
I started with the basic and easy-to-implment KNN Item Based Collaborative Filtering models for my recommender system. To overcome the downsides of collaborative filtering (including cold-start, biased towards popular items, etc.), I used more complex Alternate Least Square (ALS)  Matrix Factorization. Eventually, I combined the recommendation lists from the two methods. 


### Item-based recommendation system using KNN

  KNN is an easy-to-understand algorithm that can be used as a benchmark model for recommender system. In item-based collaborative filtering, KNN will use a pre-defined distance metric based on users' ratings to find similar items, and make recommendations using the top-k nearest neighbors. When user is new, a question will pop-up to ask about his/her favourite movie, then we will find movies that fuzzily match the movie input by the user, then distance metrics will be computed between the matched movies and the rest of movies, and top-k movies with the least distance will be returned. When user is not new, top 5 rated movies by the user will be extracted, then distance metrics will be computed between the top 5 rated movies and all the unrated movies by the user, and top-k movies with the least distance will be returned. 
  
  Let's see how it works in action:
  ![Image](https://github.com/yzheng68/Movie-recommendation-engine-development/blob/master/pics/Screen%20Shot%202019-06-22%20at%2010.27.23%20AM.png)
  ![Image](https://github.com/yzheng68/Movie-recommendation-engine-development/blob/master/pics/Screen%20Shot%202019-06-22%20at%2010.27.23%20AM.png)
  
 It looks like that KNN model works for general purpose, however, it has several limitation:

    1. It biased towards popular movies: unpopular movies will in general have very low similarity score to all the other movies in the database, despite the fact that they might actually be very similar to some movies in terms of genre, etc. This is usually the downside of item-based collaborative filtering system. 
    2. KNN algorithm is computationally expensive, therefore, it might not be scalale. 
    
In the next section, we are going to build another recommendation engine using Alternating least square(ALS), blend the list of recommended movies from this method to the list we obtained from the KNN model, to further improve the recommendation engine.
  
### Matrix Factorization using ALS

  Matrix factorization is a method to decompose the big user-item matrix into low dimentional matrices to denote user hidden taste factors and item hidden factors. This could solve the item 'cold start' problem as we could use the user hidden taste factor and item hidden factor we learned from the data to predict unrated items. There are many ways to do matrix factorization, such as SVD and ALS. I chose ALS because it is supported by Spark MLlib and could be easily implemented under the context of distributed computing. 
  
  There are three hyperparameters for the ALS algorithm:
  
    rank: the number of latent factors(defaults to 10)
    regParam: the regularization parameter that controls the model complexity (defaults to 1.0)
    maxIter: the maximum number of iterations to run (defaults to 10)
    
   The hyperparameters are tuned through a grid-search method. 
   
   ![Image](https://github.com/yzheng68/Movie-recommendation-engine-development/blob/master/pics/Screen%20Shot%202019-06-22%20at%2011.59.38%20AM.png)
   
   In the end, rank=20, regParam=0.05, and maxIter=10 will result in minumum RMSE (0.7986) in the validation dataset with out-of-bag error in the test dataset as . This set of hyperparameters will be used in the ALS model to predict movie ratings. 
   
  Following similar step as in the KNN method, when user is new, she/he will be asked about her/his favourite movie, then the user information will be appended to the dataset. The new user will be assigned userID which is the current maximum userID plus 1, and the movies related to her/his input movie will be assigned ratings of current maximum rating among all users & movies. Then the ALS model will be trained using the appended dataset, and ratings for movies which are not related to user's input movie will be predicted, then the movies with top predicted ratings will be recommended.When the user is not new, ALS model will be trained on the exising dataset, ratings for user's unrated movies will be predicted, then the movies with top predicted ratings will be recommended. 
  
### Combined the two recommendation lists

  In the end,  recommendation lists using KNN and ALS algorithm will be combined to leverage the benefits and downsides of each method. Suppose we specified to recommend n movies, 0.4*n movies will be randomly drawn from the KNN recommendation list, and 0.6*n movies will be randomly drawn from the ALS recommendation list. 
  
  Let's see how it works in action:
  
  For new user:
  ![Image](https://github.com/yzheng68/Movie-recommendation-engine-development/blob/master/pics/Screen%20Shot%202019-06-23%20at%2010.55.47%20AM.png)
  
  For existing user with userID 1:
  
  ![Image](https://github.com/yzheng68/Movie-recommendation-engine-development/blob/master/pics/Screen%20Shot%202019-06-23%20at%2010.54.15%20AM.png)
  
  
 # Future work 
 
 1. The data are from 2015-2016, it will be interesting to incorporate more recent dataset.
 2. The KNN model is intuitive, easy-to-implement, however, it is slow and not scalable. I might consider more scalable, efficient algorithm to replace the KNN model. 
 3. The current model didn't take movie genres, director, actor, etc information into account. It might improve the accuracy of the recommendation system through leverating those information. Content-based recommender system could be built and combined with the current model-based one to solve problems such as user cold-start. 
 
