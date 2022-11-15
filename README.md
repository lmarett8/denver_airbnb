# ![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png) Capstone Project: Denver Housing and Airbnb Data Exploration

### Included in this module:

- archive directory
    - Old notebook showing initial attempt at using the Zillow API
- data directory
    - Airbnb tables pulled from [Inside Airbnb](http://insideairbnb.com/get-the-data)
    - Zillow tables pulled from [Nasdaq Data Link](https://data.nasdaq.com/tables/ZILLOW/ZILLOW-REGIONS/export)
    - Created tables used across notebooks
- notebooks directory
    - 01_airbnb_cleaning -- notebook used to create a table of the number of Airbnb listings in Denver
    - 02_zillow_eda_modeling -- notebook used to explore the Zillow housing data, create models predicting future Denver housing prices, and evaluate those models
    - 03_airbnb_visualizations -- notebook used to visualize the Airbnb listings in Denver
- slides
    - Slides used for project presentation

---

## Problem Statement

The city of Denver, Colorado is the ideal destination for travelers seeking their next adventure with its prime location just east of the Rocky Mountains. What was once a somewhat small town has seen a population boom in recent years to [nearly 3 million residents in 2022](https://www.macrotrends.net/cities/22972/denver/population). Despite ample space in every direction except west, Denver consistently fails to provide affordable housing for its residents as they are [more than 50,000 units short of what's needed](https://denverite.com/2022/03/17/a-developer-on-denvers-housing-crisis-there-are-never-going-to-be-enough-resources-to-solve-this-in-the-ways-that-weve-been-trying-to-solve-it/).

To make matters worse, Airbnb has exploded onto the housing market given the city's propensity for mountaineering tourists. Since their first listing appeared in 2009, Airbnb now has nearly 6,000 active listings in the city of Denver. For this project I have been contracted by the Denver Housing Authority to forecast housing and rental prices for the next few years, and to determine if Airbnb is having too great an effect on an already limited supply of housing. If so, the city of Denver may need to look into strong regulations minimizing the continued growth that Airbnb has shown throughout its time here.

---

## Data Collection

I initially tried to use the API for [Nasdaq Data Link](https://data.nasdaq.com/) to pull the Zillow data I wanted to use for this project. This proved to be too challenging a task given the time constraints and my limitations as a programmer at this point, so I pivoted and downloaded the [data](https://data.nasdaq.com/tables/ZILLOW/ZILLOW-REGIONS/export) from the website instead. Perhaps when I revisit this project at a later date when I am more savvy, I can attempt it again then. This file is much too large to store on Github, so if needed please visit the website for the data given above.

The Airbnb data was pulled from the website [Inside Airbnb](http://insideairbnb.com/get-the-data), which has extensive Airbnb data for every city in which the company operates. Explore the Denver data further [here](http://insideairbnb.com/denver/).

---

## Data Cleaning & EDA

In order to compare apples to apples, I needed to convert the Airbnb data into a format compatible with the monthly data given for the Zillow prices. I essentially created a pivot table of the Airbnb data based on the first review for each listing, combining all of the listings posted in each month and counting the total for each. Using a cumulative sum I then determined how many total listings Airbnb had in Denver at any given time. I then merged this dataset into the Zillow data by month.

The Zillow data is extensive, most of which I did not need. After briefly exploring I decided I wanted to look at three things for the Denver housing market:

1) Home values
2) Median sales price
3) Rental prices

I also used the Airbnb data separately to contextualize some of the information, looking at how the listings are spread across the city of Denver. There are a few charts highlighting the most popular neighborhoods, as well as several maps plotting the locations of the various listings.

---

## Preprocessing & Modeling

Given that the goal here was to forecast data into the future, I used an ARIMA model to analyze and predict for each of the features (home values, median sales price, and rental prices) separately. I first determined the correct d-value for each using the Augmented Dickey-Fuller (AD Fuller) test, then tested out several p and q values looking for the best Akaike Information Criteria (AIC) score. This exercise yielded the following ideal models:

1) Home values (p, d, q) -- (1, 1, 1)
2) Median sales price (p, d, q) -- (1, 3, 1)
3) Rental prices (p, d, q) -- (4, 2, 2)

I then fit the ARIMA models for each and made predictions for the values.

After creating models for each feature separately, I then combined them all and incorporated the Airbnb data to create a Vector Autoregressive (VAR) model to see if it performed better than the individual models. I ran the same AD Fuller test for the Airbnb data to determine the best d-value for it (2) before engineering the features based on d-values that I would use to create the model.

---

## Evaluation

Overall the VAR model performed quite poorly with R2 scores in the negatives and mean squared error values greater than the mean value itself for each feature. Part of that is likely due to the fact there were limited data points feeding into the model - the points themselves were created with lots of data behind that all boiled down into monthly values, but the fact that there were limited points in the model led to significant error. In order to generate a more sound model I would want to pull in the individual data points that make up the monthly totals that were used in the model. This should lead to a more assured result from our predictions.

It was also a goal of our project to look at the correlation between Denver housing prices and the expansion of Airbnb listings throughout the city. The correlation between the Airbnb data and the other features of the model was significantly lower than expected (in the negatives for each feature). This is also likely due to the fact that there were limited data points feeding into the model from which to determine strong correlation.

---

## Conclusion and Recommendations

Our goal was to explore the Denver housing market and forecast what future home and rental prices would look like. We also wanted to incorporate Airbnb data to determine if part of the drastic increase in prices seen in recent years was in part due to the profileration of the Airbnb market. Ultimately we were not able to reach such a conclusion given how our model performed.

The primary issue that led to low scores and less than stellar results here was that limited data points fed into the model given that the data was already summarized before being incorporated. In order to yield a more robust result from our predictions, I would want to use the source data Zillow used - the predictions would likely be similar given that the data points themselves are rich as is, but there would be far less error with more data. Perhaps features could be engineered to artificially create more data as well.

Next steps other than adding more data would be to

1) approach the question of whether the increase in Airbnb listings *causes* the rapid increase in home and rental prices through a more statistical lens; I don't have a statistical background, but with more time I would start here.
2) explore other time series models; at this stage in my data science journey I have limited knowledge of time series models, but am eager to learn others and see if they could yield a better result.