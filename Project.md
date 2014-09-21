Machine-learning-Project
=================

>Background

>>Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it. In this project, your goal will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways. More information is available from the website here: http://groupware.les.inf.puc-rio.br/har (see the section on the Weight Lifting Exercise Dataset). 

>Task

>>The goal of your project is to predict the manner in which they did the exercise. This is the "classe" variable in the training set. You may use any of the other variables to predict with. You should create a report describing how you built your model, how you used cross validation, what you think the expected out of sample error is, and why you made the choices you did. You will also use your prediction model to predict 20 different test cases. 

>My solution

>>First, I loaded caret package, then the data in R. There are 160 columns, so I first took a look to see if some of them could be removed. I started looking at columns in training data. First column is just a index (the # of current row), so it can be discarded. The username column will be removed as well, because it can cause unstability and false positives (we don't want our model to be trained on usernames)

>>>Then I started printing some features histograms and charts with two features against each other to see if I could spot patterns that could help building a good model. Most features, taken alone, wouldn't show significant patterns: they looked something like this
![1](https://cloud.githubusercontent.com/assets/8848517/4347580/acc202aa-415d-11e4-8286-f526d2c0dea8.jpg)
>>>with spikes corresponding to the same values for all the classes. Moving on, not every combination of features was useful either:
![2](https://cloud.githubusercontent.com/assets/8848517/4347583/e45c3dd4-415d-11e4-8450-de9e9e30fbea.jpg)
>>>As you can see above, the shapes are almost identical for all clasees. Some combinations, instead, worked better:
![33](https://cloud.githubusercontent.com/assets/8848517/4347613/fed0a68a-415f-11e4-88eb-7d475624386a.JPG)
>>>And indeed you can see very different shapes for at least some of the classes. That tells us those features will probably be important for building a good model.

>>I checked for columns with a high percent of data that is either NA or "", and excluded them. As expected, none of the "important" features shown above has been removed from the dataset.

>>I then transformed the "classe" variable in a factor var and, finally, I partitioned the training set into training and cross validation to have a measure of out-of-sample error.

>>Initially I was planning a model with 3 predictors: a random forest, a boosting predictor, and a linear classifier. Then I resorted to using just the latter 2, with their output then used to train another random forest on the cv set. It is a bit slow to train because of the big number of columns and rows, but a couple of hours were enough to be done with it. Probably some of the less promising columns could be eliminated, or certainly a PCA analysis could be run to reduce the dimension of the data and speed up train.

>>To double check performance, testing this model against the boosting predictor only, the increment in accuracy for out-of-sample error was marginal: just about 0.3%. The increment with respect to linear regression only, instead, was about 20%. Since the gbm model is the one requiring more time to train, in absence og time constraint it is still good to gain even a small improvement using bagging among multiple models, in exchange for a few more minutes required for training.

>>>I tested the out-of-sample error on the cross-validation set and the result was very encouraging:
![4](https://cloud.githubusercontent.com/assets/8848517/4347612/e52303a4-415f-11e4-8747-0b561727dcdf.jpg)

