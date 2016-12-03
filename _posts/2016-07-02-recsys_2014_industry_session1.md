##Recsys 2014 Industry 

###Data sources:

Catalog data:

	feed provided by the merchants

User behavior data:
	
	large scale intent data
	all visits to merchant websites
	page views, basket, sales event

Ad display data:

	Displayed and clickd ads
	
	
###pipelines

User with browsed products -> Retrieving Candidates from sources -> scoring -> winner selection

###Retrieving user specific products

1. The need: storing[user-interesting products] vectors

	* diffcult to store and retrieve at scale
	* hard to keep up to date

2. Using seen products as a proxy

	* Store the [user-viewed products] vectors
	* Store [viewed product - interesting products] vector
	* based on aggregated user behavior data
	* computable offline
	* Final ranking by a ML model
	
###Scoring products

1. Need to fuse data from serveral sources

	* Product-specific
	* User-specific
	* user-product interactions
	* display-specific

2. First solution: regression model

	* Predict P(product click then sale)
	* Easy to evaluate
	
3. Second solution: ranking model

	* still maximizing post-click sales
	* can be evaluated only on multi-product banners
	
###Picking the right products

Serveral questiong to pick the winners:

	* User-product fatigue
	* Need to store actual product display counts
	
	* Independent product choice assumption
	* Need to introduce diversity in the banners
	
###Solution

2 step prediction:

1. A fast pass to remove most of the candidates
2. a slow pass to score accurately the remaining candidates

###Scaling up

* 1.7 billion users
* Up to 200k recommended product/second





 
