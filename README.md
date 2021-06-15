# Proj 4 Readme (West Nile Virus prediction)
This project was done by Allen, Jean and Mark.

## Background
The West Nile virus(WNV) is the leading cause of mosquito-borne disease in continental United States.  Cases of WNV occur during mosquito season, typically starting in the summer and continuing through fall.
Whilst most people display no symptoms, 1 in 5 people will develop a fever and may develop headaches, body aches and joint pains.
About 1 in 150 people infected will develop a severe illness affecting the central nervous system such as encephgalitis(inflammation of the brain) or meningitis.

Whilst there is no vaccine or specific antivral treatments for the West Nile Virus, over-the-counter pain relievers can be used to alleviate fever and provide some relief.  In severe cases however, patients often need to be hospitalized to receive support treatment.

The Centre for Disease Control and Prevention (CDC) has provided the team with Chicago data from 2007 to 2014, broadly grouped into 3 categories:
> 1. Spray data, recording dates and locations where fogging of mosquitos took place
> 2. Weather data, recording key weather statistics in the date range
> 3. Trap data, recording location of mosquito traps placed around the city, the number of mosquitos caught and whether these mosquitos carried the WNV.

## Problem statement
Our team will utilise the data to answer the following problem statements:
> 1.Combine the above datasets and predict the occurence of WNV by building a classification model using ROC_AUC as a score.
> 2. Identify the key features in the model that affect WNV and provide recommendations (including a cost-benefit analysis) on how to control the spread of the WNV.


## Data dictionary

Most used features
| Feature Name | Data Type | Description |
| ----| ---- | ---- |
| date | datetime64  | YYYY-MM-DD |
| nummosquitos | int | number of mosquitos caught in the trap |
| tavg | float | daily average temperature (F) |
| tmax | float | daily maximum temperature (F)|
| tmin | float | daily minimum temperature (F)|
| preciptotal | float | preciptation (inches) |
| avgspeed | int | average wind speed (mph) |
| resultspeed | int | resultant wind speed taking into account direction (mph) |
| resultdir | int | direction of wind (deg) |
| dewpoint | float | dewpoint temperature (F)|
| latitude | float | latitude from Geocoder|
| longitude | float | longitude from Geocoder|
| stnpressure | float | average daily station pressure|
| sealevel | float | average daily sealevel pressure|
| daylightmins | int | total number of minutes of daylight|
| year | int | year|
| month | int | month|
| day | int | day|
| wnvpresent | int | dummified value representing presence of WNV.  1 = Present, 0 = Not Present|
| species_X | int | dummified value representing presence of species_X in trap. 1 = Present, 0 = Not Present|

Additional features can be found in noaa_weather_qclcd_documentation.pdf.

## Methodology
The codebook is split into 3 main notebooks.

#### 01_Datacleaning

In this section we standardise the format of the datasets and 
1. Cleaning of spray dataset
> * Drop duplicates
> * Standardise columns with other datasets
2. Cleaning weather dataset
> * Datetime relevant columns
> * Drop nulls
> * Impute missing values for tavg, depart etc.
> * Split into weather data from station 1 and 2
> * Standardise columns with other datasets
3. Cleaning train and test datasets
> * Datetime relevant columns
> * Standardise columns with other datasets
> * Standardise columns with other datasets

#### 02_EDA
In this section we explore and draw some initial insights from the data.

1. EDA on Train
> * Find that the rate of WNV peaks in August
> * Find that certain species of mosquitos are more likely to carry the strain of WNV
2. EDA on Weather
> * Find that July-Aug are the hottest and wettest months in Chicago
> * Feature engineer minutes of daylight per day and find that June has the most amount of daylight
> * Visualisations of weather data with average number of mosquitos and find that nubmer of mosquitos is positively correlated with temperature and negatively correlateed with precipitation.
> * Visualisations of weather data with WNV data which suggest that WNV is inverseley proportional to precipitation and dewpoint.
> * OHE of species of mosquitos and merge weather data into train and test datasets
3. EDA on Spray
> * Visualise spray locations and locations where WNV was detected and find that accuracy of spraying locations can be improved especially in 2007.
4. Data Merging
> * Merge the weather data and the trap data on date.

#### 03_Modelling
In this section we try and obtain the best accuracy from Logreg, KNN and Random Forest classification models using Synthetic Minority Ovesrampling Technique (SMOTE) due to significantly imbalanced classes.
1. Gridsearch Logreg
2. Gridsearch KNN
3. Gridsearch RF
4. Model evaluation for Random Forest (best model)
5. Plot AUCROC for RF
6. Feature importances for RF

## Conclusions

Our final model had an AUCROC score of 0.88 on the test set, a 38% improvement from the data.  Locational features were picked out by the model as the most important, followed by the species of mosquito, dominating other weather features that might have predicted mosquito propagation such as temperature and precipitation.

However, when predicting WNV in 2008, 2010, 2012 and 2014, there was a significant difference in our accuracy on the test set between our submission score of 0.67 and the leading Kaggle scores of 0.85.  This suggests that our model did not generalise well.  Apart from collating more data, the low score might be due to the following factors:

1. Locations of WNV outbreak might have been different in the prediction years.
2. Highly imbalanced data set and possibility of spike during even years.
3. Use spray data in the model, joined on date of spray, to see if it would have further impact.


## Recommendations

To reduce the spread of WNV, it might be interesting to take a leaf out of other tropical countries such as Singapore, a small city-state with a population of about 5.7 million.  The hot and  humid temperatures means in Singapore mean it is mosquito season all year round.  However, Singapore's National environment Agency  has reduced the number of [dengue cases](https://www.straitstimes.com/singapore/singapores-weekly-dengue-cases-at-lowest-level-this-year-following-historic-outbreak#:~:text=SINGAPORE%20%2D%20Dengue%20cases%20have%20fallen,of%201%2C792%20cases%20in%20July.) through a mixture of education, fogging and bio-engineering.



__1. Spray the right areas__
As location was the most important factor, it is likely that spray coverage during the mosquito season is one of the key driving factors to curb the spread of WNV.  As could be seen from the EDA, the CDC of Chicagoonly started spraying in 2011, and not all the locations where the virus was present was covered.  

More significantly, locations which the model picked out, such as Chicago O'Hare Airport and North Oak Parks Avenue (central/east) of Chicago did not seem to be within the coverage of the spray.  These areas should be prioritised.

__1.1 Cost benefit analysis of spraying__

The cost of spraying the entire Chicago metropolitan area can be roughly estimated by the following formula:

> * `US$700` ([Cost per acre per season](https://www.homeadvisor.com/cost/environmental-safety/mosquito-control/)) * `149,894` ([land area of Chicago metropolitan area]) = US$ 104,925,800

The benefits from preventing WNV can be roughly estimated in two parts using the following formula:

> * Value of human lives saved = `1` ([Average number of deaths per year from 2007 to 2014](http://www.idph.state.il.us/envhealth/wnvnews.htm)) * `9,100,00` ([value of human life](https://www.nytimes.com/2011/02/17/business/economy/17regulation.html?_r=0&pagewanted=all)) = `US$9,100,000`
> * Value of lives improved (by not contracting the virus) = `56.25` ([Average number of people who contract WNV per year from 2007 to 2014](http://www.idph.state.il.us/envhealth/wnvnews.htm)) * `US$272,973` ([Value of human lives improved including cost of treatment, loss of income etc](https://www.npr.org/sections/health-shots/2014/02/11/275262857/the-high-cost-of-treating-people-hospitalized-with-west-nile-virus#:~:text=The%20team%20then%20used%20an,%24678%20million%20to%20%241.01%20billion.)) = `US$15,354,731.25`   
> * Total benefits = `US$9.1M` + `US$15.4M` = `US$24.5M`

From this data, it can be seen that the cost of spraying the entire Chicago metropolitan area exceed the benefits.  This means that spraying should only be used as a measure of last resort on specific clusters, or when the number of mosquitos caught in the trap are high.


__2. Active outreach and monitoring__

According to the [WHO](https://www.who.int/news-room/fact-sheets/detail/west-nile-virus#:~:text=West%20Nile%20virus%20can%20cause,disease%20and%20death%20in%20horses.), the most effective method to reduce the transmission rate of the WNV is through source control.  This may include weekly inspection and cleaning of possible breeding sites for mosquitos such as drains, ponds or other places with stagnant water and refuse.

For example, the scope of work for mosquito control as espoused by the National Environmental Agency(NEA) in Singapore [include the following](https://www.nea.gov.sg/our-services/pest-control/mosquito-control/mosquito-control-in-condominium-estates):

> * Weekly measures to check for mosquito breeding sites in drains, puddles, tree holes, outdoor bins, rooftops and any other containers.
> * Fortnightly measures checking roof drainage and gutters in all structures and application of sand granular insecticide in gully traps, manholes and valve chambers

In addition, public health educational messages should focus on the protection of individuals or communities against mosquito bites such as through the use of mosquito nets, insect repellants and by avoiding outdoor activity at peak breeding times.


__3. Bioengineering__

Should WNV concerns remain or if fogging/use of insecticides might not be welcome by the Chicago public, it may be interesting to explore genetic modification or biological engineering to further limit the population of the 2 WNV carrying mosquito species, _C. Pipiens_ and _C. Restauns_.

> * Reduce vectors of transmissions and reservoir hosts such as birds, which pass on the WNV virus to mosquitos after feeding.
> * Implement Sterile Insect Techniques (SIT) where large numbers of sterile wild male mosquitoes are released in the wild.  However, over time the numbers of the targeted mosquito species will return to normal.
> * Male mosquitoes do not bite peopole or animals and mainly feed on nectar.  Therefore, people living in the release area will not be bitten more than normal.






