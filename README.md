# MachineLearningFinalProject
This project analyzes data collected on six individuals wearing a personal tracking device.
The project preprocesses 159 covariates and trains a machine learning algorithm to accurately predict the outcome of a weightlifting exercise based on a subset of the covariates using a Random Forest model and PCA data preprocessing.
The data is sourced from http://groupware.les.inf.puc-rio.br/har.
The initial challenge was cleaning up the data; many of the variables were processed as Factors but were really numeric.
A second challenge was managing the large number of NAs in the data.  The program calculates the percent of NA's in each column and removes columns with more than 50% of values that are NA.
A third challenge was dealing with a large number of correlated covariates.  The program resolves this issue by removing covariates with correlations in excess of 70%.  The program also employes Principal Components Analysis (PCA) in preprocesing the data to identify the minimal set of factors that explain variation in the predicted variable, classe.
The final challenge was choosing the best machine learning algorithm for the classification problem.  Analysis demonstrates that the Random Forest algorithm has an accuracy rate in excess of 98% on the testing data set and correctly identified 20 of 20 additional cases.
