# 

# What drives the price of a used car?

## 1.  Business Understanding

A group of used car dealers are interested in fine-tuning their inventory. Therefore, they are seeking recommendations on what consumers value in a used car.

The goal of this project is to understand what factors make a used car more or less expensive. A brief report that highlights these factors and recommendations will be developed and delivered.

## 2. Methodology

The Cross-Industry Standard Process for Data Mining (CRISP-DM) framework is applied to guide this effort. The framework includes six phases: business understanding, data understanding, data preparation, modeling, evaluation, and deployment. This project will focus on the first five phases of the CRISP-DM framework.

After understanding the business objectives, the collected data will be explored by using visualizations and probability distributions to form initial findings and hypothesis. Then, data will be prepared to handle any integrity issues and cleaning. Features or attributes will be engineered for modelling. Next, a few different predictive regression models with the price as the target will be built with different and, hopefully, optimal parameters. They are Ridge, Lasso and Linear regression models with a cross-validation method applied. Lastly, one model that appears to have high quality (compared to the baseline and the other built models) will be validated and inspected.  The importance of each feature will be identified to determine if meaningful insights on drivers of used car prices can be provided.

## 3. Data Understanding

### 3.1 Data Overview

The original dataset contained information about 3 million used cars. The dataset for this project contains information about 426K cars to ensure speed of processing, particularly on a home laptop. The dataset describes different attributes of a used car and its price. Below are the attributes and description of the initial data:

| **No.** | **Attribute Name** | **Attribute Description**                                                                                                   | **Attribute Type** |
| ------- | ------------------ | --------------------------------------------------------------------------------------------------------------------------- | ------------------ |
| 1       | id                 | Identification                                                                                                              | int64              |
| 2       | region             | Region: Florida Keys, South Jersey, Jackson Ville, etc.                                                                     | object             |
| 3       | price              | Car price                                                                                                                   | int64              |
| 4       | year               | Manufactured year of the car : 2007, 1989 etc.                                                                              | float64            |
| 5       | manufacturer       | Car manufacturer: Toyota, Volvo, Aston-Martin, etc.                                                                         | object             |
| 6       | model              | Model of the car: Corvette, Sierra 2500, etc.                                                                               | object             |
| 7       | condition          | Condition of a car: salvage, fair, good, excellent, like new, new                                                           | object             |
| 8       | cylinders          | Number of car cylinders: 3 cylinders, 4 cylinders, 5 cylinders, 6 cylinders, 8 cylinders, 10 cylinders, 12 cylinders, other | object             |
| 9       | fuel               | Car energy sources: gas, diesel, hybrid, electric, other                                                                    | object             |
| 10      | odometer           | Distance travelled by the car                                                                                               | float64            |
| 11      | title_status       | Status of the official legal owner document: missing, parts only, salvage, rebuilt, lien, clean, other                      | object             |
| 12      | transmission       | Automatic, Manual, Other                                                                                                    | object             |
| 13      | VIN                | Unique identifier of the car                                                                                                | object             |
| 14      | drive              | Type of car drive: 4wd (4 wheel drive), fwd (front wheel drive), rwd (rear wheel drive)                                     | object             |
| 15      | size               | Car size: missing, full-size, mid-size, compact, sub-compact                                                                | object             |
| 16      | type               | Car type: sedan, SUV, pickup, truck, coupe, hatchback, wagon, van, convertible, mini-van, offroad, bus, other               | object             |
| 17      | paint_color        | Car color: white, black, silver, etc.                                                                                       | object             |
| 18      | state              | State: NY, UT, etc.                                                                                                         | object             |

### 3.2  Exploratory Data Analysis

#### 3.2.1 Data Quality

After investigating the dataset for missing or problematic data, some observations are listed below:

- Most features or attributes are categorical
- Special characters are included in the data
- The dataset contains outliers. Many values of the car prices seem to be data errors because there are prices recorded as "1234567" or "12345678" or "1234567890"
- The following attributes/features include NULL values. The "size" feature has more than 70% NULL values and can be dropped

![fig1](images/null.png)

#### 3.2.2 Explore the target attribute - Price

The standard deviation of the "price" feature is quite large: 12,182,282.17 with min value of 0 and max value of 3.736929e+09. After detecting outliers by using the boxplot, the dataset was split into three datasets based on quartile (1st, 4th and 2nd+3rd) for visualization.

**"Price" in the 1st quartile:** Many cars in the 1st quantile are less than $500 and are parts only or are missing title.

![fig2](images/1stquartile.png)

**"Price" in the 4th quartile**: 

- Many observations in the 4th quartile are outliers and/or error data (e.g. prices were recorded as "123456789"). It appears that observations that have prices greater than 1,000,000 fall into this category and can be later dropped.

- There are only about 100 observations with the prices greater than 200,000 and many of them appear to be errors as mentioned above.

- "Ferrari", "Aston-Martin" cars stand out as the top manufacturer in 26K - 200K price range. Surprisingly, “salvage” condition is included in this price range.
  
  ![fig3](images/4thquartile.png)

**"Price" in the 2nd and 3rd quartile**: 

- The 2nd + 3rd quartile dataset is expanded to include some observations in the 1st quartile and the 4th quartile (lower bound = 500 and upper bound = 200K) to hopefully gain a better understanding of the data via distribution and visualization.

- The violin plot below indicates that prices are concentrated within the range below 25,000.
  
  ![fig4](images/IQR.png)

#### 3.2.3 Explore the relationship of the Price feature and other numeric features

The dataset which includes observations with prices within the range of 500 - 200,000 was used to plot and to further explore the initial data.

###### Price, Year, and Drive features:

- Used cars tend to have better values when they are recent or very old. Perhaps, it is because these old cars are classic cars.

- Used cars which are manufactured around the year 2000 (e.g. 10-15 years old) seem to have the lowest value.

- Rear wheel drive cars seem to dominate the classic car population while recent year cars tend to be front wheel drive cars (less expensive) and four wheel drive cars (more expensive).
  
  ![fig5](images/price_year_sample.png) 

###### Price, Odometer and Transmission features:

- It is not surprising that cars have better price with lower odometers. They are mostly automatic.

#### 3.2.4 Explore the relationship of the Price feature and other categorical features

  ![fig6](images/count_cat.png)

###### Price and Fuel features:

- Diesel cars are the most expensive, followed by eletric cars and other sources to power cars. 
- However, many cars available in the dataset are gas and have lower prices

###### Price and Condition features:

- Cars with "new" condition perform best in price, followed by "good", "like new" and "excellent" conditions. Cars with "fair" and "salvage" conditions have low values.

- Many cars available in the dataset are "good" and "excellent"

###### Price and Title Status features:

- Cars with Lien and Clean titles are more expensive. Not surprisingly, parts only and missing title cars have lower price
- Most cars available in the dataset have "clean" titles

###### Price and Cylinders features:

- Most cars have 4 or 6 or 8 cylinders
- Prices increase by the even number of cynlinders: 4, 6, 8, 10, 12 cynlinders

###### Price and Paint Color features:

- Most cars are white or black and they also have good prices

###### Price and Type features:

- Mini van category is the lowest valued category while interestingly, used pickups have good prices

###### 

![fig7](images/box_cat.png)

###### Price and Manufacturer features:

- Ferrari and Aston-Martin cars stood out and have the highest prices while most cars are Ford, Toyota and Chevrolet

###### Price and State features:

- California and Floria lead in volume of cars. There is no obvious differences in prices across all states
  
  ![fig8](images/count_state_manufacturer.png)
  
  ![fig9](images/box_state_manufacturer.png)

## 3.2.5 Hypothesis

- Odometer seems to have a strong negative correlation with price. Cars with low odometer have better values.

- Prices of used cars seem to be influenced by their manufactured year. However, the relationship seem to be non-linear. Classic cars (the year value is small) and recent year used cars (the year value is high) tend to have better values. Cars manufactured approximate in 2000 seem to have the lowest prices. It appears that these cars are old but not old enough to become a valuable classic.

- Manufacturer appears to play an important role in prices.

- The "model" attribute is a categorical feature with extremely high cardinality which impedes the ability to plot. Intuition suggests that its significance (if any) should not be ignored.

## 4. Data Preparation

### 4.1 Data Cleaning and Selection

- **Cleaning:** Leading, trailing spaces and special characters were removed.

- **Identify and remove less valued features:** Attributes which are identified as less useful were dropped. They are "id" and 'VIN' variables. The "size" column contains more than 70% of Null values and was dropped. In addition, "state and "region" are related and one of them ("state") was removed.

- **Identify and remove outliers:** Outliers were removed by using the interquartile technique with a modification of the interquartile range (IQR). The 1.5 x IQR rule is applied to the "odometer" feature. For the "price" and "year" features, the lower bound or the upper bound were modified based on the results from the exploratory data analysis. This approach of adjusting the IQR bounderies attempted to balance data lost and model performance.
  
  | No. | Attributes | Min    | Max      | Count - Before | Count - After |
  | --- | ---------- | ------ | -------- | -------------- | ------------- |
  | 0   | Price      | 500.0  | 57322.0  | 426880         | 372713        |
  | 1   | Year       | 1920.0 | 2022.0   | 425675         | 371733        |
  | 2   | Odometer   | 0.0    | 277231.0 | 422480         | 370669        |

### 4.2. Data Transformation

**Fill Null values:** Except the "size" column which contains more than 70% of Null values, Null values of the remaining features were not dropped but instead were imputed. Numeric attributes were imputed with mean values. Most frequent values or "other" were imputed for the remaining categorical attributes.

**Encode categorical features:** Non-numeric attributes were converted to numeric to feed into the to-be-built models. Categorical features are encoded based on type (ordinal or nominal) and cardinality (low or high). Below is the list of ordinal categorical features with low cardinality and their encoded order:

| **Features** | Values and Orders                                                                                                  |
| ------------ | ------------------------------------------------------------------------------------------------------------------ |
| cylinders    | 'other', '3 cylinders', '4 cylinders', '5 cylinders', '6 cylinders', '8 cylinders', '10 cylinders', '12 cylinders' |
| size         | 'subcompact', 'compact', 'midsize', 'fullsize'                                                                     |
| title_status | 'missing', 'parts only', 'salvage','rebuilt', 'lien', 'clean'                                                      |
| condition    | 'salvage', 'fair', 'good','excellent', 'like new', 'new'                                                           |

Nominal categorical features with high cardinality were encoded by using
the Target Encoding technique as one-hot encoding method would increase the
dimension of the dataset significantly. Although this technique might introduce
a risk of target leakage and subsequently a special risk of overfitting, it is
a powerful encoding technique for categorical features with high cardinality. Categorical features with high cardinality are present in the dataset. The highest cardinality feature is the "model" feature which was not dropped to minimize data
loss. As a result, the target encoding technique was employed and the risk of
overfitting was mitigated with cross-validation.

**Scaling and normalization:** Attributes were scaled with the standard scaler. Target feature, 'price', was normalized with the quantile transformation technique.

 ![fig10](images/target.png)

**Correlation Matrix:** The correlation matrix below inllustrated that the top four features correlated to the price features are "model", "odometer", "year" and "type". 

![fig11](images/correlation.png)

## 5. Modeling

### 5.1 Train models

**Cross-validation:** Holdout cross-validation was implemented. Models were train on the training set and validated with the test set.

**Model technique:** Three multivariate regression models were built. They are Ridge Regression, Lasso Regression and Linear Regression.

**Hyperparameter tuning:** The popular GridSearchCV was not used to tweak model performance for optimal results because it can be computationally expensive for this dataset. Instead, RidgeCV and LassoCV modules were used because these modules implement Ridge regression and Lasso regression with built-in cross-validation of the alpha parameter - the hyperparameter. By default, they work in the same way as GridSearchCV with Leave-One-Out Cross-Validation to select an optimum alpha parameter.

**Feature selection:** RidgeCV and LassoCV were train with all transformed features of the trtrainataset. SelectFromModel was used to select features for the Linear Regression model.

**Validation metrics:** Two measuring metrices are used to validate how well the predictions of the train models approximate the real data values. They are:

- R2 (R-Squared) or Coefficient of Determination

- RMSE or Root Mean Squared Error (loss)

R2 is the default scoring in many sklearn models. The best possible R2 score is 1.0 indicating all variance in the target variable can be explained by the model. The RMSE score was added as it is sensitive to outliers and due to its ease of interpretation. It measures the average difference between the predicted and actual values in the same unit. The lower RMSE score indicates a better performance and the best possible RMSE score is 0 since it is a loss measurement.

#### 5.2 Validate and Select Model

Below is the result of the model validation:

**Scoring:** The scoring table below illustrates that all three models achieved similar performance. The RMSE scores of all three models are higher the baseline which is 12,578 on test set. Ridge Regression performed best with a small margin. Models were scored on both the train set and test set. A slight increase in the RMSE loss score and a decrease in the R2 score on the test set indicated that the models were not overfitted. 

| No. | Model                     | R2   | RMSE     |
| --- | ------------------------- | ---- | -------- |
| 0   | RidgeCV - Train           | 0.76 | 6,189.78 |
| 1   | RidgeCV - Test            | 0.73 | 6,495.39 |
| 2   | LassoCV - Train           | 0.76 | 6,205.32 |
| 3   | LassoCV - Test            | 0.73 | 6,509.41 |
| 4   | Linear Regression - Train | 0.72 | 6,682.20 |
| 5   | Linear Regression - Test  | 0.69 | 7,011.94 |

**Actual vs Prediction:** to validate the performance of the models, the Actual vs Prediction scatter chart below were plotted.

![fig12](images/prediction.png)

The black line is the perfect prediction e.g. the predicted value equals the actual values. The train models can be considered making acceptable predictions because these predictions converged along the black line with some exceptions.

**The Ridge model was selected with 73% R2 score and 6,495 RMSE score.** This reveals that 73% of the variability observed in the "price" variable can be explained or predicted by the selected model. In addtion, the RMSE suggests that on everage or the typical difference between the predictions and the actual prices is about $6.5K - with larger errors being weighted more heavily.

### 6. Evaluation

To identify which which features are most predictive, two techniques were used: Coefficients and Permutation feature importance.

#### 6.1 Coefficients

The coefficients of the selected model were plotted below. The plot shows dependencies between a feature and the target (“price”). They are **“model”, “odometer” and “year”** features. 

![fig13](images/coefs.png)

The results can be interpreted as the follows: An increase of the “model” or “year” will induce an increase of the “price” **when all other features remain constant**. On the other hand, a decrease of the "odometer"" will induce an increase of the “price” **when all other features remain constant**.

#### 6.2 Permutation feature importance

Permutation feature importance model inspection technique randomly shuffles a single feature value and consequently breaks the relationship between the feature and the target - price. The decrease in the model score indicates the level of dependency of the model to the feature and, therefore, how important the feature is to the model. Below are the results of permutation importance computed on both the train set and the test set for the selected model. The top three features are **"model", "odometer" and “year”**. The results are consistant with that of the coefficient method.

![fig14](images/permutation.png)

Features that are important on the train set but not on the test set might cause the model to overfit. The similarity of the feature importance ranking between those two plots, train and test, suggests that the selected model is not overfitting.

In additiion, when data contains strong multicollinear features, the results of permutation importance might be misleading in some cases. When a feature is permuted, the model can get the same information from the other correlated feature. Therefore, performance of the model does not effect. This is not the case for the selected model because the permutation importance plot shows that permuting a feature drops the accuracy by 0.5. In addition, based is on the correlation matrix above, features which are strong collinear are not present.

## 9. Conclusion and Recommendations

To understand what drives the price of used cars, a used car dataset was explored and transformed so that three multivariate linear regression models named Ridge, Lasso, Linear Regression, were built and validated. The model with the highest quality was inspected to identify the top three features which influence the model the most. They are **“model”, “odometer” and “year”**.

It is important to mention that the identification of feature importance is based on coefficients. Coefficients in multivariate regression models represent the dependency between a given independent feature (like “odometer”) and the target or dependent variable (“price”), conditional on the other features. They reflect the average change in the target (“price”) for one unit of change in the independent feature (“odometer”) while other features are held constant in the model. Keeping other features static is a statistical condition and not a realistic condition in the real world. 

In addition, the relationships between the features and the target do not imply causal relationships. There are potentially unobserved confounding variables that could be correlated with both the target variable, “price”, and other identified features: “model” or “odometer” or “year”. One example could be the production capacity of new cars. The positive “year” and “price” relationship suggests that the more recent a car is, the better it has in price. However, our dataset includes used cars manufactured up to 2022. It was generally acknowledged that used cars prices went up during the Covid and global chip shortage period of 2020–2023.

George Box wrote “All models are wrong, some are useful”. While **the relationship between the identified features and the target feature ("price") should be interpreted with caution**, the project findings still pinpoint to what consumers generally value in a used car:  **the model of the car, a car with fewer miles and a more recent car.**

**Next steps:** 

The project can be continued with further optimizing the performance of the models. Some examples are: using Lasso as the feature selector and/or applying additional and different algorithms. In addition, special or unique features to distinguish antique cars (which yield a high value) were not observed in this dataset and can be considered to be added. 

## 10. Jupyter Notebook

Please refer to the [Used Car Jupiter Notebook](UsedCarPricePrediction.ipynb) for more information.
