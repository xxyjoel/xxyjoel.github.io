---
layout: post
title: in progress - analytics application architectures 
excerpt: ""
categories: [aws,s3,tableau,tools,data-engineering]
modified: 2020-04-01
comments: true
---

## overview
How do you select which model to use? Once selected, how do you design the *right* architeceture? These questions, among others like 'how are data processed? In batches? On-demand? Real-time?' need answers before determining the best fit analytics solution for your business scenario. 

Hakon does a good job (1) clarifying the learning and prediction contstraints and (2) describing various business scenarios in [this quora post](https://www.quora.com/How-do-you-take-a-machine-learning-model-to-production/answer/H%C3%A5kon-Hapnes-Strand). Using his framework:

![mlops framework](/img/mlops_framework.PNG)

Hakon breaks down learning into (1) offline and (2) online training and prediction into (1) batch and (2) on-demand. I'll add that 'on-demand' and 'real-time' can be further quantified to a specific service level agreements. E.g. from point new data enters source, serve results in less than time t.  
 
Once service requirements are defined, we can break our analytics project - one where we need to build and productionalize a model, surfacing its results - into nine (9) components:

* integration - build ingestion and distribution pipes 
* modeling - design best-fit model; varies based on model complexity
* version control - track model logic and parameter changes 
* computation - determine appropriate compute resources; can be local or distributed; qty / magnitude based on business need
* storage - store model input, parameters, results and applicable performance metrics; can be local or distributed  
* hosting - ensure reliability and accessibility of application
* configuration - manage application dependencies; e.g. is [model] in [container]? is [package] [version] installed in [environment]?
* monitoring & retraining - assess mode performance at time t and retrain, if applicable
* scheduling - synchronize ingestion, model and business timing needs
* visualization - transfer knowledge to business domain; surface model results to target end user(s)

There are *many* tools that specialize in these componet categories. depending on (1) the current state of your architecture and (2) what you are trying to achieve in the next n month time period, you may want to make tradeoff between cost, flexibility and usability. below are a few solution architecture ideas and which business case they might fit in. 

* integration - [fivetran](https://fivetran.com/), stitch, aws glue, azure data factory
* modeling - python, scikit, pytorch, tensorflow, [awesomeML](https://github.com/josephmisiti/awesome-machine-learning), etc. 
* version control - git, databricks, ml flow
* computation - azureVM, ec2, databricks, etc. 
* storage - azure blob, s3, cosmos, dynamo db, azure synapse, redshift, etc.   
* hosting - sagemaker, azure ml, flask, django, databricks, etc.; more on [wiki.python.org](https://wiki.python.org/moin/WebFrameworks) 
* configuration - pickle, docker, kubernetes, ini / toml / yaml / env files; good read on the latter types [here](https://hackersandslackers.com/simplify-your-python-projects-configuration/) 
* monitoring & retraining - sagemaker, azure ml 
* scheduling - airflow, sagemaker, azure ml, aws step functions + lambda, azure logic apps, etc. 
* visualization - power bi, tableau, looker

TODO - finish flow + diagrams 
TODO - select & test
TODO - cost analysis / comparisons 
TODO - table of contents
TODO - additional stack selections added 

## stack selection
### #1
**overview**
initial thought... how do I get this up and running as quickly as possible
* python 
* git
* s3 - blob storage for your csv, parquet or json files
* athena - required for tableau integration with s3 
* databricks - model hosting, distributed compute 
* tableau desktop - visualization 

**flow** 
1. create repo / directory structure
2. store raw (input) data in s3
3. train model locally
4. host model (notebook) in databricks  
4. store model output in s3 
5. build schema in athena 
6. connect tableau 

**known limitations**
- while version control exists, capacity to ensure reproducibility and limit version conflict is low
- no run time / scheduling 
- no drift and model performance monitoring 

### #2
**overview**
saw [this article](https://towardsdatascience.com/simple-way-to-deploy-machine-learning-models-to-cloud-fd58b771fdcf) by @[Tanuj Jain](https://towardsdatascience.com/@tanuj.jain.10)and liked the stack. these tools in addition to git and s3 could make for a nice next step... note the switch to power bi. we are able to connect to aws' [s3 rest api](https://docs.aws.amazon.com/AmazonS3/latest/API/Welcome.html) via an embeded python script. while [tableau has one too](https://community.tableau.com/thread/274550) it looks like it is limited to creating calculated fields...? more research is needed there as i am not as familiar with that feature.  
* python 
* git
* flask - web servie to build rest api 
* docker - container service 
* s3
* aws ec2 - compute virtualization
* power bi

**flow** 
1. create repo / directory structure
2. store raw (input) data in s3
3. train model locally
4. wrap model logic into flask application
5. containerize flask app 
6. host image on aws ec2 
7. store results in s3
8. ingest into power bi (python script to s3 api)
 
**known limitations**
- additional costs incured for storage and compute 
- no / crude run time scheduling 
- no drift / model performance monitoring 

### useful sources 
* [tableau docs](https://www.tableau.com/about/blog/2017/5/connect-your-s3-data-amazon-athena-connector-tableau-103-71105)
* [parquet overview](https://databricks.com/glossary/what-is-parquet)
* [azure + aws services comparison](https://docs.microsoft.com/en-us/azure/architecture/aws-professional/services) 
* [simple ml model deployment](https://towardsdatascience.com/simple-way-to-deploy-machine-learning-models-to-cloud-fd58b771fdcf)
* suggested [aws + docker prod workflow](https://towardsdatascience.com/ml-models-prototype-to-production-6bfe47973123) by [@Shreya](https://towardsdatascience.com/@shreyaghelani)


