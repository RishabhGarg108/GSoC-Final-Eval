# Final Report GSOC 2021

#### Organisation: [mlpack](https://github.com/mlpack)

#### Project: Improve Tree Ensemble Support of mlpack.

#### Mentors: [Ryan Curtin](https://github.com/rcurtin) and [Germán Lancioni](https://github.com/gmanlan)

## Abstract
For Google Summer of Code 2021, my proposal `Improve the Tree Ensemble Support` was approved. Tree ensemble methods are widely used algorithms in machine learning and are often in prize winning solutions of various ML competitions. XGBoost by Tianqi Chen et al. is one of the most successful implementations of GBDT which is a tree ensemble method. So, my proposal was to have a similar implementation of it in mlpack.

## Goal
The goal was to have a working implementation of XGBoost tree method. There are many hardware and approximation optimizations which makes it blazing fast. The algorithm fits a forest of "Decision Tree Regressor" for training. But, mlpack didn't have an implementation for it. Thus, the final goal was to implement the `DecisionTreeRegressor` and have a basic implementation of the XGBoost algorithm.

## Result
A fully functional `DecisionTreeRegressor` class was implemented and thoroughly tested. Its gain computation was optimized using SIMD instructions and prefix-sum which gave significant speedups. The implemetation was benchmarked with sklearn and they performed almost the same. The Loss Function for XGBoost was merged, while the `XGBoostExactSplitter` and `XGBTreeRegressor` are almost complete with a bit of testing left to do. There were a few hiccups, but we managed to meet most of the important milestones. Finally an attempt was made to try and parallelize the gain computation in the Decision Tree, but it is still a work in progress.

<br><hr>
Following is the list of the Pull Requests which I created for my project:-

### 1. [PR 3011: Add Decision Tree Regressor (MERGED)](https://github.com/mlpack/mlpack/pull/3011)
In this PR, I implemented:-
1. `MSEGain` and `MAEGain`: These are the gain computation policy classes. Here, `MSEGain` was optimized by pre-computing prefix-sum of squares and `MAEGain` was optimized using SIMD instructions.
2. Numeric and Categorical splitting strategies for regression.
3. `DecisionTreeRegressor`: The main user facing class for training decision tree regressor.
4. This also includes the thorough test cases for each of these classes.

(Mentioning [PR 2905](https://github.com/mlpack/mlpack/pull/2905) just for reference as this contains some important discussions which has led to making many decisions down the line.)

### 2. [PR 3014: Refactor DecisionTreeRegressor to support XGBoost (MERGED)](https://github.com/mlpack/mlpack/pull/3014)
In this PR, I did some minor refactorings to the `DecisionTreeRegressor` class to support XGBoost. The following changes were made in this:-
1. An instantiated `FitnessFunction` was passed to make the XGBoost parameters accessible to it.
2. The `OuputLeafValue()` function was moved to the `FitnessFunction` classes to support a variety of ways to calculate output value as required by XGBoost.

### 3. [PR 3003: SSE Loss for XGBoost (MERGED)](https://github.com/mlpack/mlpack/pull/3003)
In this PR, I implemented SSE Loss function.
1. Implemented the interface for calculating greedy best numeric split and gain computation.

### 4. [PR 3022: Add XGBExactNumericSplit (OPEN)](https://github.com/mlpack/mlpack/pull/3022)
In this PR, I implemented Greedy Best Numeric Split Finding algorithm. This is almost complete. A small design situation is left to be fixed and after that, it is ready to merge.

### 5. [PR 3025: Add XGBoostTreeRegressor (OPEN)](https://github.com/mlpack/mlpack/pull/3025)
In this PR, I implemented the following:-
1. Core xgboost tree algorithm as described in [XGBoost: A Scalable Tree Boosting System](https://arxiv.org/abs/1603.02754).
2. It supports only numerical features in training dataset.
3. Basic tests were written and it works fine.
4. Its training time was compared with xgboost library and mlpack's implementation did better on small datasets but xgboost's implemetation did better on large datasets.
5. This PR is almost complete, just some testing is left to be 100% sure about it's correctness.


### 6. [PR 3031: Parallelize DecisionTree training (WIP)](https://github.com/mlpack/mlpack/pull/3031)
This is a work in progress PR. In it, I am trying to parallelize the gain computation across different dimensions in decision tree. This would help to boost the training in XGBoost as well. It's been a bit difficult to use OpenMP to do it, and I am still figuring out how to do it.


## Future Work
I intend to continue working on mlpack. It has been a really enriching experience to work on mlpack. My project was to add ensemble tree methods and it is an open ended project as there are tons of different ensemble techniques. First, I will finish my leftover PRs of xgboost. Even in xgboost, I barely scratched the surface and there are many more things to xgboost algorithm like approximate split finding algorithms to efficiently scale to larger datasets. So, I will focus majorly on polishing and continuing my work that I did during this awesome summer.

## Acknowledgement
I would like to thank my mentors for being so supportive and helpful. I could have never finished this without their support. There was a time when I was in a rabit hole and there was too much to refactor, but with their help, I managed to do most of it. I also want to thank the mlpack family for their help. I have heard from many people that in industry, we seldom get to work with core machine learning stuff and we mostly spend time on pther parts of the pipeline. But I feel that I am very lucky that I got the opportunity to implement one of the hottest ml algorithms - decision tree and gradient bosting trees. I got to learn these algorithms from ground up and now I can really appreciate why these algorithms are so good. This also gives me opportunity to boast about it in the machine learning class at my university too ;). I am very happy and satisfied with my accomplishments and I feel that this summer was definitely spent well.

At last I am thankful to Google for this great opportunity.
