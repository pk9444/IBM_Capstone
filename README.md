# IBM Capstone Advanced Data Science Specialization 2023
### Forecasting the Highest Stock Price of RIC over time with Advanced Machine Learning

## Project Goals

- To forecast/predict the daily highest stock price for RICs for the upcoming based on the historical data from January 2019 to 2023.
- Determine what features have the strongest impact on the daily highest price of an RIC with feature engineering.  
- Cross-validate the Random Forest Regressor from the Machine Learning stack and the LSTM Recurrent Neural Network from the Deep Learning stack for the time-series analysis.
- Deploy the best-performing model as an interactive flask-based web application that predicts the next 6-month average daily highest prices in real-time.
- Enable buyers and investors in informed business decisions through actionable insights generated by the forecasts of the Machine Learning/Neural Network model.


 ## Dataset 

 - **Data Source:** https://www.kaggle.com/datasets/luisgilch/stock-data-with-industry-information
 - **Stock Data:** CSV historical data from January 2019 to September 2023 having the daily - highest price, opening price, closing price, and trading volume.  
 - **Sector Data:** CSV sector-based information related to each RIC ticker such as company name, economic and business sector, industry name, etc.

## Technology Stack 

 - **Predictive Analysis:** PySpark, NumPy, Keras, Tensorflow, ApacheSparkSQL, Seaborn, Matplotlib 
 - **Full Stack Data Product:** Flask, HTML, CSS, JavaScript, Chart.js, PySpark, NumPy, Keras.  
     
## Data Product Full-Stack Lifecycle 

![IBM_Capstone_Implementation](https://github.com/pk9444/IBM_Capstone/assets/57378806/26ccd91a-0051-4ce0-bd3e-ad5df513b40e)


## Exploratory Data Analysis Results  

### Stock Dataset: 

- **Data Shape:** 501,625 records and 6 features (columns)
  
- **Number of duplicate records:** 15257

- **NULL Check:**
- ![NULL_check](https://github.com/pk9444/IBM_Capstone/assets/57378806/d0e00130-2071-40df-8653-a782d19c3248)


- **Univariate Analysis: Frequency Distribution and Outlier Detection:**
- ![outliers](https://github.com/pk9444/IBM_Capstone/assets/57378806/cdd4a6b9-3cb1-443f-a85c-7adafbf82c36)

 **The same, nearly identical right-skewed distribution is followed by the Opening and Closing Prices as well. And, they too have a large no. of large outliers like this. 

- **NULL Imputation:**
  - Drop the records that have a NULL date
  - Replace NULLs for highest, opening, closing prices, and trading volume with the **median** for a given RIC for that year and month.
  - Mean imputation is not suitable because it assumes normal distribution and is highly outlier sensitive.
  - On the other hand, directly dropping the NULLs for continuous features may lead to a loss of overall information.
  - Mode imputation does not work here because it is used for categorical features.

- **Multivariate Analysis:**
- ![correlation](https://github.com/pk9444/IBM_Capstone/assets/57378806/def3b9ee-1e81-4d1e-b131-3b12a4904fb1)

- The opening and closing prices have the strongest correlation to the target variable - highest stock price.
- The daily trading volume has a very low correlation compared to the other two features.
- Although very strongly correlated, the opening and closing price may possibly cause multicollinearity.
- To address it, add a new calculated feature called `Daily Return` which is defined as `Opening Price - Closing Price` for a given day. 
 

## Most Important Features Impacting the Highest Stock Price

![feature_importance](https://github.com/pk9444/IBM_Capstone/assets/57378806/e7f5621b-202c-4eaa-9424-0fd277dda0cf)


- The daily opening and closing prices have the strongest impact on the highest price for any given trading day.
- Quantitatively speaking, the opening and closing, together contribute to ~87%-88% importance in determining the highest stock price.
- The daily trading volume has a very low feature importance and hence, it does not add much information in forecasting the highest stock price.
- The daily return also has a low feature importance, but it is a viable candidate to be included in the feature as it is derived from the two most important features. 
- Based on the insights, two possible combinations of feature sets were recommended to predict the daily average highest price:
  - `{Opening Price, Closing Price}` (* throughout the iterative lifecycle, it was found out that this combination performs slightly better)
  - `{Opening Price, Closing Price, Daily Return}` 

## Model Building 

### Random Forest Regressor
![Random_Forest_Model drawio](https://github.com/pk9444/IBM_Capstone/assets/57378806/7994b7f7-03bb-4533-9752-7b5fb9a57d05)


### LSTM Recurrent Neural Network 
![LSTM_Model_Diagram drawio](https://github.com/pk9444/IBM_Capstone/assets/57378806/37299bf7-248b-4136-ae48-d54e4bc37ac1)


### Regression Testing Results 
![Regression_Testing](https://github.com/pk9444/IBM_Capstone/assets/57378806/24433e12-2a6e-468a-b805-550d75a91800)

### Drawbacks of Regression in time-series forecasting 
- The Random Forest regressor fails to capture the temporal nature of time-series data while making predictions for any further timesteps.
- Consider a real-time example -> to predict the highest stock price of an RIC for the upcoming month - November 2023.
- Factors impacting November 2023:
  - The historical `Average Highest Price` for the past at least 6-12 months.
  - And the highest prices for the past 6-12 months in turn depend upon the `Average Opening Price`, `Average Closing Price` based on feature engineering. 
-The Random forest, or any regressor for that instance, **fails to capture this very temporal or seasonal nature of a time-series historical data**.
- It can only predict based on the feature set for the current instance which is not available for the next months.
- Hence, this explains its bad performance on the Regression metrics and explains why it is not suitable for time-series forecasting.

### Advantages of the LSTM Recurrent Neural Network  
- On the other hand, the LSTM captures this very temporal aspect by creating the lag sequences from the feature vector and feeding it as input to the LSTM's output layer. 
- Consider the same real-time example as aforementioned.
- For a given RIC:
  - LSTM creates lagging sequences of the feature vector for the past 12 months, depending upon how many lags one wants to create.
  - Each lag captures the feature vector for the preceding month and so on. 
  - So, the forecasted highest price for November 2023 includes the sequence {`Average Opening`, `Average Closing Price`} for the last 12 months.
  - Define the number of output layers for how many predictions are required. For forecasting the next N-months, set `N + 1` layers - the current month + the upcoming N months. 
- In this way, the LSTM captures the temporal aspect of historical data in time-series which explains its much better performance on the Regression Testing metrics.

## Full Stack Deployment 

![Full_Stack_Deployment drawio](https://github.com/pk9444/IBM_Capstone/assets/57378806/0ab64f13-a417-4a13-a5f2-c13e2afe4a6c)


## Project Assets - Jupyter Notebooks GISTs

- EDA - https://gist.github.com/pk9444/801214f0decdaf769fa58d17f1453f36
- ETL Data Cleansing - https://gist.github.com/pk9444/2179049e5c45774e0f92b2121b88534d
- ETL Feature Engg - https://gist.github.com/pk9444/0c6f6bab39a4e8a9abf8447eb4c7252b
- Model Building - https://gist.github.com/pk9444/a49749592e53073b7d223560a73bbca5
- Data Product - https://gist.github.com/pk9444/eb0ca6cf045737b33a12661a7dd1066f

- ## YouTube Business Data Product Pitch 

- https://www.youtube.com/watch?v=e_XHJIX08wA







