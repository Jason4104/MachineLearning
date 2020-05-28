# MachineLearning

Introduction
The assignment’s purpose is to analyze the data provided on personal fitness actvity, and determine how much of a particular activity people routinely perform and how well they perform it.

Data Analysis Steps
Summary of dataset
Six young participants performed various fitness workout activities, and thier performance is recorded in 5 classes of data (Class A, B, C, D, E). Class A refers to the specified execution of the excercise, while the rest correspond with occurances of mistakes.

Data Analysis
After loading the data into R, we can perform basic exploratory analysis on the data. There is a lot of missing values and NAs in this dataset, so the data need to be cleaned first using the below command:

setwd("~/R/MachineLearning")
a=read.csv('pml-training.csv',na.strings=c('','NA'))
b=a[,!apply(a,2,function(x) any(is.na(x)) )]
c=b[,-c(1:7)]
After cleaning the data, we have a total of 19622 data points with 53 predictors.

Then we prepare the needed packages for the analysis.

library('randomForest')
## randomForest 4.6-14
## Type rfNews() to see new features/changes/bug fixes.
library('caret')
## Loading required package: lattice
## Loading required package: ggplot2
## 
## Attaching package: 'ggplot2'
## The following object is masked from 'package:randomForest':
## 
##     margin
library('e1071')
In order to be able to conduct cross validation, the testing data is split into groups with 60% and 40% split using the following commands:

subGrps=createDataPartition(y=c$classe, p=0.6, list=FALSE)
subTraining=c[subGrps,]
subTesting=c[-subGrps, ]
dim(subTraining);dim(subTesting)
## [1] 11776    53
## [1] 7846   53
So after splitting the data, we have 11776 observations in the Training group, and 7846 observations in the testing group.

After this, random forest model is utlized to create a predictive model. The model is created using the Training group observations. Once the model is created, it can be used to predict values of the testing group, using the confusion matrix. The code is used below:

subTraining$classe = factor(subTraining$classe)
model=randomForest(classe~., data=subTraining, method='class')
pred=predict(model,subTesting, type='class')
z=confusionMatrix(factor(pred),factor(subTesting$classe))
save(z,file='test.RData')
The result is shown here:

setwd("~/R/MachineLearning")
load('test.RData')
z$table
##           Reference
## Prediction    A    B    C    D    E
##          A 2232   10    0    0    0
##          B    0 1507   20    0    0
##          C    0    1 1348   20    7
##          D    0    0    0 1266    1
##          E    0    0    0    0 1434
The summary of the model is here:

z
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 2232   10    0    0    0
##          B    0 1507   20    0    0
##          C    0    1 1348   20    7
##          D    0    0    0 1266    1
##          E    0    0    0    0 1434
## 
## Overall Statistics
##                                           
##                Accuracy : 0.9925          
##                  95% CI : (0.9903, 0.9943)
##     No Information Rate : 0.2845          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.9905          
##                                           
##  Mcnemar's Test P-Value : NA              
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            1.0000   0.9928   0.9854   0.9844   0.9945
## Specificity            0.9982   0.9968   0.9957   0.9998   1.0000
## Pos Pred Value         0.9955   0.9869   0.9797   0.9992   1.0000
## Neg Pred Value         1.0000   0.9983   0.9969   0.9970   0.9988
## Prevalence             0.2845   0.1935   0.1744   0.1639   0.1838
## Detection Rate         0.2845   0.1921   0.1718   0.1614   0.1828
## Detection Prevalence   0.2858   0.1946   0.1754   0.1615   0.1828
## Balanced Accuracy      0.9991   0.9948   0.9905   0.9921   0.9972
Thus, the accuracy of the model is 99.16%, and the out of sample error, which is the error rate on the new testing set, is 0.99%. The 95% confidence interval is between 0.98% to 0.99%

Prediction Since the random forest model is very accurate, the out of sample error is very small, we can successfully use this model to predict the actual testing data set.
First we need to process the testing data set:

d=read.csv('pml-testing.csv',na.strings=c('','NA'))
e=d[,!apply(d,2,function(x) any(is.na(x)) )]
f=e[,-c(1:7)]
After this, the predictive model is utilized on this testing dataset using the following code:

predicted=predict(model,f,type='class')
save(predicted,file='predicted.RData')
And the predictive result for the 2o test cases are shown below:

setwd("~/R/MachineLearning")
load('predicted.RData')
predicted
##  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 
##  B  A  B  A  A  E  D  B  A  A  B  C  B  A  E  E  A  B  B  B 
## Levels: A B C D E

