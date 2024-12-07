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


# Framing a Prediction Problem


# Baseline Model


# Final Model