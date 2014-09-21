library(caret)
#setwd("..YOUR_PATH..")
training = read.csv("pml-training.csv", header = TRUE)

training <- training[, 6:dim(training)[2]]

treshold <- dim(training)[1] * 0.95
#Remove columns with more than 95% of NA or "" values
goodColumns <- !apply(training, 2, function(x) sum(is.na(x)) > treshold  || sum(x=="") > treshold)

training <- training[, goodColumns]

badColumns <- nearZeroVar(training, saveMetrics = TRUE)

training <- training[, badColumns$nzv==FALSE]

training$classe = factor(training$classe)

#Partition rows into training and crossvalidation
inTrain <- createDataPartition(training$classe, p = 0.6)[[1]]
crossv <- training[-inTrain,]
training <- training[ inTrain,]
inTrain <- createDataPartition(crossv$classe, p = 0.75)[[1]]
crossv_test <- crossv[ -inTrain,]
crossv <- crossv[inTrain,]

testing = read.csv("pml-testing.csv", header = TRUE)
testing <- testing[, 6:dim(testing)[2]]
testing <- testing[, goodColumns]
testing$classe <- NA
testing <- testing[, badColumns$nzv==FALSE]

#Train 3 different models
#mod1 <- train(classe ~ ., data=training, method="rf")
mod2 <- train(classe ~ ., data=training, method="gbm")
mod3 <- train(classe ~ ., data=training, method="lda")

#pred1 <- predict(mod1, crossv)
pred2 <- predict(mod2, crossv)
pred3 <- predict(mod3, crossv)

//show confusion matrices
#confusionMatrix(pred1, crossv$classe)
confusionMatrix(pred2, crossv$classe)
confusionMatrix(pred3, crossv$classe)

#predDF <- data.frame(pred1, pred2, pred3, classe=crossv$classe)
predDF <- data.frame(pred2, pred3, classe=crossv$classe)

combModFit <- train(classe ~ ., method="rf", data=predDF)
#in-sample error
combPredIn <- predict(combModFit, predDF)
confusionMatrix(combPredIn, predDF$classe)

#out-of-sample error
pred2 <- predict(mod2, crossv_test)
pred3 <- predict(mod3, crossv_test)
predDF <- data.frame(pred2, pred3, classe=crossv_test$classe)

combPredOut <- predict(combModFit, predDF)
confusionMatrix(combPredOut, predDF$classe)



pml_write_files = function(x){
  n = length(x)
  for(i in 1:n){
    filename = paste0("problem_id_",i,".txt")
    write.table(x[i],file=filename,quote=FALSE,row.names=FALSE,col.names=FALSE)
  }
}

pml_write_files(combPred)
