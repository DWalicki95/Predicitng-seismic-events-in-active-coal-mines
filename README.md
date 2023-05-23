# Predicitng-seismic-events-in-active-coal-mines

1. Introduction

This project is about the prediction of seismic events in active coal mines. The safety of miners is the most important requirement for the coal mining industry. Coal mining companies are obligated by law to introduce many safety measures, but sometimes systems fail, which can have fatal consequences. The purpose of this project is to find methods for predicting periods of increased seismic activity in miners' workplaces. This was the AAIA'16 Data Mining Challenge from KnowledgePit - a data challenge platform (https://knowledgepit.ml/aaia16-data-mining-challenge/). 

2. Requirements to run

There are IPython Notebook (.ipynb) files and a .py file. To run the program, you need libraries such as numpy, pandas, matplotlib, seaborn, sklearn, statsmodels, imblearn, random, lightgbm, xgboost, and catboost.

3. Datasets

The training file contains 133,151 records, each corresponding to 24 hours of measurements. Values stored in a single record can be divided into two separate parts. The first part consists of an identifier of the main working site and 12 other characteristics related to the entire 24-hour period described by the record. The second part is composed of hourly aggregated measurements; thus, for each characteristic, it includes 24 consecutive values. The data has a total of 541 columns. There is also a separate file available with additional information about all main working sites included in the data (in both the training and test parts). We also provide a separate file with the names of the columns (attributes) in the training and test datasets.

Labels in the data indicate whether the total seismic energy perceived within 8 hours after the period covered by a data record exceeds the warning threshold (i.e., 5*10^4 Joules).

It is important to note that time periods in the test data do not overlap, and they are given in a random order.

The test set consists only of 3,860 unlabeled observations. The observations in the test set were randomly selected, rather than chosen as time series as provided within the training set. From the series of consecutive observations, samples were uniformly drawn to form a test set.

The most important part of the task was to create a classification model that generalizes well to new locations. The sets of locations differ between the training and test datasets. The model should be both location and time-independent.

A second significant problem was the extremely large class imbalance.
![datasets_balance](https://user-images.githubusercontent.com/115831899/235488277-30dae94f-9d1c-4136-88b1-4bd073111466.png)

To fully understand the dataset and the problem, please open data_loading.ipynb.  

4. Guide 

In the table below lists the files and their descriptions.

| No. | File | Description | 
| :-------: | :--------: | :-------: |
| 1 | *data_loading* | Data overview |
| 2 | *data_processing_0* | Basic approach, no time series(TS) transformations |
| 3 | *data_processing_1* | First approach for TS transformations | 
| 4 | *data_processing_2* | Second approach for TS transformations | 
| 5 | *data_processing_3* | Third approach for TS transformations |
| 6 | *data_processing_4* | Fourth approach for TS transformations |
| 7 | *datasets_approaches_init_evaluation* | Choosing the best approach for TS transformation|
| 8 | *model_experiments* | data scaling, class balancing, feature selection, dimensionality reduction, models tests, threshold balance  |
| 9 | *main_model* | final pipeline, all steps together |

The idea for solving the problem was to test five different approaches for time series transformation: one with almost no transformation and four calculating various statistics from different time intervals. Some approaches used data from an additional dataset, others did not.

These five approaches were then tested in datasets_approaches_init_evaluation.ipynb. The first one turned out to be the best.

To see how the model was developed, you need to run the files in the following order:

data_loading ---> data_processing_1 --> model_experiments

You can find the full model, with all operations condensed into one pipeline, in main_model.

To fully understand the problem-solving process, I encourage you to review all the files in the order shown in the table above.

5. Methodology

The first approach to feature engineering involves the following steps:

Keep the initial general columns,
Convert categorical features to integers,
Sum the count_e*, sum_e*, number_of_rock_bursts, highest_bump_energy (respectively: number of bumps, energies of bumps) over each time series period - 24 hours.
Aggregate time series for max_gactivity, max_genergy, avg_gactivity, avg_genergy, max_difference_in_gactivity, max_difference_in_genergy, avg_difference_in_gactivity, avg_difference_in_genergy in the following way:
Compute statistics such as average and absolute values, standard deviation, maximum and maximum absolute values, last 5 hours: average, standard deviation, slope of the linear regression with respect to time.

| No. | Problem | Tested methods | Best method | 
| :-------: | :--------: | :-------: | :-------: |
| 1 | Categorical features| One-hot encoding, Ordinal encoding | Ordinal encoding |
| 2 | Data scaling | StandardScaler | StandardScaler |
| 3 | Class balancing | Without resampling, SMOTE, ADASYN, SMOTE+RandomUnderSampler, SMOTETomek Links, SMOTEENN | SMOTE |
| 4 | Feature selection | Pearson correlation+RFE+SelectFromModel | Pearson correlation+RFE+SelectFromModel |
| 5 | Dimensionality reduction | Without, PCA | Without |
| 6 | Model selection| Logistic Regression, Random Forest, LightBGM, Catboost, Xgboost | LightGBM |
| 7 | Precision vs recall | Threshold adjust | Threshold reduction |

6. Results

Five models were used for experiments: Logistic Regression, Random Forest Classifier, LightGBM Classifier, Catboost Classifier, XGBoost Classifier.
Two of these five classifiers were rejected first, as they gave hopeless results (Catboost and Random Forest).
Three of them exhibited good overall results. Logistic Regression showed slightly worse cross-validation score results than the other two models. However, none of the models were overfitted.
The final choice of algorithm depends on the business need and should be consulted with the business before implementation.
Notwithstanding this, in my view, in active coal mines, the safety of workers is paramount, so the key metrics are both ROC AUC and recall, which are much more important than precision. This results in detecting more True Positives (Warnings in this case), even if many of the positives are False Positives. On the other hand, I was guided by not too low precision, as it results in stopping work in the mine too often.
According to the above, the best model was LightGBMClassifier. It exhibits the highest roc_auc_score (~0.88), 0.93 recall of class 1, and 0.23 precision of class 1. Close behind was XGBoost with an even higher recall (0.95), but lower precision (0.2) and roc_auc, and a slightly worse Logistic Regression. The winning algorithm has another advantage - it learns really fast.

<b>LightGBM</b> results:

* AUC: 0.88

| Class | Precision | Recall | F1-score |
| :-------: | :--------: | :-------: | :-------: |
| Normal | 1.00 | O.83| 0.91 |
| <b>Warning</b> | 0.23 | 0.93 | 0.36 |

![lightGBM - confusion matrix](https://user-images.githubusercontent.com/115831899/235650354-18969f4b-6b3d-4abb-a3dd-178f80b52c08.png)


7. Sources

* Challange page: https://knowledgepit.ml/aaia16-data-mining-challenge/
* Thanks for datasets sharing for Mr Andrzej Janusz,
* A.Janusz, M.Grzegorzewski, M.Michalak, Ł.Wróbel, M.Sikora, D.Ślęzak, "Predicting seismic events in coal mines based on underground sensor measurements", Engineering Applications of Artificial Intelligence 64, 83-94, 2017
* R. Bogucki, J. Lasek, J. Kanty Milczek, M. Tadeusiak, deepsense.io, "Early Warning System for Seismic Events in Coal Mines Using Machine Learning", Proceedings of Federated Conference on Computer Science and Information Systems pp.213-220, 2016
* E.Zdravevski, P.Lameski, A.Kulakov, "Automatic Feature Engineering for Prediction of Dangerous Seismic Activities in Coal Mines, Proceedings of the Federated Conference on Computer Science and Information Systems pp. 245-248, 216.
