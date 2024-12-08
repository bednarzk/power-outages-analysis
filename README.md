# power-outages-analysis
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
- OUTAGE.DURATION, the duration of the outage event (in mintues)
- CLIMATE.CATEGORY, the climate episodes corresponding to the years ('Warm', 'Cold', or 'Normal' episodes)
- YEAR, the year when the outage event occurred
- NERC.REGION, the North American Electric Reliability Corporation (NERC) regions involved in the outage event
- CLIMATE.REGION, the U.S. Climate region as specified by the National Centers for Environmental Information (nine climatically consistent regions in the continental U.S.)

# Data Cleaning and Exploratory Data Analysis

## Data Cleaning
OUTAGE.START.DATE and OUTAGE.START.TIME:

The original data contained two separate columns for the start date and time of the outage. I consolidated these columns into one column OUTAGE.START, which is a datetime variable. It will be easier to work with this variable as a datetime object later in exploration and prediction. Then, I dropped the old variables OUTAGE.START.DATE and OUTAGE.START.TIME, which now is entirely contained in OUTAGE.START and were repetitive otherwise.

OUTAGE.RESTORATION.DATE and OUTAGE.RESTORATION.TIME:

Just like for the start date and time of the outage, the original data had two columns for the outage restoration date and time. Similarly, I consolidated these columns into one column OUTAGE.RESTORATION. I also dropped the old variables OUTAGE.RESTORATION.DATE and OUTAGE.RESTORATION.TIME.

YEAR:

Since our data is only from the years 2000 to 2016, I added a column YEARS AFTER 2000. Subtracting 2000 from the YEAR column may make feature engineering easier later on if I want to create polynomial or exponential features based on the year.

CAUSE.CATEGORY.DETAIL:

Many of the detailed categories are repetitive that are being counted separately due to manual errors. I wanted to combine as many of the same categories as possible. I stripped all the categories to remove whitespace causing some of them to be counted separately (ex. 'Coal' and ' Coal'). I bucketed 'thunderstorm; islanding' into 'thunderstorm', 'snow/ice storm' and 'hailstorm' into 'winter storm', 'wind' and 'wind storm' into 'heavy wind', 'wind/rain' into 'storm', and 'snow/ice' into 'winter'.

POP_URBAN and POP_UC:

The original data includes values for the state's population and also percentages of urban population in that state, but not the total number of people in urban areas. I created a column POP_URBAN, the total number of people in urban areas in the state, by multiplying the POPULATION and POPPCT_URBAN (% of urban population in the state). Similarly, there is a variable for the percentage of people living in urban clusters per state, but not the total number of people living in urban clusters. I created variable POP_UC, the total number of people living in urban clusters in the state, by multiplying the POPULATION and POPPCT_UC variables.

Here are the first 10 rows and first 11 columns.

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

<iframe
  src="assets/fig1.html"
  width="1100"
  height="600"
  frameborder="0"
></iframe>

This is the distribution of the primary variable that I'm interested in, CUSTOMERS.AFFECTED. Most of the data is clustered close to the left side of the box plot, indicating that the vast majority of outages affect 150,000 or less customers. The median number of custoemrs affected is around 70,000. However, there are still some outages that affected more than 500,000 people, with a maximum of over 3 million customers affected, however this does not appear to be the norm.

<iframe
  src="assets/fig2.html"
  width="1100"
  height="600"
  frameborder="0"
></iframe>

This choropleth shows the geographic distribution of the total number of outages recorded from 2000 to 2016. It appears that California and Texas have the highest total number of outages, with 210 and 127 outages, respectively. There also seems to be a higher number of total outages in Washington and the Midwest. It appears that there may be geographic factors affecting the *number* of outages, but it is still unclear whether the number of outages is indicative of the *size* of the outages based on number of customers affected.

## Bivariate Analysis

<iframe
  src="assets/fig3.html"
  width="1100"
  height="600"
  frameborder="0"
></iframe>

Here we are analyzing the number of customers affected by power outages based on the year. It appears that most of the largest magnitude outages (over 1M customers affected) occurred prior to 2011. It appears that the variability in the number of customers affected may have decreased over time, leading to fewer large-scale outage events. The most recent year we have data for, 2016, only had a maximum of 415,000 customers affected by a single outage, compared to millions of customers affected in previous years. It may be the case that future outages, on average, are less severe than past outages if this trend continues.

<iframe
  src="assets/fig4.html"
  width="1100"
  height="600"
  frameborder="0"
></iframe>

Again, we are looking at a geographic distribution of outages, but now, we are looking at the average number of customers affected per state rather than just the number of outages. While California and Texas are still two of the highest severity states on average, Florida has the highest average customers affected. South Carolina and Illinois now stand out a bit more as having large-severity outages, showing a trend of higher severity outages in the Midwest and Eastern states. There may be a reason that these states in partcular tend to have widespread outages, but this would required further research.


# Framing a Prediction Problem


# Baseline Model


# Final Model