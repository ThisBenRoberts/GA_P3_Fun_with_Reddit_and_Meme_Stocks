# **Subreddit Classification Predictions With Web APIs and NLP**

## Problem Statement
Given only the titles of posts from two subreddits, r/Stocks and r/Options, can we train a classifier on which subreddit a given post comes from using NLP and classification techniques?

## Executive Summary
The goal of this project was to use an API (https://github.com/pushshift/api) to extract post data from two subreddits.  Then, using limited post information, NLP and classification techniques, attempt to accurately determine which subreddit a post is from.

Our chosen subreddits were:

* r/Stocks
* r/Options

Created in June 2008, r/Stocks has 3.7M members and welcomes any post related to stocks.  Moderators ask that users disclose if they are holding any positions related to the content that they're posting, specifically ban advertisments, self-promotion,  discussion of crypto currency, and discussion of penny stocks or OTC (Over The Counter) stocks.

r/Options was created in October of 2009 and has 837k members. Unlike r/Stocks, oderators point members who are new to options trading to other forums and automatically filter new posts that appear to be questions typical of new traders. They ask that posts regarding strategies or positions be comprehensive and detailed. Posts linking to other content or platforms are also banned.
 
## Project Breakdown

The code for each of the following sections can be found in the included numbered Jupyter Notebooks.

[01: Data Collection](./code/01_Collect_Data.ipynb)

The data used in this project comes from Reddit via the pushshift API (https://github.com/pushshift/api). We iterated through each subreddit to collect the 2000 most recent posts. The data was returned to us as a .json dictionary.  We converted the .json data to a Pandas DataFrame, and then saved the raw data to a csv file with a date and time stamp. 

[02: EDA and Cleaning](./code/02_EDA_cleaning.ipynb)

Data Cleaning began with the removal of an unnamed index column and removing null values.  We then reformatted the title, selftext, and target data followed by the creation of columns for title and selfteext length and word count.   

Initial EDA included examining the title and selftext for length distribution by word and character count in each subreddit.  The number of comments per post was also looked at. Next we looked at the number of posts over time, as well as the number of unique authors by subreddit to determine if there was any overlap.  

Pre-processing included tokenizing and removing contractions from titles and selftext, as well as stemming, lemmatizing, and vectorizing with Count Vectorizer. 

We then looked at the most common words used in each subreddit title and selftext, considering both single words and bigrams, and ran a Sentiment Intensity Analysis to determine the positive, negative, and neutral scores for each title and selftext in each subreddit. 

[03: Modeling](./code/03_Modeling.ipynb)

First, we setup the date by dividiung into a Train and Test vector. Then, using Pipeline and GridSearchCV, we initially fit and scored 3 models:

Logistic Regression
Random Forest
K-Nearest Neighbors

Using the cross val score, best model was determined to be Random Forest with a score of 83.69%.  Hyperparameters were then tuned in an attempt to optimize our final model though we were unable achieve a score significantly different than the original test.

## Data Dictionary
|Feature|Type|Dataset|Description|
|---|---|---|---|
lemma_title|object|reddit_content_20220227-105531.csv|String of lemmatized title tokens|
subreddit|int|reddit_content_20220227-105531.csv|Binary integer: 0 for r/Options, 1 for r/Stocks|


## Recommendations & Conclusions
**Recommendations to Improve Our Model**

The first recommendation to improve the model would be to gather data from discrete periods of time, rather by the number of posts. As time goes on, the social, political, and economic conditions that affect the market and topics being discussed on social media will change. The topics that affect stocks and options will overlap but not always as the prices are controlled by different factors. And since the number of posts per day is drastically different for r/Stocks vs r/Options, if we pulled data from the last week, rather than the 500 newest posts, we could probably do a better job of predicting which subreddit the post came from. This article from Merril does a pretty good job explaining how options are priced https://www.merrilledge.com/investment-products/options/options-pricing-valuation

For all of the reasons mentioned above, I would also try to include the actual date of the post as a feature. Similarly, I would like to examine the text and dates of the comments as features in the model. Almost certainly some particular equities are more popular and being discessed with greater frequency at any given point in time. Given that the titles are short, there may be more clues in the comments as to which subreddit a particular post came from.

Given more time, a few other things I would like to try include identifying the ticker symbols being discussed using RegEx, more detailed analysis on sentiment and stop words, and identifying correlations between authors, commentors, and the subreddit of origin.

Lastly, I would use GridSearchCV, rather than RandomSearchCV, to find the best combination of hyperparameters. Originally, GridSearchCV was attempting more than 59,000 different combinations. For the sake of time, this plan was abandoned.

**Conclusions**

While there are a number of posts in foreign languages, many with no selftext, and a few different tactics we would like to employ in order to improve accuracy, the model is still about 82% accurate in predicting the origin subreddit based on the lemmatized title of the post. This represents an improvement above the baseline of 32%.