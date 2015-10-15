#Practical Machine Learning Project

##Background
Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement - a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it. In this project, our goal will be use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways.


##Load the data and read the data

```sh
setwd("C:/Users/owner/Desktop/Datascience")

library(caret)
library(rpart)
library(rpart.plot)
library(randomForest)
library(corrplot)
`````
```sh
training<-read.csv("./Practical machine learning/pml-training.csv")
testing<-read.csv("./Practical machine learning/pml-testing.csv")
dim(training)

````
```sh
## [1] 19622   160
```
```sh
dim(testing)
```
```sh
## [1]  20 160
```
Training data : 19622 observations and 160 variables Testing data: 20 obsercations and 160 variables

#Clean the data
```sh
sum(complete.cases(training))
```

```sh
## [1] 406
```

```sh
trainingNA<-training[, colSums(is.na(training))==0]
testingNA<-testing[, colSums(is.na(testing))==0]

classe<-trainingNA$classe
trainingRemove<-grepl("^X|timestamp|window", names(trainingNA))
trainingNA <- trainingNA[, !trainingRemove]
trainingCleaned <- trainingNA[, sapply(trainingNA, is.numeric)]
trainingCleaned$classe <- classe
testingRemove <- grepl("^X|timestamp|window", names(testingNA))
testingNA <- testingNA[, !testingRemove]
testingCleaned <- testingNA[, sapply(testingNA, is.numeric)]

dim(trainingCleaned)
```

```sh
## [1] 19622    53

```sh
dim(testingCleaned)
```
```sh
## [1] 20 53
```
The cleaned training data: 19622 observations and 53 variables The cleaned testing data: 20 observations and 53 variables

##Partion the data

Partion training data set into two set, 60% for the training data set and 40% for the testing data set.

