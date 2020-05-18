---
layout: post
title: analytics application architectures - part 1
excerpt: "a quick & easy application architectures for building, deploying and maintaining ml models"
categories: [s3,powerbi,dask,python,tools,data-engineering, ml]
modified: 2020-03-29
comments: true
---

## overview
* How do you select which model to use? 
* Once selected, how do you design the *right* architeceture? 
* How do you manage the deployment of the models? 
* Who owns the model prior to deployment? After deployment? 

More importantly, how do I build an application powered by machine learning? This quetsion, among others, need answers before determining the best fit analytics solution for your business scenario. Before we get to building, it helps to understand what tools we have to work with. Looking at how cloud service providers categorize their technology can give us a quick overview of technologies both related to, and outside of, the machine learning space. I will get a little meta and add that communities are more effective and effecient innovators, compared to their corporate counterparts. Because of this fact, it is common for AWS / Azure / GCP et al to [capitalize on the open-source market](https://www.geekwire.com/2019/elasticsearch-distribution-amazon-web-services-sends-shockwaves-open-source-software/). Ergo there is *probably* an open source alternative to the cloud service providers' prodcts I am going to list. There is a lot to consider when selecting a tool, but I will do my best to recommend open-source projects where possible. I digress. Cloud products fall into a few categories: 

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

### preview 
| provider  | tool   | category  |description | cost_model | integration | modeling | ... | 
| ------------- |:-------------:| -----:|-----:|-------:|-----:|-----:|-----:|
| fivetran     | fivetran_standard | migration | etl service for various apps | pay as you go | 1 | 0
| aws  | sagemaker | modelng | model management service | pay as you go | 0 | 1
| MIT License  | jenkins | integration | ci/cd service | free | 0 | 0

Now that we have a good idea of the tools that are available, we can being to define out service requirements. Hakon does a good job (1) clarifying the learning and prediction contstraints and (2) describing various business scenarios in [this quora post](https://www.quora.com/How-do-you-take-a-machine-learning-model-to-production/answer/H%C3%A5kon-Hapnes-Strand). 

![mlops framework](/img/mlops_framework.PNG)

Worth noting how Hakon breaks down learning into (1) offline and (2) online training and prediction into (1) batch and (2) on-demand. I'll add that 'on-demand' could be quantified to a specific service level agreement. E.g. from point new data enters source, serve results in less than time t. Constrains, such as team skill set, may also shapre your optimum service requirements. Can your data engineers productionalize a jupyter notebook? Can they seamlessly convert python to scala? Or do your data scientist need to deploy their own work? Do you have a machine learning engineer? Etc. 

Once service requirements and constraints are defined, we can break our analytics project - one where we need to build and productionalize a model and surface its results - into nine (9) components and begin selecting our ideal toolset:

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
_____________________________
### part 1 - quick and easy analytics
**overview**
how do I get this up and running as quickly as possible? 
* python + [dask](https://docs.dask.org/en/latest/)  - language of choice
* github - version control, source of truth 
* s3 - blob storage (model input + output)
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

**cababilities**
* process large data sets 
* version control 
* virtually unlimited storage 
* begin generating insights
* get people excited about "the possible" 

**limitations**
* while version control exists for code, data version control does not exist 
* limited reproducibility
* low data sciecne capacity; inability to iterate on models quickly 
* PII is vulnerable   
* manual updates required to update dashboard 
* no drift and model performance monitoring 
