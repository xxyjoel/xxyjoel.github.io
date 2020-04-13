---
layout: post
title: study material & useful links
excerpt: "ds & ml guides, best-practices, statistics fundamentals, ensemble bible and other useful study tools to maximize your value when delivering data science applications"
categories: [code,musings,tools]
modified: 2020-03-16
comments: true
---
## overview 
a study-material list i believe to be "sources of truth" on their respective topics; it is not enough for an analytics professional to 'generate insights'; the professional must be able to (1) describe and communicate insights and recommendations and (2) have intelligent discussions as to how the model(s) should be designed and supported in production; required expertise (or familiarity) in the following subjects: 

* general scientific methods (how to setup, run, qualify and ensure experiment reporducibility)
* maths, statistics, probability theory, et al  
* scripting & package knowledge (Python, R and respective packages)
* visualization  (packages, reporting tools, etc.)
* computer science / programming basics (version control, testing framework, design standards, etc.)
* data engineering (how are you ingesting, transforming and updating your data feed)
* cloud architecture (where / how are your models going to be supported?)

given the pace at which technology is ever-changing, there should be a portion of ones time spent, to use a nn reference, "exloring" versus exploiting the current mastered skill sets; certain tools may solve a problem more effecienty with minimum time investment (e.g. shiny versus tableau or power bi mastery)  

## current topics of interest
* ml - the goldilocks' fit (avoiding under / overfitting) 
* ml - improving model testing and cross vadlidation procedures in production
* ml - selecting & optimizing parameters & hyperparameters
* ml - objectives and loss-functions 
* ml - proper utilization of label versus one hot encoding 
* ml - missing data management (done the right way) 
* py - improving management and use of scopes, classes, instances, methods
* py - data classes 

## resource links  
* [awesome production machine learning](https://github.com/EthicalML/awesome-production-machine-learning) - curated list of open source libs to deploy, monitor, version and scale your machine learning

* [data scientist roadmap](https://github.com/MrMimic/data-scientist-roadmap) - really well done repo by [MrMimic](https://github.com/MrMimic) that outlines fundamentals in various areas 

* [statistics fundamentals](https://www.statlect.com/fundamentals-of-statistics) - modules ranging from point estimation to bayesian inference 

* [python best practices, green tea press](http://greenteapress.com/thinkpython/html/index.html) - how to think like a (python) computer scientist 
* [hitchhikers guide to python, o'reilly](https://docs.python-guide.org/) - environment setup standards and generally how to write great python code 

* [hundred page machine learning book](http://themlbook.com/) - Andriy is nice enough to offer this book on a 'read first, buy later' principal; i eneded up purchasing the paperback copy... because why wouldnt you want to support people doing good for the community? 

* [hundred page machine learning book - data & code samples](https://github.com/aburkov/theMLbook) - the code to go with the hundred page ml book's examples

* [ml prod deployment methods](https://www.kdnuggets.com/2019/06/approaches-deploying-machine-learning-production.html) - overview of some deployment methods with varying environments & scenarios 

* [ml evaluation methods p1](https://www.oreilly.com/data/free/files/evaluating-machine-learning-models.pdf) - overview of basic evaluation metrics and methods for common model application scenarios 

* [ml evaluation methods p2](http://pages.cs.wisc.edu/~dpage/cs760/evaluating.pdf) - overview of basic evaluation metrics and methods for common model application scenarios 

* [kaggle ensembling guide](https://mlwave.com/kaggle-ensembling-guide/) - methods of improving the accuracy of various ml tasks by joining models 

* [application scenarios](https://github.com/firmai/industry-machine-learning) - i have linked to this before in previous posts, however, it is useful to understand *where* to apply the tools in your toolbox and [firmai](https://github.com/firmai) provides the best list i have found to date  

* [managing missing data...the right way](https://play.google.com/books/reader?id=OaiODwAAQBAJ&hl=en&pg=GBS.PT15) - many packages recommend you 'fill in' missing values with mean, medain, mode, etc. when in fact this is rarely the path to be taken; a good response to this problem is summarized in [this stack exchange answer](https://stats.stackexchange.com/questions/391602/how-to-handle-missing-data-in-machine-learning)

* [google's guide to ml ops](https://cloud.google.com/solutions/machine-learning/mlops-continuous-delivery-and-automation-pipelines-in-machine-learning) 

## research papaers
good reads (and how to find more of them)
* [hidden tech debt in ml systems](https://papers.nips.cc/paper/5656-hidden-technical-debt-in-machine-learning-systems.pdf) 
* [ml production readiness rubric](https://static.googleusercontent.com/media/research.google.com/cs//pubs/archive/aad9f93b86b7addfea4c419b9100c6cdd26cacea.pdf) 
* [ssrn top 10k research papers](https://hq.ssrn.com/rankings/Ranking_display.cfm?TRN_gID=10&utm_source=share&utm_medium=ios_app&utm_name=iossmf)
* [mathematics and statistics online](https://projecteuclid.org/)