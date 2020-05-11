---
layout: post
title: analytics application architectures - part 3
excerpt: "sclablable application architectures with automated deployment workflows"
categories: [s3,ec2,powerbi,dask,python,tools,data-engineering,databricks,ml,mlflow, github actions]
modified: 2020-04-17
comments: true
---
## overview 
In the [second post](https://xxyjoel.github.io/articles/2020-04/deploying-analytics-apps-2) we looked for ways to better integrate with spark data pipelines and improve model hosting capabilities. We introduced databricks, a 'unified platform for data and ai' and discussed how data scientists might interact with data engineers and the platform itself. In part 3 we will discuss how to (1) automate deployment worflows using that same toolset

### recalling part 2 limitations
* while version control exists for code, data version control does not exist 
* limited reproducibility
* low data sciecne capacity; inability to iterate on models quickly 
* PII is vulnerable   
* manual updates required to update dashboard 
* no drift and model performance monitoring 

### #part 3 - automating deployment
* python - language and model package source
* github - version control 
* s3 - distributed storage
* ec2 - distributed compute    
* dask - distributed compute, python abstraction
* databricks connect + cli - hosting, compute, scheduling
* mlflow - ml lifecycle management
* github actions - ci
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
12. configure databricks notebook to run on more (or all) data in your target bucket 
1. output results from all data to target bucket
1. connect power bi to target bucket 

**imrovements to "#2"**

**known limitations**
* scripts used in datascience environment differ from those used in the databricks environment 
    * while continuous integration is available, it is not a seemless transition from the python scripts to 
* no drift and model performance monitoring 

**notes**
* [databricks connect, preferred ide setup instructions](https://docs.databricks.com/dev-tools/databricks-connect.html)
* as of the time of this writing, the jupyterlab integration with databricks-connect did not support windows machines
