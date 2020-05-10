---
layout: post
title: in progress - analytics application architectures 
excerpt: "application architectures for building, deploying and maintaining ml models"
categories: [aws,s3,tableau,tools,data-engineering, ml]
modified: 2020-04-01
comments: true
---

TODO - update process flow  
TODO - add process diagrams  
TODO - add commands for test  
TODO - finish flow + diagrams  
TODO - select & test  
TODO - cost analysis / comparisons   
TODO - table of contents  
TODO - additional stack selections added  
TODO - aws s3 windows connection post linked  
TODO - s3 to power bi connection via s3 api  
TODO - add content to each architecture and break them out into individual posts

## overview
* How do you select which model to use? 
* Once selected, how do you design the *right* architeceture? 
* How do you manage the deployment of the models? 
* Who owns the model prior to deployment? After deployment? 

More importantly, how do I build an application powered by machine learning? This quetsion, among others, need answers before determining the best fit analytics solution for your business scenario. Before we get to building, it helps to understand what tools we have to work with. Looking at how cloud service providers categorize their technology can give us a quick overview of technologies both related to, and outside of, the machine learning space. Ill get a little meta and add that communities are more effective and effecient innovators, compared to their corporate counterparts. Because of this fact, it is common for AWS / Azure / GCP et al to [capitalize on the open-source market](https://www.geekwire.com/2019/elasticsearch-distribution-amazon-web-services-sends-shockwaves-open-source-software/). Ergo there is *probably* an open source alternative to the cloud service providers' prodcts I am going to list. There is a lot to consider when selecting a tool, but I will do my best to recommend open-source projects where possible. I digress. Cloud products fall into a few categories: 

* compute
* storage
* database
* migration
* networking & content delivery
* mobile services
* developer tools
* management tools
* security, identity & compliance
* analytics
* application services
* messaging
* internet of things
* support, customer
* machine learning / modeling
* game development
* media service
* application integration
* cost management
* containers
* container management

If you are insterested, I have a working document with a [compiled list of tools here](https://docs.google.com/spreadsheets/d/1dPar1Ir7--63BboNhbrkcCqwehtwO3VcfBBaYjLJqpY/edit?usp=sharing). 

### Preview 
| provider  | tool   | category  |description | cost_model | integration | modeling | ... | 
| ------------- |:-------------:| -----:|-----:|-------:|-----:|-----:|-----:|
| fivetran     | fivetran_standard | migration | etl service for various apps | pay as you go | 1 | 0
| aws  | sagemaker | modelng | model management service | pay as you go | 0 | 1
| MIT License  | jenkins | integration | ci/cd service | free | 0 | 0

Now that we have a good idea of the tools that are available, we can being to define out service requirements. Hakon does a good job (1) clarifying the learning and prediction contstraints and (2) describing various business scenarios in [this quora post](https://www.quora.com/How-do-you-take-a-machine-learning-model-to-production/answer/H%C3%A5kon-Hapnes-Strand). 

![mlops framework](/img/mlops_framework.PNG)

Worth noting how Hakon breaks down learning into (1) offline and (2) online training and prediction into (1) batch and (2) on-demand. I'll add that 'on-demand' could be quantified to a specific service level agreement. E.g. from point new data enters source, serve results in less than time t. Constrains, such as team skill set, may also shapre your optimum service requirements. Can your data engineers productionalize a jupyter notebook? Can they seamlessly convert python to scala? Or do your data scientist need to deploy their own work? Do you have a machine learning engineer? Etc. 

Once service requirements and constraints are defined, we can break our analytics project - one where we need to build and productionalize a model and surfacing its results - into nine (9) components and begin selecting our ideal toolset:

* **integration** - build ingestion and distribution pipes 
* **modeling** - design best-fit model; varies based on model complexity
* **version control** - track changes to models, parameters and data 
* **computation** - determine appropriate compute resources; can be local or distributed; qty / magnitude based on business need
* **storage** - store model input, parameters, results and applicable performance metrics; can be local or distributed  
* **hosting** - ensure reliability and accessibility of application
* **configuration** - manage application dependencies; e.g. is [model] in [container]? is [package] [version] installed in [environment]?
* **monitoring & retraining** - assess mode performance at time t and retrain, if applicable
* **scheduling** - synchronize ingestion, model and business timing needs
* **visualization** - transfer knowledge to business domain; surface model results to target end user(s)

As noted above in the toolkit, there are *many* tools that specialize in these componet categories. depending on (1) the current state of your architecture (2) your constrains and (3) what you are trying to achieve, you may want to make tradeoff between cost, flexibility and usability. Below are a few solution architecture ideas and which business case they might fit in. 

## stack selection
### #1 - quick and easy
**overview**
how do I get this up and running as quickly as possible? 
* python - language of choice
* github - version control, source of truth 
* s3 - blob storage (model input + output)
* local machine - compute 
* [dask](https://docs.dask.org/en/latest/) - package to greatly improve compute performance
* power bi desktop 

**general flow**
1. install python, anaconda, dask and your prefered IDE 
1. create repo / directory structure
    * basic repo structure 
    ```
    ├── LICENSE
    ├── README.md     # Top-level README for developers using this project.
    ├── data          # ingestion commands; templates for calling various data sources
    │   ├── external  # connection to data from third party sources, if applicable.
    │   ├── interim   # connection to intermediate data that has been transformed.
    │   └── raw       # connection to the original, immutable data dump.
    │
    ├── src           # trained and serialized models
    ├── notebooks     # Jupyter notebooks to assist with documentation
    ├── configuration # toml, yml, or otherwise
    ├── .gitignore    # intentionally untracked files
    ├── requirements.txt     # Python file used to install runtime dependencies
    ``` 
1. store / access raw (input) data in s3
    * I use vs code and have had luck using the [aws toolkit](https://aws.amazon.com/visualstudiocode/)
1. train model locally  
1. store model output in s3
    * make sure your file format is appropriate for your selected visualization tool
1. push changes to master when ready to productionalize
1. setup power bi 
    * as described [here](https://community.powerbi.com/t5/Service/Power-BI-Web-App-and-AWS-S3/td-p/617949) there are two methods for connecting to s3 from power bi, (1) using an odbc data source or (2) call the aws s3 api direectly through the the power bi web connector. 
    * I will go into more detail here on another post 

**limitations**
- local development limits compute and storage and has general security concerns
- while version control exists, capacity to ensure model reproducibility and limit version conflict is low
- PII is vulnerable  
- no run time / scheduling; dashbaord updates require a manual run of the model(s) 
- no drift and model performance monitoring 
- limited reproducibility
- no drift and model performance monitoring 


### #2 - improving performance
**overview**
While using the same tech stack, additional features such as job scheduling, automated deployment and the databricks cli can improve our workflow. While i was not able to get it working in a reasonbile amount of time on a window machine, jupyterlab integration is another potential option at this stage. Content pulled from [these docs](https://databricks.com/blog/2019/12/03/jupyterlab-databricks-integration-bridge-local-and-remote-workflows.html) can help walk you through the process. Note, there are potential security concerns with the jupyterlab integration. 

* python - language of choice
* github - version control, source of truth 
* databricks connect + cli - hosting, compute, scheduling
* s3 - blob storage 
* power bi - visualization 

**general flow** 
1. install python + your favorite IDE 
1. setup a databricks account and configure a cluster with python   
1. [install databricks cli](https://docs.databricks.com/dev-tools/cli/index.html) in the workspace of your chosing
```
pip install databricks-cli
```
4. configure access token, authenticate to the cli and verify contents of workspaces and specific user profiles; open your bash or powershell core and run the following commands
```
databricks configure --token 
```
```
databricks host (should begin with https://): https://<some-stuff>.cloud.databricks.com
token: <token you created while configuring access token>
```
```
databricks workspace ls
databricks workslace ls /Users/<user@email.com>
databricks libraries list
```
```databricks library list``` will not pick up packages that have been installed via anaconda e.g. numpy / pandas. i recommend installing python directly so you can control package versions from the same location  

5. create repo / directory structure
    * basic repo structure 
    ```
    ├── LICENSE
    ├── README.md     # Top-level README for developers using this project.
    ├── data          # ingestion commands; templates for calling various data sources
    │   ├── external  # connection to data from third party sources, if applicable.
    │   ├── interim   # connection to intermediate data that has been transformed.
    │   └── raw       # connection to the original, immutable data dump.
    │
    ├── src           # trained and serialized models
    ├── notebooks     # Jupyter notebooks to assist with documentation
    ├── configuration # toml, yml, or otherwise
    ├── .gitignore    # intentionally untracked files
    ├── requirements.txt     # Python file used to install runtime dependencies
    ``` 
1. store / access raw (input) data in s3
    * I use vs code and have had luck using the [aws toolkit](https://aws.amazon.com/visualstudiocode/)
1. train model locally, taking a percentage of the total population 
    * [lukas @kdnuggets](https://www.kdnuggets.com/2019/05/sample-huge-dataset-machine-learning.html) does a good job explaining a few methods around how to correctly select a sample from a large dataset 
1. create new feature branch (or clone existing model from databricks)
1. make some changes / develop locally (preferred ide)
1. push to master (standard git flow)
1. push to databricks workspace 
```
databricks workspace -h
Usage: databricks workspace [OPTIONS] COMMAND [ARGS]...

  Utility to interact with the Databricks Workspace. Workspace paths must be
  absolute and be prefixed with `/`.

Options:
  -v, --version
  -h, --help     Show this message and exit.

Commands:
  delete      Deletes objects from the Databricks...
  export      Exports a file from the Databricks workspace...
  export_dir  Recursively exports a directory from the...
  import      Imports a file from local to the Databricks...
  import_dir  Recursively imports a directory from local to...
  list        List objects in the Databricks Workspace
  ls          List objects in the Databricks Workspace
  mkdirs      Make directories in the Databricks Workspace.
  rm          Deletes objects from the Databricks...
```
1. configure databricks notebook to run on more (or all) data in your target bucket 
1. output results from all data to target bucket
1. connect power bi to target bucket 

**known limitations**
- local development limits compute and storage and has general security concerns
- while version control exists, capacity to ensure model reproducibility and limit version conflict is low
- PII is vulnerable  
- no drift and model performance monitoring 
- limited reproducibility
- running arbitrary code that is not a part of a Spark job on the remote cluster.
- no drift and model performance monitoring 

**notes**
- [databricks connect, preferred ide setup instructions](https://docs.databricks.com/dev-tools/databricks-connect.html)
* as of the time of this writing, the jupyterlab integration with databricks-connect did not support windows machines

### #3 - automating your workflow 
**overview**
saw [this article](https://towardsdatascience.com/simple-way-to-deploy-machine-learning-models-to-cloud-fd58b771fdcf) by @[Tanuj Jain](https://towardsdatascience.com/@tanuj.jain.10)and liked the stack. these tools in addition to git and s3 could make for a nice next step...
* python - language and model package source
* github - version control 
* s3 - distributed storage
* ec2 - distributed compute      
* power bi - viz
* python - language of choice
* github - version control, source of truth 
* databricks connect + cli - hosting, compute, scheduling
* s3 - blob storage 
* power bi - visualization 

**general flow** 
1. install python + your favorite IDE 
1. setup a databricks account and configure a cluster with python   
1. [install databricks cli](https://docs.databricks.com/dev-tools/cli/index.html) in the workspace of your chosing
```
pip install databricks-cli
```
4. configure access token, authenticate to the cli and verify contents of workspaces and specific user profiles; open your bash or powershell core and run the following commands
```
databricks configure --token 
```
```
databricks host (should begin with https://): https://<some-stuff>.cloud.databricks.com
token: <token you created while configuring access token>
```
```
databricks workspace ls
databricks workslace ls /Users/<user@email.com>
databricks libraries list
```
```databricks library list``` will not pick up packages that have been installed via anaconda e.g. numpy / pandas. i recommend installing python directly so you can control package versions from the same location  

5. create repo / directory structure
    * basic repo structure 
    ```
    ├── LICENSE
    ├── README.md     # Top-level README for developers using this project.
    ├── data          # ingestion commands; templates for calling various data sources
    │   ├── external  # connection to data from third party sources, if applicable.
    │   ├── interim   # connection to intermediate data that has been transformed.
    │   └── raw       # connection to the original, immutable data dump.
    │
    ├── src           # trained and serialized models
    ├── notebooks     # Jupyter notebooks to assist with documentation
    ├── configuration # toml, yml, or otherwise
    ├── .gitignore    # intentionally untracked files
    ├── requirements.txt     # Python file used to install runtime dependencies
    ``` 
1. store / access raw (input) data in s3
    * I use vs code and have had luck using the [aws toolkit](https://aws.amazon.com/visualstudiocode/)
1. train model locally, taking a percentage of the total population 
    * [lukas @kdnuggets](https://www.kdnuggets.com/2019/05/sample-huge-dataset-machine-learning.html) does a good job explaining a few methods around how to correctly select a sample from a large dataset 
1. create new feature branch (or clone existing model from databricks)
1. make some changes / develop locally (preferred ide)
1. push to master (standard git flow)
1. push to databricks workspace 
```
databricks workspace -h
Usage: databricks workspace [OPTIONS] COMMAND [ARGS]...

  Utility to interact with the Databricks Workspace. Workspace paths must be
  absolute and be prefixed with `/`.

Options:
  -v, --version
  -h, --help     Show this message and exit.

Commands:
  delete      Deletes objects from the Databricks...
  export      Exports a file from the Databricks workspace...
  export_dir  Recursively exports a directory from the...
  import      Imports a file from local to the Databricks...
  import_dir  Recursively imports a directory from local to...
  list        List objects in the Databricks Workspace
  ls          List objects in the Databricks Workspace
  mkdirs      Make directories in the Databricks Workspace.
  rm          Deletes objects from the Databricks...
```
1. configure databricks notebook to run on more (or all) data in your target bucket 
1. output results from all data to target bucket
1. connect power bi to target bucket 

**known limitations**
- local development limits compute and storage and has general security concerns
- while version control exists, capacity to ensure model reproducibility and limit version conflict is low
- PII is vulnerable  
- no drift and model performance monitoring 
- limited reproducibility
- running arbitrary code that is not a part of a Spark job on the remote cluster.
- no drift and model performance monitoring 

**notes**
- [databricks connect, preferred ide setup instructions](https://docs.databricks.com/dev-tools/databricks-connect.html)
* as of the time of this writing, the jupyterlab integration with databricks-connect did not support windows machines

_____________________________________________________________________  
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
* aws ec2 - distributed compute      
* power bi - viz
* python - language of choice
* github - version control, source of truth 
* databricks connect + cli - hosting, compute, scheduling
* s3 - blob storage 
* power bi - visualization 


**flow** 
1. create repo / directory structure

 
**known limitations**
- no internet... no development 
- 


### useful links 
* [tableau docs](https://www.tableau.com/about/blog/2017/5/connect-your-s3-data-amazon-athena-connector-tableau-103-71105)
* [parquet overview](https://databricks.com/glossary/what-is-parquet)
* [azure + aws services comparison](https://docs.microsoft.com/en-us/azure/architecture/aws-professional/services) 
* [simple ml model deployment](https://towardsdatascience.com/simple-way-to-deploy-machine-learning-models-to-cloud-fd58b771fdcf)
* suggested [aws + docker prod workflow](https://towardsdatascience.com/ml-models-prototype-to-production-6bfe47973123) by [@Shreya](https://towardsdatascience.com/@shreyaghelani)
* [ml ops key challenges](https://github.com/microsoft/MLOps)
* [jupyter bridges local & remote workflows with databricks](https://databricks.com/blog/2019/12/03/jupyterlab-databricks-integration-bridge-local-and-remote-workflows.html)


