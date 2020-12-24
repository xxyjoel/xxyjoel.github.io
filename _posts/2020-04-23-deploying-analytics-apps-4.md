---
layout: post
title: analytics application architectures - part 4
excerpt: "alternate sclablable application architectures with automated deployment workflows, model lineage and versioning; my preferred stack"
categories: [s3,ec2,powerbi,dask,python,tools,data-engineering,databricks,ml,mlflow, github actions]
modified: 2020-04-17
comments: true
---
## overview 
In the [third post](https://xxyjoel.github.io/articles/2020-04/deploying-analytics-apps-3) we completed a sample end to end analytics workflow using python, ml flow databricks and a few basic aws tools. However, because databricks is spark based, 

### recalling part 3 limitations
* 

### part 4 - preferred stack
* python + [dask](https://docs.dask.org/en/latest/) - language of choice; package for improving compute 
* github - version control 
* github actions - ci
* s3 - distributed storage
* ec2 - distributed compute    
* flask - python api framework 
* mlflow - ml lifecycle management
* docker - container service
* power bi - visualization 

**general flow** 
1. 

**rationale for changes"**
* automated model integration 

**known limitations**
* 

**notes**
* [deploying containerized flask applications with aws and docker](https://linuxacademy.com/blog/linux-academy/deploying-a-containerized-flask-application-with-aws-ecs-and-docker/)
* [hyperparam tuning with dask and mlflow](https://karimlahrichi.com/2020/03/29/end-to-end-machine-learning-project-with-scikit-learn-dask-distributed-and-mlflow/)
* [minimul viable data scientist](https://minimum-viable-data-scientist.readthedocs.io/en/latest/) 
* [microman, python task creator](https://github.com/michalwols/microman)