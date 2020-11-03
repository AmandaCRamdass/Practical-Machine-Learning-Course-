---
title: "Amanda R-Assignment 1"
author: "Amanda Ramdass"
date: "11/3/2020"
output: html_document
---
## Background

Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it. 

In this project, my goal will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants to predict the manner in which they did the exercise.

This report describes how a model was built, how cross validation was used, what the expected out of sample error might be, and the rationale behind the choices made. The prediction model will also be used to predict 20 different test cases.

## Set-up

Load package.

```{r}
set.seed(12345)
library(caret)
```

## Download data and submit it

```{r}
url_train <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
rawdata <- read.csv(url_train, na.strings = c("", "NA"))
url_submit <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"
submit_data <- read.csv(url_submit, na.strings = c("", "NA"))
```
## clean the data
We should delete the column that contains NA to avoid the error. In addition, in order to make accurate predictions, columns that is not related exercise must also be deleted.

```{r}
#Remove NA cols
colname <- colnames(rawdata)[!colSums(is.na(rawdata)) > 0]
colname
```


```{r}
#Slice data related with exercise
colname <- colname[8: length(colname)]
df_wo_NA <- rawdata[colname]

#Check the colnames of df_wo_NA is in submit_data.
#The last colname is "classe"
is.element(colname, colnames(submit_data))
```

## Split into random train and test

```{r}
inTrain = createDataPartition(df_wo_NA$classe, p = 3/4)[[1]]
training = df_wo_NA[ inTrain,]
testing = df_wo_NA[-inTrain,]
```

# Random forest

```{r}
model_rf <- train(classe ~ ., data = training, method = "rf")
pred_rf <- predict(model_rf, testing)
confusionMatrix(testing$classe, pred_rf)
```

## Linear Discriminant Analysis

Poor accuracy and it takes a short time

```{r}
model_lda <- train(classe ~ ., data = training, method = "lda")
pred_lda <- predict(model_lda, testing)
confusionMatrix(testing$classe, pred_lda)
```

## Recursive Partitioning and Regression Trees
The results can be confirmed visually, but poor accuracy.

```{r}
model_rpart <- train(classe ~ ., data = training, method = "rpart")
pred_rpart<- predict(model_rpart, testing)
confusionMatrix(testing$classe, pred_rpart)
```

## Load library

```{r}
library(rattle)
fancyRpartPlot(model_rpart$finalModel)
```

## Submit data with Random Forest
We can use the high accuracy model to submit data. In this report the Random Forest accuracy has the highest value 99.45. We can show the prediction.

```{r}
submit_rf <- predict(model_rf, submit_data)
submit_rf
```

# Conclusion

In this report the Random Forest accuracy has the highest value 99.45.
