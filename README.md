# Predicitng-seismic-events-in-active-coal-mines

1. This project is about prediction of seismic events in active coal mine. Safety of miners is the most important       requirement for the coal mining industry. Coal mining companies are obligated by the law to introduce many safety measurements, but sometimes systems fail which have fatal consequences. The purpose of this project is to find methods for predicting periods of increased seismic activity in a miners' workplace. This was AAIA'16 Data Mining Challenge from knowledgePit - a data challange platform (https://knowledgepit.ml/aaia16-data-mining-challenge/).  

2. Requirements to run

There are IPython Notebooks (.ipynb) files and .py file. To run the program You need libraries like numpy, pandas, matplotlib, seaborn, sklearn, statsmodels, imblearn, random, lightgbm, xgboost, catboost.

3. Datasets

Training file contains 133,151 records, each corresponding to 24 hours of measurements.  Values stored in a single record can be divided into two separate parts. The first part consists of an identifier of the main working site and 12 other characteristics related to the whole period of 24 hours described by the record. The second part is composed of hourly aggregated measurements, thus for each characteristic it includes 24 consecutive values. There is a total number of 541 columns in the data. There is also available a separate file with additional information about all main working sites included in the data (in the training and test parts). We also provide a separate file with names of columns (attributes) in the training and test data sets.

Labels in the data indicate whether a total seismic energy perceived with 8 hours after the period covered by a data record exceeds the warning threshold (i.e. 5*10^4 Joules).

It is important to note that time periods in the test data do not overlap and they are given in a random order.

The test set consists only of 3 860 unlabeled observations. The observations in the test set were randomly selected rather than time series as provided within the training set. From series consecutive obserations samples were uniformly drawn to form a test set. 

The most important part of the task was to create a classification model that generalizes well to new locations. The sets of locations differ between the training and test dataset. The model should be both location and time independent.

Second significant problem were extremely large class imbalance. 
![datasets_balance](https://user-images.githubusercontent.com/115831899/235488277-30dae94f-9d1c-4136-88b1-4bd073111466.png)

To fully understand the dataset and also the problem please open _data_loading.ipynb_.  

4. Guide 

In the table below lists the files and their description.

| No. | File | Description | 
| :-------: | :--------: | :-------: |
| 1 | ****data_loading**** | Data overview |
| 2 | ****data_processing_0**** | Basic approach, no time series(TS) transformations |
| 3 | ****data_processing_1**** | First approach for TS transformations | 
| 4 | ****data_processing_2**** | Second approach for TS transformations | 
| 5 | ****data_processing_3**** | Third approach for TS transformations |
| 6 | ****data_processing_4**** | Fourth approach for TS transformations |
| 7 | ****datasets_approaches_init_evaluation**** | Choosing the best approach for TS transformation|
| 8 | ****model_experiments**** | data scaling, class balancing, feature selection, dimensionality reduction, models tests, threshold balance  |
| 9 | ****main_model**** | final pipeline, all steps together |

The idea for solving the problem assumed to test five different approaches for time series transformation: almoast without transformation and with calculating various statistics from different time intervals. Some approaches used data from an additional dataset, others did not. 

Then these 5 approaches were tested in ****datasets_apporaches_init_evaluation.ipynb****. The first turned out to be the best.
The first apporach to feature engineering is to:
* kept first general columns,
* categorical features converted to integers,
* count_e*, sum_e*, number_of_rock_bursts, highest_bump_energy (respectively: number of bumps, energies of bumps) summed over each time series period - 24 hours. 
* aggregate time series: max_gactivity, max_genergy, avg_gactivity, avg_genergy, max_difference_in_gactivity, max_difference_in_genergy, avg_difference_in_gactivity, avg_difference_in_genergy in a following way:
  * compute statistics like average and absolute values average, std, max and absolute values max, last 5 hours: average, std, slope of linear regression with respect to time.

To see the way to develop the model you need to run the files in a following way:
**data_loading** --- > **data_processing_1** -- > **model_experiments**

Full model with all operations condensed in one pipeline you can find i **main_model**.

To fully understand problem solving I encourage to see all the files in the order shown in the table above.

