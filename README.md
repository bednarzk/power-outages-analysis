# Can widespread data predict widespread outages?
An analysis of power outages data in the continental US from January 2000 to July 2016. Completed as a final project for EECS 398: Practical Data Science.

# Introduction
This dataset contains information about power outages in the continental United States from January 2000 to July 2016. When an outage occurs, one of the first things that an energy company or local government agency will want to know is the extent of the outage. Is this a minor incident that will only affect tens of customers, or is this a massive outage that could affect millions of Americans? The energy company needs to know the extent of the issue in order to understand the extent of the outage's impact and to prioritize recovery efforts in the event of multiple simultaneous outages. 

I will be exploring the following question: What are the characteristics of major power outages with higher severity? What risk factors may an energy company want to look into when predicting the location and severity of its next major power outage?

Specifically, I will be determining the severity of an outage based on the number of customers it affects. The more widespead the outage is, the more urgently it needs to be addressed by the power company. If energy companies can accurately predict the number of customers affected by an outage, they can more quickly determine the extent of an outage, and customers will have higher satisfaction through the company helping the greatest number of people.

This dataset has 1,534 rows and 56 columns. The primary variable I am interested in is CUSTOMERS.AFFECTED, the number of customers affected by the power outage event.

Some other variables I am especially interested in to see their possible connection with CUSTOMERS.AFFECTED are:
- CAUSE.CATEGORY, categories of all the events causing the major power outages
- CAUSE.CATEGORY.DETAIL, a detailed description of the event categories causing the major power outages
- POSTAL.CODE, the postal code of the U.S. state where the outage occurred
- OUTAGE.DURATION, the duration of the outage event (in minutes)
- CLIMATE.CATEGORY, the climate episodes corresponding to the years ('Warm', 'Cold', or 'Normal' episodes)
- YEAR, the year when the outage event occurred
- NERC.REGION, the North American Electric Reliability Corporation (NERC) regions involved in the outage event
- CLIMATE.REGION, the U.S. Climate region as specified by the National Centers for Environmental Information (nine climatically consistent regions in the continental U.S.)

# Data Cleaning and Exploratory Data Analysis

## Data Cleaning
OUTAGE.START.DATE and OUTAGE.START.TIME:

The original data contained two separate columns for the start date and time of the outage. I consolidated these columns into one column OUTAGE.START, which is a datetime variable. It will be easier to work with this variable as a datetime object later in exploration and prediction. Then, I dropped the old variables OUTAGE.START.DATE and OUTAGE.START.TIME, which now are entirely contained in OUTAGE.START and were repetitive otherwise.

OUTAGE.RESTORATION.DATE and OUTAGE.RESTORATION.TIME:

Just like for the start date and time of the outage, the original data had two columns for the outage restoration date and time. Similarly, I consolidated these columns into one column OUTAGE.RESTORATION. I also dropped the old variables OUTAGE.RESTORATION.DATE and OUTAGE.RESTORATION.TIME.

YEAR:

Since our data is only from the years 2000 to 2016, I added a column YEARS AFTER 2000. Subtracting 2000 from the YEAR column may make feature engineering easier later on if I want to create polynomial or exponential features based on the year.

CAUSE.CATEGORY.DETAIL:

Many of the detailed categories are repetitive and are being counted separately due to manual entry errors. I wanted to combine as many of the same categories as possible. I stripped all the categories to remove whitespace causing some of them to be counted separately (ex. 'Coal' and ' Coal'). I bucketed 'thunderstorm; islanding' into 'thunderstorm', 'snow/ice storm' and 'hailstorm' into 'winter storm', 'wind' and 'wind storm' into 'heavy wind', 'wind/rain' into 'storm', and 'snow/ice' into 'winter'.

POP_URBAN and POP_UC:

The original data includes values for the state's population and also percentages of urban population in that state, but not the total number of people in urban areas. I created a column POP_URBAN, the total number of people in urban areas in the state, by multiplying the POPULATION and POPPCT_URBAN (% of urban population in the state). Similarly, there is a variable for the percentage of people living in urban clusters per state, but not the total number of people living in urban clusters. I created variable POP_UC, the total number of people living in urban clusters in the state, by multiplying the POPULATION and POPPCT_UC variables.

Here are the first 10 rows and first 11 columns of the cleaned data.

|   OBS |   YEAR |   MONTH | U.S._STATE   | POSTAL.CODE   | NERC.REGION   | CLIMATE.REGION     |   ANOMALY.LEVEL | CLIMATE.CATEGORY   | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL   |
|------:|-------:|--------:|:-------------|:--------------|:--------------|:-------------------|----------------:|:-------------------|:-------------------|:------------------------|
|     1 |   2011 |       7 | Minnesota    | MN            | MRO           | East North Central |            -0.3 | normal             | severe weather     | nan                     |
|     2 |   2014 |       5 | Minnesota    | MN            | MRO           | East North Central |            -0.1 | normal             | intentional attack | vandalism               |
|     3 |   2010 |      10 | Minnesota    | MN            | MRO           | East North Central |            -1.5 | cold               | severe weather     | heavy wind              |
|     4 |   2012 |       6 | Minnesota    | MN            | MRO           | East North Central |            -0.1 | normal             | severe weather     | thunderstorm            |
|     5 |   2015 |       7 | Minnesota    | MN            | MRO           | East North Central |             1.2 | warm               | severe weather     | nan                     |
|     6 |   2010 |      11 | Minnesota    | MN            | MRO           | East North Central |            -1.4 | cold               | severe weather     | winter storm            |
|     7 |   2010 |       7 | Minnesota    | MN            | MRO           | East North Central |            -0.9 | cold               | severe weather     | tornadoes               |
|     8 |   2005 |       6 | Minnesota    | MN            | MRO           | East North Central |             0.2 | normal             | severe weather     | thunderstorm            |
|     9 |   2015 |       3 | Minnesota    | MN            | MRO           | East North Central |             0.6 | warm               | intentional attack | sabotage                |
|    10 |   2013 |       6 | Minnesota    | MN            | MRO           | East North Central |            -0.2 | normal             | severe weather     | winter storm            |

## Univariate Analysis

This is the distribution of the primary variable that I'm interested in, CUSTOMERS.AFFECTED.

<iframe
  src="assets/fig1.html"
  width="1100"
  height="600"
  frameborder="0"
></iframe>

 Most of the data is clustered close to the left side of the box plot, indicating that the vast majority of outages affect 150,000 or less customers. The median number of customers affected is around 70,000. There are still some outages that affected more than 500,000 people, with a maximum of over 3 million customers affected, however this does not appear to be the norm.

This choropleth shows the geographic distribution of the total number of outages recorded from 2000 to 2016.

<iframe
  src="assets/fig2.html"
  width="1100"
  height="600"
  frameborder="0"
></iframe>

It appears that California and Texas have the highest total number of outages, with 210 and 127 outages, respectively. There also seems to be a higher number of total outages in Washington and the Midwest. It appears that there may be geographic factors affecting the *number* of outages, but it is still unclear whether the number of outages is indicative of the *size* of the outages based on number of customers affected.

## Bivariate Analysis

Here we are analyzing the number of customers affected by power outages based on the year.

<iframe
  src="assets/fig3.html"
  width="1100"
  height="600"
  frameborder="0"
></iframe>

It appears that most of the largest magnitude outages (over 1M customers affected) occurred prior to 2011. It appears that the variability in the number of customers affected may have decreased over time, leading to fewer large-scale outage events. The most recent year we have data for, 2016, only had a maximum of 415,000 customers affected by a single outage, compared to millions of customers affected in previous years. It may be the case that future outages, on average, are less severe than past outages if this trend continues.

Again, we are looking at a geographic distribution of outages, but now, we are looking at the average number of customers affected per state rather than just the number of outages.

<iframe
  src="assets/fig4.html"
  width="1100"
  height="600"
  frameborder="0"
></iframe>

 While California and Texas are still two of the highest severity states on average, Florida has the highest average customers affected. South Carolina and Illinois now stand out a bit more as having large-severity outages, showing a trend of higher severity outages in the Midwest and Eastern states. There may be a reason that these states in particular tend to have widespread outages, but this would require further research.

## Interesting Aggregates

Now, let's take a look at the average number of customers affected based on NERC.REGION.

| NERC.REGION   |   CUSTOMERS.AFFECTED |
|:--------------|---------------------:|
| HI            |               294000 |
| FRCC          |               289778 |
| ECAR          |               256354 |
| TRE           |               226469 |
| SPP           |               188513 |
| WECC          |               133833 |
| RFC           |               127894 |
| HECO          |               126729 |
| NPCC          |               108726 |
| SERC          |               107854 |
| MRO           |                88985 |
| PR            |                62000 |
| ASCC          |                14273 |
| FRCC, SERC    |                  nan |

It appears that there is some variation in the average number of customers affected per outage based on the NERC.REGION. Region HI has the highest average, although this region has had less than 5 total outages, so this may not be a large enough sample size to fairly compare to other regions. Regions FRCC, ECAR, and TRE also had an average of over 200,000 customers affected. Regions MRO, PR, and ASCC are among the smallest average number of customers affected. Interestingly, region FRCC, SERC has no data on the number of customers affected.

## Imputation

CUSTOMERS.AFFECTED has 443 rows with null values. Since this is the variable that we will eventually want to predict, I will perform listwise deletion since we will not be able to use these observations to train our models or to determine the accuracy of our model.

OUTAGE.START has 7 null values. These rows are also missing a value for MONTH, so we do not have any information about when the outage was except for the year. I will perform listwise deletion because I will not lose too much data.

RES.PRICE and POPDEN_UC only have 9 observations missing, so again I will perform listwise deletion since we are not losing too much information. All the missing values of POPDEN_UC are from Washington, D.C., so we should recognize that our model should not be used to predict outages in D.C. since we've removed all its data.

We would not know OUTAGE.RESTORATION, OUTAGE.DURATION, or DEMAND.LOSS.MW at the time of prediction, so we can leave all their null values.

<iframe
  src="assets/fig5.html"
  width="1100"
  height="600"
  frameborder="0"
></iframe>

CLIMATE.REGION has 5 missing values, all of which are from Hawaii. None of the existing climate region categories make sense for Hawaii, so I created a new category called "Hawaii."

<iframe
  src="assets/fig6.html"
  width="1100"
  height="600"
  frameborder="0"
></iframe>

The 'Hawaii' category is now included as the climate region with the least outages.

HURRICANE.NAMES does not have any predictive power so we can leave the null values since we will not use this column in our predictive model.

<iframe
  src="assets/fig7.html"
  width="1100"
  height="600"
  frameborder="0"
></iframe>

Many CAUSE.CATEGORY.DETAIL values are missing, which is expected since this column just has more detailed information for some observations. I will bucket the null values into a 'No Information' category so that we can see if the fact that they have no information has any predictive power. 

<iframe
  src="assets/fig8.html"
  width="1100"
  height="600"
  frameborder="0"
></iframe>

The "No Information" category is now included as the category with the most outages.

Any columns where listwise deletion occurred will look identical.


# Framing a Prediction Problem

The prediction problem that I will be analyzing is: Predict the severity (in terms of number of customers) of a major power outage. This is a regression problem, since the CUSTOMERS.AFFECTED variable is a continuous numerical variable. The response variable is CUSTOMERS.AFFECTED since I believe that this would be a good indicator for how urgent a power outage needs to be fixed. Even if an outage has a long duration, if it's in a location with few to no customers, it shouldn't necessarily be the most urgent thing to resolve. By predicting the number of customers affected by an outage, energy companies can send their resources to fix those issues faster, and therefore help as many people as possible more quickly. I will be evaluating my model based on the lowest mean squared error so that predictions further from the actual value are penalized more harshly than in mean absolute error, for example.

For this prediction problem, I am assuming that an energy company has just found out about an outage and its cause, but do not yet have an esimate about how many customers this outage has affected. In this case, the energy company would not know the time of restoration (OUTAGE.RESTORATION), the length of the outage (OUTAGE.DURATION), or the amount of peak demand lost during the outage (DEMAND.LOSS.MW). Therefore, I will drop these columns before training my models. All other variables I would know at the time of prediction.


# Baseline Model

My baseline model performs a basic multiple linear regression using all the features of my data (after removing OBS, HURRICANE.NAMES, U.S._STATE, TOTAL.PRICE, TOTAL.SALES, TOTAL.CUSTOMERS, AND YEAR).

The following are features in my model:

'MONTH', 'POSTAL.CODE', 'NERC.REGION', 'CLIMATE.REGION',
       'ANOMALY.LEVEL', 'CLIMATE.CATEGORY', 'CAUSE.CATEGORY',
       'CAUSE.CATEGORY.DETAIL', 'RES.PRICE', 'COM.PRICE', 'IND.PRICE',
       'RES.SALES', 'COM.SALES', 'IND.SALES', 'RES.PERCEN', 'COM.PERCEN',
       'IND.PERCEN', 'RES.CUSTOMERS', 'COM.CUSTOMERS', 'IND.CUSTOMERS',
       'RES.CUST.PCT', 'COM.CUST.PCT', 'IND.CUST.PCT', 'PC.REALGSP.STATE',
       'PC.REALGSP.USA', 'PC.REALGSP.REL', 'PC.REALGSP.CHANGE', 'UTIL.REALGSP',
       'TOTAL.REALGSP', 'UTIL.CONTRI', 'PI.UTIL.OFUSA', 'POPULATION',
       'POPPCT_URBAN', 'POPPCT_UC', 'POPDEN_URBAN', 'POPDEN_UC',
       'POPDEN_RURAL', 'AREAPCT_URBAN', 'AREAPCT_UC', 'PCT_LAND',
       'PCT_WATER_TOT', 'PCT_WATER_INLAND', 'OUTAGE.START', 'YEARS AFTER 2000',
       'POP_URBAN', 'POP_UC'

- Ordinal: 2 (MONTH, CLIMATE.CATEGORY)
- Nominal: 5 (POSTAL.CODE, NERC.REGION, CLIMATE.REGION, CAUSE.CATEGORY, CAUSE.CATEGORY.DETAILED)
- Quantitative: 39 (all other variables)

I encoded all my categorical variables using a One Hot Encoder.

The mean squared error of this baseline model is 66,761,118,602. It is difficult to tell how well this model performed without comparing it to another model, so we can compare it to the constant model.

I also calculated the mean squared error of a model that just predicted the mean of the training CUSTOMERS.AFFECTED variable on the unseen data. This model had a mean squared error of 65,501,779,872, which is lower than the MSE for the baseline model. This indicates that our baseline model performs worse than the constant model. This means that the baseline model is not doing a good job, and that it may potentially be overfitting to the training data.


# Final Model

For my final model, I added 3 new features and used a StandardScaler on my numerical features. First of all, I created 3 new features from my OUTAGE.START variable. This variable is a datetime object that contains a lot of information that our baseline model may not be capturing. I created a variable for the day of the month (from 1 to 31) of the outage to analyze whether more outages might occur towards the beginning or end of the month. Since this creates a numeric feature, I used a StandardScaler. Next, I created a feature for the hour the outage occurred. I was interested in whether outages that occurred in the middle of the night, for example, would affect more people than an outage in the morning or afternoon. Therefore, I found the hour of the outage, then discretized it into 4 buckets, from midnight to 6am, 6am to 12pm, 12pm to 6pm, and 6pm to 12am; this is an ordinal feature that was one hot encoded. Finally, I created a feature for the day of the week to see if outages on certain days of the week affected more customers. Therefore, I found the day of the week with Monday = 0, Tuesday = 1, ..., Sunday = 6, then one hot encoded these values; this is also an ordinal variable. Additionally, I used a StandardScaler to standardize all numerical variables. This is because for my final model, I used Lasso Regression. In lasso regression, the magnitude of the coefficients affects how much the model is penalized, so it is important that all variables are on the same scale before we use lasso. 

For lasso regression, I used cross-validation to determine the best hyperparameter, alpha. Lasso regression performs variable selection by penalizing the magnitude of the coefficients on each of our features, therefore setting many of them to 0 if they do not have predictive power. The best alpha for lasso using cross-validation was 10,000. I chose the lasso model as my final model because it resulted in the lowest mean squared error on unseen test data; it had a MSE of 60,203,778,155, which was lower than the baseline model, the constant model, and the ridge model I also tested. This was an improvement of 6,557,340,447 over the baseline model!

Here is a summary of the mean squared errors of each model I created:

|                |         MSE |
|:---------------|------------:|
| base model     | 6.67611e+10 |
| constant model | 6.55018e+10 |
| ridge          | 6.35868e+10 |
| lasso          | 6.02038e+10 |