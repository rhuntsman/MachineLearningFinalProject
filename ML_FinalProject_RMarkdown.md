# Forecasting Lifting Accuracy with Machine Learning
========================================================

# Overview
This project analyzes data generated by six participants lifting weights while wearing a personal tracking device.  We process, analyze, select and combine 159 potential covariates to build a machine learning model using the random forest method that correctly predicts 100% of 20 cases using a subset of the covariates.  

The data for this project was developed by:
http://groupware.les.inf.puc-rio.br/har

The training data for this project is available here:
https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv

The test data for this project is available here:
https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv

# Load libraries

```
## Error: Project/release lubridate 1.3.3 was not found. Required releases:
## 1.3.3-r30,1.3.3
```

```
## Error: Project/release rpart core was not found. Required releases:
## core-r30,core
```

```
## Error: Project/release rpart 4.1-8 was not found. Required releases:
## 4.1-8-r30,4.1-8
```

```
## Error: unable to install packages
```

```
## Error: unable to install packages
```

```
## Error: unable to install packages
```

```
## Error: unable to install packages
```

```
## Error: unable to install packages
```

```
## Error: unable to install packages
```

```
## Error: package 'RcppEigen' could not be loaded
```

```
## Error: package 'RcppEigen' could not be loaded
```

```
## Error: package or namespace load failed for 'devtools'
```

```
## Error: package or namespace load failed for 'data.table'
```

```
## Error: package or namespace load failed for 'htmltools'
```

```
## Error: package or namespace load failed for 'rmarkdown'
```

```
## Error: package 'raster' could not be loaded
```



# Testing and Training Data Sets
We begin by loading the pml-training data set and examining its structure.  The structure of the training data set shows that many of the variables are in the incorrect format (e.g. numeric variables are factors and time variables aren't properly formatted).   


```r
dat = read.csv(trainLoc, header = TRUE)
str(dat)
```

```
## 'data.frame':	19622 obs. of  160 variables:
##  $ X                       : int  1 2 3 4 5 6 7 8 9 10 ...
##  $ user_name               : Factor w/ 6 levels "adelmo","carlitos",..: 2 2 2 2 2 2 2 2 2 2 ...
##  $ raw_timestamp_part_1    : int  1323084231 1323084231 1323084231 1323084232 1323084232 1323084232 1323084232 1323084232 1323084232 1323084232 ...
##  $ raw_timestamp_part_2    : int  788290 808298 820366 120339 196328 304277 368296 440390 484323 484434 ...
##  $ cvtd_timestamp          : Factor w/ 20 levels "02/12/2011 13:32",..: 9 9 9 9 9 9 9 9 9 9 ...
##  $ new_window              : Factor w/ 2 levels "no","yes": 1 1 1 1 1 1 1 1 1 1 ...
##  $ num_window              : int  11 11 11 12 12 12 12 12 12 12 ...
##  $ roll_belt               : num  1.41 1.41 1.42 1.48 1.48 1.45 1.42 1.42 1.43 1.45 ...
##  $ pitch_belt              : num  8.07 8.07 8.07 8.05 8.07 8.06 8.09 8.13 8.16 8.17 ...
##  $ yaw_belt                : num  -94.4 -94.4 -94.4 -94.4 -94.4 -94.4 -94.4 -94.4 -94.4 -94.4 ...
##  $ total_accel_belt        : int  3 3 3 3 3 3 3 3 3 3 ...
##  $ kurtosis_roll_belt      : Factor w/ 397 levels "","-0.016850",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ kurtosis_picth_belt     : Factor w/ 317 levels "","-0.021887",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ kurtosis_yaw_belt       : Factor w/ 2 levels "","#DIV/0!": 1 1 1 1 1 1 1 1 1 1 ...
##  $ skewness_roll_belt      : Factor w/ 395 levels "","-0.003095",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ skewness_roll_belt.1    : Factor w/ 338 levels "","-0.005928",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ skewness_yaw_belt       : Factor w/ 2 levels "","#DIV/0!": 1 1 1 1 1 1 1 1 1 1 ...
##  $ max_roll_belt           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ max_picth_belt          : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ max_yaw_belt            : Factor w/ 68 levels "","-0.1","-0.2",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ min_roll_belt           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_pitch_belt          : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_yaw_belt            : Factor w/ 68 levels "","-0.1","-0.2",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ amplitude_roll_belt     : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ amplitude_pitch_belt    : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ amplitude_yaw_belt      : Factor w/ 4 levels "","#DIV/0!","0.00",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ var_total_accel_belt    : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ avg_roll_belt           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ stddev_roll_belt        : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ var_roll_belt           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ avg_pitch_belt          : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ stddev_pitch_belt       : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ var_pitch_belt          : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ avg_yaw_belt            : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ stddev_yaw_belt         : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ var_yaw_belt            : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ gyros_belt_x            : num  0 0.02 0 0.02 0.02 0.02 0.02 0.02 0.02 0.03 ...
##  $ gyros_belt_y            : num  0 0 0 0 0.02 0 0 0 0 0 ...
##  $ gyros_belt_z            : num  -0.02 -0.02 -0.02 -0.03 -0.02 -0.02 -0.02 -0.02 -0.02 0 ...
##  $ accel_belt_x            : int  -21 -22 -20 -22 -21 -21 -22 -22 -20 -21 ...
##  $ accel_belt_y            : int  4 4 5 3 2 4 3 4 2 4 ...
##  $ accel_belt_z            : int  22 22 23 21 24 21 21 21 24 22 ...
##  $ magnet_belt_x           : int  -3 -7 -2 -6 -6 0 -4 -2 1 -3 ...
##  $ magnet_belt_y           : int  599 608 600 604 600 603 599 603 602 609 ...
##  $ magnet_belt_z           : int  -313 -311 -305 -310 -302 -312 -311 -313 -312 -308 ...
##  $ roll_arm                : num  -128 -128 -128 -128 -128 -128 -128 -128 -128 -128 ...
##  $ pitch_arm               : num  22.5 22.5 22.5 22.1 22.1 22 21.9 21.8 21.7 21.6 ...
##  $ yaw_arm                 : num  -161 -161 -161 -161 -161 -161 -161 -161 -161 -161 ...
##  $ total_accel_arm         : int  34 34 34 34 34 34 34 34 34 34 ...
##  $ var_accel_arm           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ avg_roll_arm            : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ stddev_roll_arm         : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ var_roll_arm            : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ avg_pitch_arm           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ stddev_pitch_arm        : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ var_pitch_arm           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ avg_yaw_arm             : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ stddev_yaw_arm          : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ var_yaw_arm             : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ gyros_arm_x             : num  0 0.02 0.02 0.02 0 0.02 0 0.02 0.02 0.02 ...
##  $ gyros_arm_y             : num  0 -0.02 -0.02 -0.03 -0.03 -0.03 -0.03 -0.02 -0.03 -0.03 ...
##  $ gyros_arm_z             : num  -0.02 -0.02 -0.02 0.02 0 0 0 0 -0.02 -0.02 ...
##  $ accel_arm_x             : int  -288 -290 -289 -289 -289 -289 -289 -289 -288 -288 ...
##  $ accel_arm_y             : int  109 110 110 111 111 111 111 111 109 110 ...
##  $ accel_arm_z             : int  -123 -125 -126 -123 -123 -122 -125 -124 -122 -124 ...
##  $ magnet_arm_x            : int  -368 -369 -368 -372 -374 -369 -373 -372 -369 -376 ...
##  $ magnet_arm_y            : int  337 337 344 344 337 342 336 338 341 334 ...
##  $ magnet_arm_z            : int  516 513 513 512 506 513 509 510 518 516 ...
##  $ kurtosis_roll_arm       : Factor w/ 330 levels "","-0.02438",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ kurtosis_picth_arm      : Factor w/ 328 levels "","-0.00484",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ kurtosis_yaw_arm        : Factor w/ 395 levels "","-0.01548",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ skewness_roll_arm       : Factor w/ 331 levels "","-0.00051",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ skewness_pitch_arm      : Factor w/ 328 levels "","-0.00184",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ skewness_yaw_arm        : Factor w/ 395 levels "","-0.00311",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ max_roll_arm            : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ max_picth_arm           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ max_yaw_arm             : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_roll_arm            : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_pitch_arm           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_yaw_arm             : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ amplitude_roll_arm      : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ amplitude_pitch_arm     : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ amplitude_yaw_arm       : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ roll_dumbbell           : num  13.1 13.1 12.9 13.4 13.4 ...
##  $ pitch_dumbbell          : num  -70.5 -70.6 -70.3 -70.4 -70.4 ...
##  $ yaw_dumbbell            : num  -84.9 -84.7 -85.1 -84.9 -84.9 ...
##  $ kurtosis_roll_dumbbell  : Factor w/ 398 levels "","-0.0035","-0.0073",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ kurtosis_picth_dumbbell : Factor w/ 401 levels "","-0.0163","-0.0233",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ kurtosis_yaw_dumbbell   : Factor w/ 2 levels "","#DIV/0!": 1 1 1 1 1 1 1 1 1 1 ...
##  $ skewness_roll_dumbbell  : Factor w/ 401 levels "","-0.0082","-0.0096",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ skewness_pitch_dumbbell : Factor w/ 402 levels "","-0.0053","-0.0084",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ skewness_yaw_dumbbell   : Factor w/ 2 levels "","#DIV/0!": 1 1 1 1 1 1 1 1 1 1 ...
##  $ max_roll_dumbbell       : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ max_picth_dumbbell      : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ max_yaw_dumbbell        : Factor w/ 73 levels "","-0.1","-0.2",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ min_roll_dumbbell       : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_pitch_dumbbell      : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_yaw_dumbbell        : Factor w/ 73 levels "","-0.1","-0.2",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ amplitude_roll_dumbbell : num  NA NA NA NA NA NA NA NA NA NA ...
##   [list output truncated]
```


We next divide the training data set into training and testing subsets, using 80% of the initial training data set for the sub-train data set.

```r
inTrain <- createDataPartition(y = dat$classe, p = 0.8, list = FALSE)
training = dat[inTrain, ]
testing = dat[-inTrain, ]
```


# Preprocessing Functions
We developed a set of preprocessing functions for converting the columns of the training data to their correct formats and for removing variables from the dataset with zero variance; zero variance variables do not explain the variance of the outcome variable, classe, and so add noise.  The main preprocessing function calls a number of other functions, each designed to achieve the purpose described by its name.


```r
preProcessData <- function(dataset) {
    # Convert columns that should be numeric to numeric
    dataset <- convertFactorsToNumeric(dataset)
    dataset <- convertToNumeric(dataset)
    # Convert covariates that should be in date/time format to date/time format
    dataset <- convertToDateTime(dataset)
    # Create dummy variables from user_name, new_window
    dataset <- removeZeroVar(dataset)
    return(dataset)
}
```


The first function converts factor variables that should be numeric to numeric variables:

```r
convertFactorsToNumeric <- function(dataset) {
    # Convert factor vectors that should be numeric to numeric
    factor_col <- lapply(dataset, class) == "factor"
    factor_col_names <- array(names(dataset[, factor_col == TRUE]))
    factor_col <- which(colnames(dataset) %in% factor_col_names)
    nFactorCols <- nrow(factor_col_names)
    factor_col <- factor_col[-c(1, 2, 3, nFactorCols)]  # Don't convert factor variables that are categorical
    dataset[, factor_col] <- lapply(dataset[, factor_col], as.character)
    dataset[, factor_col] <- lapply(dataset[, factor_col], as.numeric)
    return(dataset)
}
```


The second function converts all other variables types that should be numeric to numeric:

```r
convertToNumeric <- function(dataset) {
    # Convert all columns that should be numeric and that are not factors to
    # numeric
    lastCol <- ncol(dataset)
    nonNumericCols <- c(1, 2, 3, 4, 5, 6, 7, lastCol)
    dataset[, -nonNumericCols] <- lapply(dataset[, -nonNumericCols], as.numeric)
    return(dataset)
}
```


The third function converts date/time fields to their appropriate format:

```r
convertToDateTime <- function(dataset) {
    dataset$cvtd_timestamp <- dmy_hm(dataset$cvtd_timestamp)
    return(dataset)
}
```


The fourth function removes variables with zero variation:

```r
removeZeroVar <- function(dataset) {
    nsv <- nearZeroVar(dataset, saveMetrics = TRUE)
    dataset <- dataset[, nsv$nzv == FALSE]
    return(dataset)
}
```


Functions 5-7 calculate the percent of each column in a data frame that consists of NA's so that covariates with a high percentage of NA's can be removed.

```r

percentNA <- function(dataset) {
    naPercent <- naCount(dataset)/colLength(dataset)
}

naCount <- function(x) sapply(x, function(y) sum(is.na(y)))
colLength <- function(x) sapply(x, function(y) length(y))
```


# Preprocessing
The core set of code does three things: preprocesses the training data, analyzes the covariates and builds a prediction model.

The following line of code preprocess the data using the functions previously loaded:

```r
training <- preProcessData(training)
```

```
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
## Warning: NAs introduced by coercion
```



```r
ggp <- ggplot(training, aes(x = classe))
ggp + geom_histogram(fill = "lightgreen") + ggtitle("Training Classe Distribution") + 
    labs(x = "Classe", y = "Count of Classe")
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11.png) 


Remove the first six variables from the training data set since they are descriptive and add little to no predictive value.

```r
training <- training[, -c(1:6)]
```


Remove variables with a majority of NA values.

```r
percentNA <- percentNA(training)
majorityNA <- names(percentNA[percentNA > 0.5])
training <- training[, !names(training) %in% majorityNA]
```


# Correlation Analysis
Calculate correlations among covariates.

```r
predictors <- training[, -ncol(training)]
corMatrix <- abs(cor(predictors))
diag(corMatrix) <- 0
```


The following chart shows that many of the variables are highly correlated and can be removed.  We remove correlated covariates because they explain the same behavior and are duplicative:

```r
corrplot(corMatrix, type = "upper", method = "color")
```

![plot of chunk unnamed-chunk-15](figure/unnamed-chunk-15.png) 


Remove correlated covariates:

```r
highlyCor <- findCorrelation(corMatrix, 0.7)
training <- training[, -highlyCor]
```


# Most Significant Variable Analysis
Create a random forest model to find the most important covariates

```r
set.seed(1234)
rfFit <- randomForest(classe ~ ., data = training, importance = TRUE, ntree = 100)
```


The variable fits show that much of the variation, whether explained by the decrease in accuracy or the Gini coefficient, is explained by the top 10-15 variables:

```r
varImpPlot(rfFit)
```

![plot of chunk unnamed-chunk-18](figure/unnamed-chunk-18.png) 


# Model Development
We now train two different machine learning models, Linear Discriminant Analysis (LDA) and Random Forest (RF) on the Training data set using PCA to preprocess the data (Note: I saved modrf and am reloading it for purposes of saving time rather than rerunning the model.  

The code to train the model is modrf <- train(classe~.,data=training,method="rf",preProcess="pca")). At this point we have 30 covariates and 1 target variable, classe.  

Preprocessing the data using PCA will find the minimal number of covariates out of the 30 covariates that explains the maximum amount of variation.

The Random Forest model is built by taking the training data set and bootstrapping both the variables and the data.  The tree starts by building predictions on 5 different variables at each split.  Using these variables, the tree predicts the outcome of classe.  The tree then splits and at each additional split, it again tries to estimate classe using 5 of the variables out of the 30 variables in the remaining data set.  The result of splitting multiple times produces 500 separate trees, each with a prediction.  The predictions are averaged to produce an overall prediction of the classe variable.  A printout of the random forest model results and confusion matrix (cross-validation) is presented.

Linear Discriminant Analysis predicts the probability of an observation being in a certain class based on a linear combination of the 30 covariates in the prediction data set.  It basically asks the question: "what is the probability of this observation being in class A-E given the values of the covariates of this observation?" 

```r
modlda <- train(classe ~ ., data = training, method = "lda", preProcess = "pca")
modrf <- readRDS("rfModel.RDS")

modrf
```

```
## 
## Call:
##  randomForest(formula = classe ~ ., data = training, importance = TRUE,      preProcess = "pca") 
##                Type of random forest: classification
##                      Number of trees: 500
## No. of variables tried at each split: 5
## 
##         OOB estimate of  error rate: 1.11%
## Confusion matrix:
##      A    B    C    D    E class.error
## A 4457    6    0    0    1    0.001568
## B   29 2996   10    1    2    0.013825
## C    1   29 2688   20    0    0.018262
## D    0    0   58 2509    6    0.024874
## E    0    1    2    8 2875    0.003812
```


We'll first run the models on the training data to get a rough estimate of the out-of-sample error when the models are run on the actual testing data set.  The accuracy of the Linear Discriminant model is only 52% using cross validation with the training data set.  Therefore, the out-of-sample prediction error will be greater than 48% (1-52%) using the testing data set.  The testing error for the Random Forest model is less than 1% on the training data (0.0022).  I expect that the out-of-sample error will be larger and likely in the 1-2% range based on the cross validation data set produced by the model generation process (1.11% is the actual predicted error rate).  


```r
pTrainLDA <- predict(modlda, training)
pTrainRF <- predict(modrf, training)
confusionMatrix(pTrainLDA, training$classe)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 2971  702  698  178  265
##          B  536 1206  280  560  501
##          C  502  542 1450  424  426
##          D  428  420  203 1139  323
##          E   27  168  107  272 1371
## 
## Overall Statistics
##                                        
##                Accuracy : 0.518        
##                  95% CI : (0.51, 0.526)
##     No Information Rate : 0.284        
##     P-Value [Acc > NIR] : <2e-16       
##                                        
##                   Kappa : 0.389        
##  Mcnemar's Test P-Value : <2e-16       
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity             0.666   0.3970   0.5296   0.4427   0.4751
## Specificity             0.836   0.8517   0.8539   0.8953   0.9552
## Pos Pred Value          0.617   0.3912   0.4336   0.4532   0.7049
## Neg Pred Value          0.863   0.8548   0.8958   0.8912   0.8899
## Prevalence              0.284   0.1935   0.1744   0.1639   0.1838
## Detection Rate          0.189   0.0768   0.0924   0.0726   0.0873
## Detection Prevalence    0.307   0.1964   0.2130   0.1601   0.1239
## Balanced Accuracy       0.751   0.6244   0.6917   0.6690   0.7151
```

```r
confusionMatrix(pTrainRF, training$classe)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 4460    9    0    0    0
##          B    4 3025    5    0    0
##          C    0    2 2732   11    0
##          D    0    0    1 2556    2
##          E    0    2    0    6 2884
## 
## Overall Statistics
##                                         
##                Accuracy : 0.997         
##                  95% CI : (0.996, 0.998)
##     No Information Rate : 0.284         
##     P-Value [Acc > NIR] : <2e-16        
##                                         
##                   Kappa : 0.997         
##  Mcnemar's Test P-Value : NA            
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity             0.999    0.996    0.998    0.993    0.999
## Specificity             0.999    0.999    0.999    1.000    0.999
## Pos Pred Value          0.998    0.997    0.995    0.999    0.997
## Neg Pred Value          1.000    0.999    1.000    0.999    1.000
## Prevalence              0.284    0.194    0.174    0.164    0.184
## Detection Rate          0.284    0.193    0.174    0.163    0.184
## Detection Prevalence    0.285    0.193    0.175    0.163    0.184
## Balanced Accuracy       0.999    0.998    0.998    0.997    0.999
```


# Prediction
Using the models developed in the prior step, we predict the outcomes of the testing data set using the Linear Discriminant and Random Forest models.


```r
plda <- predict(modlda, testing)
prf <- predict(modrf, testing)
```


# Model Accuracy
Finally, we assess the accuracy of the two models using confusion matrices, which compare predictions versus actuals.  The Random Forest model has a 99.62% prediction accuracy while the LDA model has only a 52.2% accuracy.  The out-of-sample error rates are lower than forecasted using predictions on the training data set.  This is a surprising results since error rates are usually larger on a new data set not used in the model building process.


```r
confusionMatrix(plda, testing$classe)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction   A   B   C   D   E
##          A 766 176 174  40  54
##          B 130 293  69 144 125
##          C 110 128 356 106 113
##          D 103 111  56 287  95
##          E   7  51  29  66 334
## 
## Overall Statistics
##                                         
##                Accuracy : 0.519         
##                  95% CI : (0.503, 0.535)
##     No Information Rate : 0.284         
##     P-Value [Acc > NIR] : <2e-16        
##                                         
##                   Kappa : 0.39          
##  Mcnemar's Test P-Value : <2e-16        
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity             0.686   0.3860   0.5205   0.4463   0.4632
## Specificity             0.842   0.8521   0.8589   0.8887   0.9522
## Pos Pred Value          0.633   0.3850   0.4379   0.4402   0.6858
## Neg Pred Value          0.871   0.8526   0.8945   0.8912   0.8874
## Prevalence              0.284   0.1935   0.1744   0.1639   0.1838
## Detection Rate          0.195   0.0747   0.0907   0.0732   0.0851
## Detection Prevalence    0.308   0.1940   0.2072   0.1662   0.1241
## Balanced Accuracy       0.764   0.6191   0.6897   0.6675   0.7077
```

```r
confusionMatrix(prf, testing$classe)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 1115    0    0    0    0
##          B    1  759    4    0    0
##          C    0    0  680    3    0
##          D    0    0    0  640    0
##          E    0    0    0    0  721
## 
## Overall Statistics
##                                         
##                Accuracy : 0.998         
##                  95% CI : (0.996, 0.999)
##     No Information Rate : 0.284         
##     P-Value [Acc > NIR] : <2e-16        
##                                         
##                   Kappa : 0.997         
##  Mcnemar's Test P-Value : NA            
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity             0.999    1.000    0.994    0.995    1.000
## Specificity             1.000    0.998    0.999    1.000    1.000
## Pos Pred Value          1.000    0.993    0.996    1.000    1.000
## Neg Pred Value          1.000    1.000    0.999    0.999    1.000
## Prevalence              0.284    0.193    0.174    0.164    0.184
## Detection Rate          0.284    0.193    0.173    0.163    0.184
## Detection Prevalence    0.284    0.195    0.174    0.163    0.184
## Balanced Accuracy       1.000    0.999    0.997    0.998    1.000
```


The following table shows the close relationship between the predicted and actual values of classe in the training set using the Random Forest model with PCA preprocessing.

```r
table(prf, testing$classe)
```

```
##    
## prf    A    B    C    D    E
##   A 1115    0    0    0    0
##   B    1  759    4    0    0
##   C    0    0  680    3    0
##   D    0    0    0  640    0
##   E    0    0    0    0  721
```

