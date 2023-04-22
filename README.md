# Patient-survival-prediction-using-Tree-Algorithms-in-R-and-PySpark.

This is a project on predicting a patient's survival depending on their medical parameters. The dataset is collected from
Kaggle with the link given below. It consists of different parameters collected when a patient is admitted like medical factors w.r.t blood pressure, heartrate, respiratory rate, temperature etc., demographic factors like age, gender, ethnicity etc., identification factors like patient ID, icu ID, hospital ID, etc.

Kaggle Link - https://www.kaggle.com/datasets/mitishaagarwal/patient
Dataset is 31.41 MB of size and stored as a CSV file.


# Data preparation 

The dataset is cleansed and prepared for exploratory analysis in R. Columns which have more than 5% missings values were dropped. 

The numerical columns are imputed with the median whereas the categorical columns are imputed with mode. 
However, the binary variables are imputed using logistic regression from the ‘mice’ package. 

Duplicate instances were removed using the 'unique()' function. 

Furthermore, after numerically exploring the dataset using the 'diagnose()' function, I also observed that many of the numeric variables have to be changed to factors. I changed these variables to factor by seeing which of them have unique count of 2. Changing the data type of binary variables from numeric to factor is done by the help of ‘as.factor()’ function.

In terms of outliers, they generally have 3 effects on the model generation which are skewing the data, changing the overall statistical distribution of data w.r.t mean, variance, etc, and leading to a bias in the accuracy level of our model and data interpretation. In some datasets it is an efficient approach to remove the outliers to squeeze better results out of the analysis or prediction while some analysis require to consider them as they contain critical and valuable information needed. However, I decided to remove the outliers from the analysis for a more generic and safer approach towards biasing the prediction accuracy. Another reason to not include outliers is due to the usage of K-Means clustering for exploratory data analysis because K-means is sensitive to outliers and therefore removing of outliers for this was more suitable. 
'diagnose_outlier()' and 'plot_outlier' gave a more insightful report on numerical and visual characteristics of outliers in the dataset. These were removed using the 'which()' function. 


# Exploratory data analysis

In this section, we see how the data is explored in depth regarding the patient’s survival with the help of exploratory tools like normality, correlation, principal component analysis and K-means clustering.

1. Normality 

The normality of the data w.r.t the patient’s survival is explored using normality statistics, density and Q-Q plots of the numeric variables in the dataset.

The normality statistics can be yielded through looking closely which variables have statistics closer to 1 and later reconfirm by looking at their density and Q-Q plots. The variables recording minimum parameters have more normality meaning they are less skewed and have relation w.r.t the patients survival. We can see that parameters like d1_diasbp_min, d1_diasbp_noninvasive_min, d1_heartrate_min, d1_mbp_min, d1_mbp_noninvasive_min, d1_resprate_min, d1_spo2_min, d1_sysbp_min, d1_sysbp_noninvasive_min, d1_temp_min, h1_diasbp_min, h1_heartrate_min, h1_resprate_min, h1_spo2_min and h1_sysbp_min are more normally distributed than the maximum parameters concluding that as the parameters tend to drop towards the minimum, there is some relation with the patients survival i.e. eventually leading to the patient’s death. We can confirm this by also seeing the normality plots and Q-Q plots that these minimum parameters are more normally distributed than the maximum ones. Some examples of such normally distributed minimum parameters are given below. The non-inclusive usage of normality tests is a very critical thing to reflect here. Normality tests like Shapiro-Wilk tests have best use cases in terms of theoretical normality. In large datasets, these tests will discover even very minute and modest deviations from theoretical normality. This arises a question of doubt in data scientists mind in the practical concerns of using these normality tests. Therefore, we have chosen to look for normality in the density and Q-Q plots.

2. Principal Component Analysis (PCA)

Having a lot of data can be a curse in machine learning because too many features or dimensions can reduce the model’s accuracy. Since this dataset is large with too many noise columns/features, I'm using dimensionality reduction in PCA to feature extract desired variables which are most important for prediction.  PCA replaces original variables with new variables called principal components, explained variance is the amount of variance explained by each component. 

3. Clustering

Clustering is an unsupervised learning method to segregate data points into groups for better understandability of the data using similarity/dissimilarity and distinctiveness metrics. The main idea is to put each data point into a cluster of similar data points which are dissimilar to data points belonging to a different cluster. This is done through various similarity measures like vectors (cosine distance), sets (jaccard distance) and points. In this dataset, K-Means clustering is used to determine the clusters where a pre-defined level of clusters ‘n’ and ‘k’ is set to use as a preliminary centroid datapoint. This centroid starts a repetitive jump to the subsequent datapoint which is similar to it and becomes the centroid until all the similar datapoints are captured inside the cluster. The main advantage of using K-means is because it’s easy to implement, faster and flexible on large datasets but the drawback is that its sensitive to outliers which is why we removed the outliers initially. Another drawback is to specify the number of centroids before clustering is done. 


# Machine Learning Prediction

In this analysis, we see how Decision trees and Random forests are used to predict the patient’s survival by using the various medical parameters recorded when admitted. Random forests are originally an ensemble of decision trees which combines the output of multiple randomly generated Decision Trees while Decision trees are a series of sequential decisions to just form 1 tree. We can also relate Random Forests to the concept of bagging, where several subsets of data are derived from base dataset. Similarly, in random forests the division of features along with the data are used to grow the separate individual trees. The repetitive nature of random forests makes it less overfitted than decision trees producing less deeper trees also boosting the accuracy at a cost of increased storage & computational power. 

In this dataset, we start by using stratified random sampling on the data w.r.t ‘hospital_death’ to balance the distribution since the imbalance in data can create bias to our prediction. Sampling also help is in run time, balancing the majority class which is ‘0’ in our target variable by using proportioning/fractioning of the distribution. The main motive to do stratified sampling is to retain the overall population characteristics of the dataset similar in our sample. This removes the concept of bias in the way sampling is done (Investopedia, n.d.). Now we move on to use vector assembler to transform the numerical columns into a single vector called features and string indexer to encode ‘hospital_death’ into label indices. 

Both the assembler and indexer are defined as features and labels first and then transformed on the dataset. Next step is to split the dataset into train and test for prediction purposes. The concept of splitting the data is to use the train set to feed in data into the Random Forest algorithm to learn about the data and its interactions and then use the test set to evaluate how good the algorithm has learnt about the data to predict about it. The dataset is divided on a general 70/30 split giving 10190 and 4315 records respectively. The algorithm for decision tree and random forest is almost the same where we assign the features and labels into a classifier and then fit this into the train set. This creates a classification tree through which the predictions measures are obtained. ’prediction’ is the predicted label of ‘hospital_death’, ‘rawPrediction’ is the measure of train labels at the node of the tree assisting in the prediction, whereas ‘probablity’ is the normalized raw prediction on a scale of 0-1. Although, the decision trees are easy to build and interpret, random forests are a combination of randomly generated multiple trees where each tree is a predictor making it slightly more difficult to interpret. Thus, using random forest gives a boost in the accuracy from the basic level decision tree. To accomplish further more prediction accuracies, we can use Gradient-Boosted Tree algorithm which uses concept of boosting the weak predictors by using information about previous built trees whereas Random Forests depend on the idea of bagging by averaging the prediction of all individual trees. The depth of trees is comparatively standardized to 3 as unlike Random Forests because building individual trees can soon end in overfitting the data. In conclusion, the high accuracy rate of GBT is achieved at the cost of small tree depth which are difficult to interpret and harder to tune also resulting in larger bias than the other 2 algorithm.


# Performance Evaluation and Comparison of methods
 
The prediction accuracy of the algorithms is done by using a ‘BinaryClassificationEvaluator()’ on the predictions obtained from the algorithm’s trees. The accuracy used in the evaluation in these algorithms is Receiver Operating Characteristic (ROC) value of the predictions which ranges from 0 to 1 which is a plot of the false positive versus true positive rate of the predictions. Given below are the accuracy results of Decision Tree, Random Forests and Gradient Boosted Trees.

The basic level decision tree gives the lowest accuracy of 0.7183 but they are easy to interpret since they produce only 1 tree. An improved accuracy can be achieved by tuning the model with bagging (Random Forests) gives 0.7984 and boosting (GBT) gives 0.8069. In thirst of a higher accuracy rate, the level of interpretation of trees becomes more complex, difficult and time consuming taking up more computational power and as also the number of trees produced (RF and GBT) and features increase. As we can also see that the number in predicting 0.0s increases with increasing accuracy in the algorithms. This means that if we want to know how the algorithm is working and want to interpret the model w.r.t the patient’s survival, then we can use decision trees. But if a boost is needed compromising this interpretability, then we can use Random Forest or GBT. On a comparative note, Random forests are the most efficient for prediction if there is less need for interpretation and want better performance since Gradient-boosted takes more time, harder to tune and easily overfits. Hence, random forests sit at the perfect intersection. Also, the removal of outliers might have lost some critical information required to train the algorithms. Therefore, there is a possibility that there could be a boost in all algorithms considering the inclusion of outliers because the trees are not sensitive to outliers. 


# Discussion and findings

In this analysis, we first explored the dataset seeing how the different parameters recorded when a patient is admitted interact with each other and ‘hospital_death’ to predict if the patient would survive or not. The exploratory data analysis brought light on how different parameters like blood pressure, heartrate, respiration and temperature affect the patient’s body and also saw how they are correlated with each other. They also contribute to predicting the patient’s survival. In the data preparation stage, there are many identifiers like icu_id, hospital_id, etc., which are recognised as numeric variables. The removal of outliers might have lost some critical information for prediction, thus including them intuits the possibility of getting a higher prediction accuracy in all algorithms. The correlation in the parameters affecting ‘hospital_death’ are also an important thing to notice because it tells us about the predictors for patient’s survival. While doing cluster analysis, it’s the analyst’s choice to choose the number of ‘k’s for cluster formation. Another suggestion that could be done towards sampling of data is to include SMOTE undersampling or oversampling of classes which synthetically generates data accordingly using ’k-NN’. Using SMOTE, we can increase the recall of prediction at the cost of precision. Although generation of synthetic data is synthetic, SMOTE balances the majority and minority classes. Using such predictive analysis in the healthcare industry, the patient’s hour 1 and day 1 records are fed into algorithms with the kind of disease they are infected with. These algorithms can predict the patient’s survival or how worse the patient’s condition is. This helps in assisting the doctors in treating the patients more closely and create a plan to treat the patients more effectively. 
