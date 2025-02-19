# Using NLP to Identify Positive and Negative Comments for Women's Fashion Brands
Author: Jillian Clark

![](./images/header.png)

## Overview
In this project, I am a Data Scientist contractor working with different Women's Fashion Brands, such as Zara and H&M, to classify comments as negative or positive. This project aims to create a generalizable classification model that can be fed comments from any platform, such as Twitter, Instagram, or blogs. For demonstration purposes, I will utilize my model to classify tweets as positive or negative so the customer service teams can respond appropriately, such as responding to customer concerns in negative tweets and retweeting positive tweets. 

## Business Problem
Natural language processing (NLP) has proved to be a highly effective technology for companies to save time and money while optimizing business processes. One of the major benefits of natural language processing for businesses is the ability to process massive volumes of text across various outlets, such as social media platforms, blogs, comments, and so on. By utilizing a machine learning model to classify text as positive or negative, businesses can send the comments to the appropriate teams, such as Quality Assurance for negative comments or Marketing for positive comments. Social media is the second most preferred channel (after an in-person interaction) for customers to initiate communication; for millennials, social media is the most preferred channel. It is important for businesses to respond to these comments in order to protect the brand's reputation, show customers your care, reduce churn, and improve visibility to potential new customer bases and improve customer loyalty for current customers. Generalizability is important with the constant evolution of technology, social media, and how users choose to interact with products and brands. While one brand may receive lots of tweets, another brand may be discussed more in the comments of their Instagram posts.

### Use Cases
Positive Comments
- Retweet or repost from the Brand account
- Respond to boost that comment and reach more viewers
- Identify positive trends, such as colors or fit types, that multiple users are responding to
- Improve brand loyalty 

Negative Comments
- Respond to help customers feel heard and solve any fixable issues
- Identify issues with items before next drops, such as colors not photographing accurately on website or quality issues with fabric
- Improve brand loyalty 

### Why utilize data from Reviews?
Reviews left by customers contain both text reviews (the meat of the review) in conjunction with a rating. By utilizing reviews as my source, I am able to easily assign a label of Positive_Rating (0 being false, 1 being true) to the review text based on the rating. This helps my model learn what words are associated with a positive sentiment and what words may be more associated with a negative sentiment. I can then apply this model to texts that are not accompanied by a number rating, such as tweets, Instagram captions, blog posts, and more.

## Data Understanding
Data for this project was pulled from a compiled dataset of Women's E-Commerce Clothing Reviews compiled in one CSV file. The dataset can be found [here](https://www.kaggle.com/datasets/nicapotato/womens-ecommerce-clothing-reviews). 

The review data contained 23,486 reviews. Additional variables included Clothing ID, Age, Title (of the review if there was one), Review Text, Rating, Recommended IND (whether or not the customer recommends a product), Positive Feedback Count (number of other customers who found the review positive), Division Name (categorical name of product high-level division), Department Name, and Class Name. Reviews ranged on a scale of 1-5. A majority of reviews received an overall rating of 5, which could be a limitation to the model. 

![Review Distributions](./images/reviews_distribution.png)

A majority of reviews also recommended the product.

![Recommended IND Distributions](./images/recommendedIND_distribution.png)

Upon analysis, I also noticed some users either had a positive rating (4 or 5) but did NOT recommend the product, or recommended the product but did not have a positive rating (1-3). This could be due to user error and incorrectly using the rating system, or some users who had a personal negative review but would still recommend the product for others. If I had more time, I may run my model utilizing Recommended_IND as my target and see how my results change. 

Below are some examples of reviews where Rating and Recommended_IND did not match up.

![](./images/positive_not_recommended.png)

![](./images/positive_not_recommended2.png)

![](./images/negative_recommended.png)

![](./images/negative_recommended2.png)

It looks as though users who rated an item positively but did not recommend the product had an overall positive experience, while users who rated an item negatively but recommended the product had a positive experience, but incorrectly rated the item. This could potentially affect the words associated with negative ratings for my model.

## Data Preparation
The initial data source did not require too much cleaning. I removed 845 entries, which made up approximately 3.6% of m data, because they did not include any review text. I then went on to preprocess the review text so it could be used in a model. My preprocessing steps included: tokenizing my text (breaking the reviews into separate words), removing stop words (common words in the English language that appear often and do not carry much weight when deciding sentiment), lower casing, removing punctuation and strings with non-alphabetic properties, and finally lemmatizing text.

After preprocessing, here are the top 10 words by frequency of my reviews:

![Top 10 Word Frequency](./images/top_10_word_frequency.png)

Here are the top 10 words of positvely-sentimented reviews:

![Top 10 Positive Word Frequency](./images/top_10_positive_word_frequency.png)

Here are the top 10 words of negatively-sentimented reviews:

![Top 10 Negative Word Frequency](./images/top_10_negative_word_frequency.png)

I created two separate datasets, one with additional stop words removed and one that did not remove any additional stop words. 

[Here](./working_notebooks/data_exploration_notebook.ipynb) you can find my notebook that goes through the process of adding additional stop words. Additional stop words added include ['dress', 'fit', 'top', 'size', 'very', 'look', 'like', 'color', 'love', 'small']. As we can see, many of these stop words are generally related to how we talk about clothes and may have different meanings depending what word comes in front of or after them like "fits well" versus "does not fit."

I found that my dataset that did not remove any additional stop words during the preprocessing steps regularly performed better. This may be explained by my model looking at both unigrams and bigrams, meaning my model looks at both single words and word pairings, like "fit," and "not fit." 

Below is a WordCloud visualizing the frequency of all words in the dataset.

![WordCloud](./images/wordcloud.png)

The target variable for the model is Positive_Rating. I created this variable utilizing the Rating column with a rating of 4-5 being classified as 1 and a rating of 1-3 being classified as 0. The only variable needed for my model is Review_Text, which could come from any source. Like the distribution for ratings, the distribution for my target is a bit skewed.

![Target Distribution](./images/target_distribution.png)

## Methods

I utilized a Dummy Classifer for the initial model, which returned an accuracy score of 77% by predicting the majority label for each observation. I iterated through the following model algorithms to assess which models to further explore: MultinomialNB(), LogisticRegression(), KNeighborsClassifier(), DecisionTreeClassifier(), XGBoost(), and RandomForestClassifier(). The results were based on cross validation and returning the accuracy score for each model. The top 3 models according to accuracy were LogisticRegression(), XGBoost(), and RandomForestClassifier(). Based on these results, I chose those 3 models to explore further.

I ran multiple grid searches to test hyperparameters for LogisticRegression(), XGBoost(), and RandomForestClassifier(). I ran multiple grid searches with both the dataset without additional stop words and with additional stop words to see which dataset produced better results. My dataset without additional stop words removed produced better results on all models. I utilized my dataset without additional stop words as my final dataset to compare all models with the final dataset. XGBoost() and RandomForestClassifier() models performed well on my training data, but continued to be overfit; the results were significantly worse utilizing cross validation. While Logistic Regression models did not perform as well on training data, these models produced the best results utilizing cross validation. 

Below is a comparison of the cross validation accuracy scores across models:

![Model Accuracy Scores](./images//model_accuracy_scores.png)

## Final Model/Evaluation

The best model based on cross validation scores is a Logistic Regression Pipeline with the following hyperparameters tuned:
- For TfidVectorizer: max_df=.35, max_features=3000, ngram_range=(1,2)
- For LogisisticRegression: solver='lbfgs', C=3, max_iter=30

The final model using Logistic Regression yielded an accuracy score of 88.27% on unseen data, meaning it correctly classified unseen data as positive or not positive over 88% of the time. The initial Dummy Classifier performed with a 77.04% accuracy, meaning I was able to improve accuracy by over 11% by utilizing and tuning a more complex model. From the confusion matrix, we can see that the model misclassified reviews that were not positive as positive over six times as much as classified reviews that were positive as not positive. This imbalance may be explained by our starting class imbalance. 

![Final Confusion Matrix](./images/final_confusion_matrix.png)

The below visualization more clearly demonstrates the difference between positive reviews that were accurately classified versus negative reviews that were accurately classified.

![](./images/class_predictions.png)

## Classifying New Data

Prior to running the inputted data through the model, a function will perform text preprocessing steps. 

In order to obtain new data, I utilized Twitter's API and made API calls to obtain tweets that were related to women's clothing items. Specifically, I made queries directed @ZARA and @h&m that included words such as "dress," "shirt," and "clothing." I then ran these tweets through a preprocessing function so they could be run through my model. My model is able to return a classification for each tweet along with probability of each class (0 or 1). 

From the inspection of my model, it is clear that tweets that are predicted as 0 (negative) are most likely correct and need to be immediately addressed. Tweets (or other comments) that are addressed as 1 are more likely to possibly be class 0 and will require further inspection by a human. This is most likely due to the class imbalance of my initial dataset and my model having more examples of positive reviews than negative reviews when training my model. 

Below is an example of a positive tweet being accurately classified:

![](./images/positive_tweet_correct.png)

This is a great example of a tweet that could be retweeted for visibility and showing a real person representing Zara's brand.

Below is an example of a negative tweet being accurately classified:

![](./images/negative_tweet_correct.png)

This is an example of where Zara could both reply to the tweet to rectify the situation and flag the tweet for further investigation into the barcode and warehousing process.

And below is an example of a negative tweet being inaccurately classified as positive:

![](./images/positive_tweet_incorrect.png)

This tweet shows why tweets being classified as positive may need a second inspection before assigning them to an appropriate response team.

Upon my inspection, there are no positive tweets being inaccurately classified as negative.

## Limitations
While the final model optimizes accuracy of predicted sentiment of comments, it has its limitations. 

- Class Imbalance: almost 80% of the reviews were coded as "positive" based on a rating of 4 or 5
- User error when rating items: based on some of the reviews, it appears as though users may have mixed up the ratings and utilized 1 for positive reviews or 5 for negative reviews

## Next Steps

Given more time, I would expand on this project by:

- Pulling in more data, specifically negative reviews or comments to help better train my model
- Create a dual classification system that first classifys comments as Spam or Not Spam, and then filters out Spam comments and classifies Not Spam as Positive or Negative
- Test my model with other platforms, such as Instagram comments
- Creating a "neutral" target for classification
- Create custom stopwords list, such as removing "not" from stopwords (may be helpful for my model when looking at bigrams)
- Upgrade and deploy an app for brands to utilize to perform Twitter API calls based on user inputted query terms and return a dataframe with one column of tweets and one column of classification

## Conclusion

In conclusion, using my generalizable model will allow women's fashion brand companies to feed comments from any web source to identify positively and negatively sentiment comments and respond appropriately. Utilizing the model will allow companies to save time and by flagging the comments for appropriate teams to respond to, such as Quality Assurance to respond to negative comments, or marketing to respond to and promote positive comments. Responding to these comments appropriately will improve products by identifying potential clothing quality issues if the same sentiment is being repeated, identify potential positive trends, boost visibility by responding to comments, and improve brand loyalty by making customers feel heard.

### References
The following are websites I referenced when coming up with my Business Understanding:
- [Bizibl Commerce](https://bizibl.com/commerce/article/4-reasons-you-should-respond-every-customer-review)
- [VirTasktic](https://www.virtasktic.com/social-media-comments/#:~:text=These%20types%20of%20interactions%20can,people%20bad%2Dmouthing%20your%20brand.)

## Repository Structure
```
├── data
│   ├── Womens Clothing E-Commerce Reviews 2.csv
│   ├── bigram_data.csv
│   ├── final_data.csv
│   ├── final_tweet_dataset.csv
│   ├── single_word_data.csv
│   ├── tweet_dataset.csv
├── images
│   ├── class_predictions.png
│   ├── final_confusion_matrix.png
│   ├── header.png
│   ├── model_accuracy_scores.png
│   ├── negative_recommended.png
│   ├── negative_recommended2.png
│   ├── negative_tweet_correct.png
│   ├── positive_not_recommended.png
│   ├── positive_not_recommended2.png
│   ├── positive_tweet_correct.png
│   ├── positive_tweet_incorrect.png
│   ├── recommendedIND_distribution.png
│   ├── reviews_distribution.png
│   ├── target_distribution.png
│   ├── target_distribution_presentation.png
│   ├── top_10_negative_word_frequency.png
│   ├── top_10_positive_word_frequency.png
│   ├── top_10_word_frequency.png
│   ├── wordcloud.png
├── working_notebooks
│   ├── data_exploration_notebook.ipynb
│   ├── data_modeling_rating_prediction.ipynb
│   ├── data_modeling_sentiment_prediction.ipynb
│   ├── data_no_additional_stopwords.ipynb
│   ├── model_with_tweets.ipynb
│   ├── model_with_tweets2.ipynb
│   ├── twitter_api_exploration.ipynb
├── .gitignore
├── LICENSE
├── README.md
├── final_data_prep.ipynb
├── final_model_tuning.ipynb
├── final_model_with_tweets.ipynb
├── presentation_slides.ipynb
├── trained_model.sav
```
