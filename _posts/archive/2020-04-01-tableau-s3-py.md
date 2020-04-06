---
layout: post
title: using s3 as your tableau data source 
excerpt: ""
categories: [aws,s3,tableau,tools,data-engineering]
modified: 2020-04-01
comments: true
---

## overview
Oversimplified an advanced analytics product has 3 main components - source(s), model(s), report(s). The I prefer to focus on the model development and, wanted to outline a system that enables rapid protyping of an end to end analytics solution - reducing the need for source configuration and report development. 

### suggested stack
* s3 - blob storage for your csv, parquet or json files
* athena - required for tableau integration with s3 
* databricks - model hosting, distributed compute 
* tableau desktop - visualization 

### flow 
1. gather data from datawarehouse et al (source)
2. store in s3
3. create workspace in databricks 
4. 

### sources 
* [tableau docs](https://www.tableau.com/about/blog/2017/5/connect-your-s3-data-amazon-athena-connector-tableau-103-71105)
* [parquet overview](https://databricks.com/glossary/what-is-parquet)


