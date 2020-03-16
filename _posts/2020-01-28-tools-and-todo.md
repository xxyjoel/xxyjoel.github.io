---
layout: post
title: tools and todos  
excerpt: "resources I use frequently, shout out to instrumental developers in the community and projects I am currently interested in."
categories: [code,musings,tools]
modified: 2020-01-28
comments: true
---

TODO - add / finish table of contents
TODO - transfer projects from notes to this post

## overview 
this is the start of a collection of tools and resources and projects currently in progress. 

## resources 
See [my github stars](https://github.com/xxyjoel?tab=stars) for a complete list. Here are some of my favorite:

* [public api list for data](https://github.com/public-apis/public-apis) - a complete list of public apis for you to get virtually any size / shape of data you could possibly want. Thank you to all the [contributors](https://github.com/public-apis/public-apis/graphs/contributors) (specifically, [Dave](https://github.com/davemachado)! 

* [awesome python libs](https://github.com/vinta/awesome-python) - curated list of amazingly-awesome python frameworks... categorized by use case! Thank you to all the [contributors](https://github.com/vinta/awesome-python/graphs/contributors) (specifically, [vinta](https://github.com/vinta)! 

* [advanced pandas](https://github.com/firmai/pandasvault/blob/master/README.md) - curated list of advanced pandas functions. Thank you to all the [contributors](https://github.com/firmai/pandasvault/graphs/contributors)!  

* [models by industry and application scenario](https://github.com/xxyjoel/industry-machine-learning) - I love when the gap between technical and business is virtually non-existent and [FirmAI](https://www.firmai.org/) does an impeccable job categorizing models by application scenarios. 

* [automated feature engineering](https://docs.featuretools.com/en/stable/) - [github here](https://github.com/FeatureLabs/featuretools) with tons of great tools to help automate your feature engineering process 

## 2020 projects
**automated eda** - while the [objectives of EDA](https://en.wikipedia.org/wiki/Exploratory_data_analysis) are to (1) suggest hypothesis about the causes of obsereved phenomena (2) assess assumptions on which statistical inference will be based (3) support the selection of appropriate statistical tools and techniques and (4) probide a basis for further data colleciton through experimentation, the techniques and tools used are fairly limited. By profiling your data prior to conducting analysis, I believe you can automate much of discovery.   

TODO - for a number of data sets, based on data types provided, size, unique value counts, missing value counts, duplicate counts, distributions and other notable attributes I am not thinking of, label all possible visuals (from the list below) and the criteria used to select them. this will set you up for a supervised learning approach. future input = pandas df, output = recommended labeled chats, graphs and summary statistics  

TODO - limitations arrise given single df input. determine how to provide same service for related tables. when plotting numerical / int distributions, determine how to differentiate between IDs (effectively numberic categorical values) and non-IDs 

**automated feature engineering** - as noted above, there are some auto feature engineering tools available, however, they have been privatized and taken off the open source market; current methods are unintuitive 

**csa rules engine** - streamed product data to define and categorize the products and services offered by major cloud providers. Purpose, in addiiton to functional constraints - compatible with s3 / lambda, - and pricing constraints - compute range, optimum listed. goal being to make optimum solution architecture easier to define. next step would be to produce [pulumi](https://www.pulumi.com/) config files for optimum pairings.  

**predictive maintenance** - working with derek snow, [firmai.org's](https://www.firmai.org) founder, to build out ml notebooks around [maintenance prediciton](https://en.wikipedia.org/wiki/Predictive_maintenance)  

**data science toolkit** - consolidate github resources, useful packages and tools for various common analysis problems 

**infrastructure toolkit** - consolidate infrastructure tools / resources, their limitations and application scenarios
