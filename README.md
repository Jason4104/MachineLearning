# MachineLearning

## Introduction
The assignment's purpose is to analyze the data provided on personal fitness actvity, and determine how much of a particular activity people routinely perform and how well they perform it. 

## Data Analysis Steps

1. Summary of dataset

Six young participants performed various fitness workout activities, and thier performance is recorded in 5 classes of data (Class A, B, C, D, E). 
Class A refers to the specified execution of the excercise, while the rest correspond with occurances of mistakes. 

2. Data Analysis

After loading the data into R, we can perform basic exploratory analysis on the data. There is a lot of missing values and NAs in this dataset, so the data need to be cleaned first using the below command:

```{r}
setwd("~/R/MachineLearning")
a=read.csv('pml-training.csv',na.strings=c('','NA'))
b=a[,!apply(a,2,function(x) any(is.na(x)) )]
c=b[,-c(1:7)]
```

After cleaning the data, we have a total of 19622 data points with 53 predictors. 

Then we prepare the needed packages for the analysis.

```{r}
library('randomForest')
library('caret')
library('e1071')
```

In order to be able to conduct cross validation, the testing data is split into groups with 60% and 40% split using the following commands:
```{r}
subGrps=createDataPartition(y=c$classe, p=0.6, list=FALSE)
subTraining=c[subGrps,]
subTesting=c[-subGrps, ]
dim(subTraining);dim(subTesting)
```

So after splitting the data, we have 11776 observations in the Training group, and 7846 observations in the testing group.

After this, random forest model is utlized to create a predictive model. The model is created using the Training group observations. Once the model is created, it can be used to predict values of the testing group, using the confusion matrix. The code is used below:
```{r}
subTraining$classe = factor(subTraining$classe)
model=randomForest(classe~., data=subTraining, method='class')
pred=predict(model,subTesting, type='class')
z=confusionMatrix(factor(pred),factor(subTesting$classe))
save(z,file='test.RData')
```

The result is shown here:
```{r}
setwd("~/R/MachineLearning")
load('test.RData')
z$table
```
The summary of the model is here:
```{r}
z
```

Thus, the **accuracy** of the model is 99.16%, and the **out of sample error**, which is the error rate on the new testing set, is 0.99%.
The **95% confidence interval** is between 0.98% to 0.99%

3. Prediction
Since the random forest model is very accurate, the out of sample error is very small, we can successfully use this model to predict the actual testing data set. 

First we need to process the testing data set:
```{r}
d=read.csv('pml-testing.csv',na.strings=c('','NA'))
e=d[,!apply(d,2,function(x) any(is.na(x)) )]
f=e[,-c(1:7)]
```

After this, the predictive model is utilized on this testing dataset using the following code:
```{r}
predicted=predict(model,f,type='class')
save(predicted,file='predicted.RData')
```

And the predictive result for the 2o test cases are shown below:
```{r}
setwd("~/R/MachineLearning")
load('predicted.RData')
predicted
```


