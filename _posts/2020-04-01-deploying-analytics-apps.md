---
layout: post
title: in progress - analytics application architectures 
excerpt: ""
categories: [aws,s3,tableau,tools,data-engineering]
modified: 2020-04-01
comments: true
---
TODO - update process flow 
TODO - add process diagrams 
TODO - add commands for test 

## overview
How do you select which model to use? Once selected, how do you design the *right* architeceture? These questions, among others like 'how are data processed? In batches? On-demand? Real-time?' need answers before determining the best fit analytics solution for your business scenario. 

Hakon does a good job (1) clarifying the learning and prediction contstraints and (2) describing various business scenarios in [this quora post](https://www.quora.com/How-do-you-take-a-machine-learning-model-to-production/answer/H%C3%A5kon-Hapnes-Strand). Using his framework:

![mlops framework](/img/mlops_framework.PNG)

Hakon breaks down learning into (1) offline and (2) online training and prediction into (1) batch and (2) on-demand. I'll add that 'on-demand' could be quantified to a specific service level agreement. E.g. from point new data enters source, serve results in less than time t.  
 
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
* monitoring & retraining - ml flow, sagemaker, azure ml
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
initial thought... how do I get this up and running as quickly as possible? 
* python - language of choice
* github - version control, source of truth 
* s3 - blob storage 
* athena - required for tableau integration with s3 
* databricks - model hosting, distributed compute 
* tableau desktop - visualization 

**general flow** 
1. create repo / directory structure
2. store raw (input) data in s3
3. train model locally
4. host model (notebook) in databricks  
5. store model output in s3 
6. build schema in athena 
7. connect tableau 

**known limitations**
- while version control exists, capacity to ensure reproducibility and limit version conflict is low
- requires manually copying master branch to databricks... 
- PII is vulnerable  
- no run time / scheduling 
- no drift and model performance monitoring 

### #2
**overview**
while using the same tech stack, additional features such as job scheduling, JupyterLab integrations and the databricks cli can improve our workflow. content pulled from [these docs](https://databricks.com/blog/2019/12/03/jupyterlab-databricks-integration-bridge-local-and-remote-workflows.html) 

* python - language of choice
* github - version control, source of truth 
* github actions - ci
* databricks connect + cli - hosting, compute, scheduling
* s3 - blob storage 
* athena - required for tableau integration with s3 
* tableau desktop - visualization 

**general flow** 
1. install anaconda 
2. [install databricks cli](https://docs.databricks.com/dev-tools/cli/index.html) in the workspace of your chosing
```
pip install databricks-cli
```
3. configure access token & authenticate to the cli
```
databricks configure --token 
```
databricks host = homepage url; https://<some-stuff>.cloud.databricks.com
token = token you created while configuring
4. configure github actions to trigger when merge / pull request is approved to master 
5. create new feature branch (or clone model from databricks)
6. make some changes / develop locally (preferred ide)
7. push to master 
8. update athena schema 
9. tableau desktop / server

**known limitations**
- limited reproducibility
- structured Streaming
- running arbitrary code that is not a part of a Spark job on the remote cluster.
- scala, python, and R APIs for Delta table operations.
- dbutils.fs and dbutils.secrets are supported.
- apache zeppelin 0.7.x and lower.
- PII is vulnerable developing locally 
- no drift and model performance monitoring 
- tableau server required for easy sharing and distribution of dashboard content 

**notes**
- [databricks connect, preferred ide setup instructions](https://docs.databricks.com/dev-tools/databricks-connect.html)
* athena seems to be slow... why
* this seems to be the best lightweight option for ml deployment
* as of the time of this writing, the jupyterlab integration with databricks-connect did not support windows machines

### #3
**overview**
saw [this article](https://towardsdatascience.com/simple-way-to-deploy-machine-learning-models-to-cloud-fd58b771fdcf) by @[Tanuj Jain](https://towardsdatascience.com/@tanuj.jain.10)and liked the stack. these tools in addition to git and s3 could make for a nice next step... note the switch to power bi. we are able to connect to aws' [s3 rest api](https://docs.aws.amazon.com/AmazonS3/latest/API/Welcome.html) via an embeded python script. while [tableau has one too](https://community.tableau.com/thread/274550) it looks like it is limited to creating calculated fields...? more research is needed there as i am not as familiar with that feature.  
* python - language and model package source
* github - version control 
* flask - web servie to build rest api 
* docker - container service 
* s3 - distributed storage
* aws ec2 - distributed compute      
* power bi - viz

**general flow** 
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
- no internet, no development
- no / crude run time scheduling 
- no drift / model performance monitoring 

**notes**
- how can we work pulumi into this flow? 
- would allow you to attach required services at the model level
- could be interesting to scale up / down compute based on model accuracy... i.e. add data and compute when accuracy dips below threshold to billing threshold; when slope of performance increase declines and dollars invested outpace performance gained, reduce investmen; this methodology would require model performance to be linked to business revenue which may not always be possible

### #4
**overview**

* python - language and model package source
* github - version control 
* flask - web servie to build rest api 
* docker - container service 
* s3 - distributed storage
* rackspace cloud - compute      
* power bi - viz


**flow** 
1. create repo / directory structure

 
**known limitations**
- no internet... no development 
- 

### #4
**overview**

* python 
* github
* ml flow 
* databricks
* s3      
* power bi

**flow** 
1. create repo / directory structure

 
**known limitations**
- 

### useful links 
* [tableau docs](https://www.tableau.com/about/blog/2017/5/connect-your-s3-data-amazon-athena-connector-tableau-103-71105)
* [parquet overview](https://databricks.com/glossary/what-is-parquet)
* [azure + aws services comparison](https://docs.microsoft.com/en-us/azure/architecture/aws-professional/services) 
* [simple ml model deployment](https://towardsdatascience.com/simple-way-to-deploy-machine-learning-models-to-cloud-fd58b771fdcf)
* suggested [aws + docker prod workflow](https://towardsdatascience.com/ml-models-prototype-to-production-6bfe47973123) by [@Shreya](https://towardsdatascience.com/@shreyaghelani)
* [ml ops key challenges](https://github.com/microsoft/MLOps)
* [jupyter bridges local & remote workflows with databricks](https://databricks.com/blog/2019/12/03/jupyterlab-databricks-integration-bridge-local-and-remote-workflows.html)


