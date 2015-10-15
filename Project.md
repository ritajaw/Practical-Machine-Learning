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
```
```sh
dim(testingCleaned)
```
```sh
## [1] 20 53
```
The cleaned training data: 19622 observations and 53 variables The cleaned testing data: 20 observations and 53 variables

##Partion the data

Partion training data set into two set, 60% for the training data set and 40% for the testing data set.

```sh

set.seed(12345) 
inTrain <- createDataPartition(trainingCleaned$classe, p=0.60, list=F)
trainingData <- trainingCleaned[inTrain, ]
testingData <- trainingCleaned[-inTrain, ]
dim(trainingData)

```

```sh
## [1] 11776    53
```

```sh
dim(testingData)
```

```sh
## [1] 7846   53
```
## Using Random Forest algorithm for Prediction
```sh
controlRf <- trainControl(method="cv", 5)
modelRf <- train(classe ~ ., data=trainingData, method="rf", trControl=controlRf, ntree=250)
modelRf
```
```sh
## Random Forest 
## 
## 11776 samples
##    52 predictor
##     5 classes: 'A', 'B', 'C', 'D', 'E' 
## 
## No pre-processing
## Resampling: Cross-Validated (5 fold) 
## Summary of sample sizes: 9420, 9421, 9420, 9420, 9423 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy   Kappa      Accuracy SD  Kappa SD   
##    2    0.9878570  0.9846379  0.002047083  0.002590078
##   27    0.9888767  0.9859292  0.002442210  0.003089856
##   52    0.9855649  0.9817377  0.003228312  0.004087724
## 
## Accuracy was used to select the optimal model using  the largest value.
## The final value used for the model was mtry = 27.
```

```sh
predictRf <- predict(modelRf, testingData)
confusionMatrix(testingData$classe, predictRf)
```

```sh
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 2230    2    0    0    0
##          B   12 1501    5    0    0
##          C    0    6 1357    5    0
##          D    0    0   19 1263    4
##          E    0    2    3    5 1432
## 
## Overall Statistics
##                                           
##                Accuracy : 0.992           
##                  95% CI : (0.9897, 0.9938)
##     No Information Rate : 0.2858          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.9898          
##  Mcnemar's Test P-Value : NA              
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9946   0.9934   0.9805   0.9921   0.9972
## Specificity            0.9996   0.9973   0.9983   0.9965   0.9984
## Pos Pred Value         0.9991   0.9888   0.9920   0.9821   0.9931
## Neg Pred Value         0.9979   0.9984   0.9958   0.9985   0.9994
## Prevalence             0.2858   0.1926   0.1764   0.1622   0.1830
## Detection Rate         0.2842   0.1913   0.1730   0.1610   0.1825
## Detection Prevalence   0.2845   0.1935   0.1744   0.1639   0.1838
## Balanced Accuracy      0.9971   0.9953   0.9894   0.9943   0.9978


```
Based on the result of Random Forest Algorithm, the estimated accuracy of this model is 99.2% and the estimated out-of-sample error is 0.8%.
