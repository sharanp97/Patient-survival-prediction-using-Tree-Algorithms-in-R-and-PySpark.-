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
