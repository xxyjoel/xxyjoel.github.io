---
layout: post
title: analytics application architectures - part 2
excerpt: "sclable application architectures for building, deploying and maintaining ml models"
categories: [s3,powerbi,dask,python,tools,data-engineering,databricks,ml]
modified: 2020-04-17
comments: true
---
## overview 
In the [first post](https://xxyjoel.github.io/articles/2020-03/deploying-analytics-apps-1), we covered a light-weight architecture to enable data scientists to process large data sets and publish results on an explorable dashboard.  In part 2, we are going to focus how to scale analytics at organizations with more data engineers than scientist.  

### recalling part 1 limitations
* while version control exists for code, data version control does not exist 
* limited reproducibility
* low data sciecne capacity; inability to iterate on models quickly 
* PII is vulnerable   
* manual updates required to update dashboard 
* no drift and model performance monitoring 

### #part 2 - scaling operations
**overview**
While using the same tech stack, additional features such as job scheduling, automated deployment and the databricks cli can improve our workflow. While i was not able to get it working in a reasonbile amount of time on a window machine, jupyterlab integration is another potential option at this stage. Content pulled from [these docs](https://databricks.com/blog/2019/12/03/jupyterlab-databricks-integration-bridge-local-and-remote-workflows.html) can help walk you through the process. Note, there are potential security concerns with the jupyterlab integration. 

* python + [dask](https://docs.dask.org/en/latest/) - language of choice; package for improving compute 
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
12. configure databricks notebook to run on more (or all) data in your target bucket 
1. output results from all data to target bucket
1. connect power bi to target bucket 

**imrovements to "#1"**
* databricks adds spark; written in scala and existing support for python, r, sql and jvm based infra
* organizations with access to more data engineer talent than data scientist talen will be able to support medium to medium-large scale operations
* databricks integrates well with ml flow, azure and aws services 
* ensuring model reproducibility is possible 
* schedules can easily be set via a GUI (this can be done in part 1 using dask with certain limitations)

**known limitations**
* ml ci/cd is not present, duplicated deployment efforts (to databrcks and to github)
* databricks and local enviroment development differ if scientists are using pyhton or r; productionalized files will likely need to be reconfigured
* PII is vulnerable on local development environment  
* no drift and model performance monitoring 

**notes**
* [databricks connect, preferred ide setup instructions](https://docs.databricks.com/dev-tools/databricks-connect.html)  
* as of the time of this writing, the jupyterlab integration with databricks-connect did not support windows machines
* additional information on dask + spark comparison