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

To fully understand the dataset and also the problem please open _data_loading.ipynb_.   Results
