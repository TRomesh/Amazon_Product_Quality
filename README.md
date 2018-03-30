# Description of Project

Our project is based off the paper ["A Probabilistic Graphical Model for Brand Reputation Assessment in Social Networks"](https://dl.acm.org/citation.cfm?id=2492556), written by researchers at Northwestern University and presented at the 2013 IEEE/ACM International Conference on Advances in Social Networks Analysis and Mining. The paper proposes a probabilistic graphical model to measure "social brand reputation", a metric of how positively or negatively a "brand" is perceived on a social network. The paper uses a Facebook dataset, where "brands" include public pages, such as Barach Obama or Starbucks. Along with social brand reputation, the proposed model computes "user positivities", a metric of how positive or negative a user's posts are on the social network. Both social brand reputation and user positivities are modeled as hidden variables whose values are inferred from the sentiment of social media posts, which act as the observed variable. The inference is conducted via Markov Chain Monte Carlo sampling.

We aim to produce a similar model to the one described in the paper on an Amazon product review dataset. This dataset is a good fit, because just like in a social network, Amazon has "brands" (products) with "reputations" (5-star ratings) determined by "posts" (reviews) from "users". We can evaluate our model's success by comparing each product's "social brand reputation" with their real Amazon rating.

This model computes P(R|S) and P(U|S), where R is a brand's reputation or a product's rating, U is user positivity, and S is the sentiment of posts or reviews. The model does not compute P(R|U), the probability of a particular user liking a product. In other words, this model only computes the "aggregate" quality of a product, given a corpus of reviews. This model is not a recommendation system. It would be interesting to explore the use of MCMC models in recommendation systems in the future.

Sentiment analysis is performed by the [Textblob](http://textblob.readthedocs.io/en/dev/index.html) package, which wraps around the [pattern](https://www.clips.uantwerpen.be/pages/pattern-en#sentiment) package created by the CLiPS research center at the University of Antwerp. The sentiment analysis is performed by assigning a sentiment polarity to every adjective in the text and averaging all of the adjective sentiments. This is a very simple model; more advanced models could be trained and tested on [the Amazon Reviews for Sentiment Analysis dataset](https://www.kaggle.com/bittlingmayer/amazonreviews). For simplicity, we assume that Textblob's false positive rate and false negative rate are approximately equal, so that the predicted rating for any given product is not impacted by errors in Textblob's sentiment prediction.

[The original paper](https://dl.acm.org/citation.cfm?id=2492556) uses a much more involved ensemble sentiment analysis algorithm that we excluded in our model due to time constraints. The original paper also has an involved data cleaning phase that filters out brands and users with very few posts, as well as users with too many posts (spammers). We did not include this phase.


# Data

We received our data from the [University of California San Diego's Amazon dataset](http://jmcauley.ucsd.edu/data/amazon/). In particular, the dataset we used was "reviews_Amazon_Instant_Video_5.json.gz", a set of reviews for Amazon Instant Video content, but our technique should work on any of the 5-core datasets they provided.


# Code Execution

1. Run "python parse.py (data_file) (pickle_file)" to parse the data file into a Python dictionary, which is stored in a pickle file. The defaults are "reviews_Amazon_Instant_Video_5.json.gz" and "products.pkl". This step only needs to be run once per dataset.
2. Run "python sample.py (pickle_file) (rounds)" to run a MCMC sampling for the specified number of rounds to predict the probability that each product is "of quality". The defaults are "products.pkl" and 100.
3. Run "python evaluate.py" to create a linear regression model measuring the strength of correlation between our model's output probabilities and the real Amazon 5-star score. This model could also be used as a rating-prediction score for any given user.


# Current Results

The below results are from a linear regression betweeen the predicted probability that an arbitrary users would like an item, and the actual Amazon review score. This was done on 20 products.

    LinregressResult(slope=0.53754379740098479, intercept=3.8027298531955478, rvalue=0.2402211636806181, pvalue=0.30764972157693871, stderr=0.51198766872208323)


# Checklist

Completed:
- [X] Sentiment Analysis
- [X] Implementation of Algorithm (for product ratings)
- [X] Evaluation (compare predicted 5-star rating to real Amazon rating by training a regression model)

Next Steps:
- [ ] Fix probability calculation (sample.py line 12) (ask prof)
- [ ] Calculate+print User General Sentiment, + incorporate user general sentiment into current probability calculations

Later:
- [ ] Write up a report / demo / presentation (~4/30) (ask prof)
  - [ ] Create a visualization demonstrating our results
    - [ ] Evaluate how many rounds of MCMC are needed to converge with our dataset
    - [ ] Play around with sigma, alpha, and beta values and evaluate impact on model performance

If time permits:
- [ ] Add [multiprocessing](https://docs.python.org/3/library/multiprocessing.html) and run on larger dataset on cluster (cloud, school servers, etc.)
- [ ] [Query real Amazon reviews to evaluate on from the Amazon API](https://python-amazon-product-api.readthedocs.io/en/latest/)
- [ ] Manually expand Amazon dataset (manually check scores on Amazon website)