---
layout: post
title: python, windows, and ec2 
excerpt: "configuring a python environment on ec2 with a windows machine"
categories: [s3,ec2,windows,ssh,python,powershell,vscode]
modified: 2020-05-12
comments: true
---

## overview 
How to setup and configure an ec2 instance with python using a windows machine. While miniconda / anaconda can be greate for some, I have since moved away from using the environment; version hierarchy was difficult to manage between preinstalled conda packages and newly desired packages.   

### connecting to ec2 from vs code 
* for a more detailed description, [follow this guide from aws](http://bw4sz.github.io/ec2/#v.-accessing-an-amazon-ec2-instance-in-the-browser) 
* log into your aws console and navigate to the ec2 service
* launch a **linux ami instance** and ensure a public DNS or IP is available
* save your api key in a known location
* open vs code and download the [remote ssh](https://code.visualstudio.com/remote-tutorials/ssh/getting-started) extension. once installed you should see a status bar in the bottom left corner of your screen 
* when in vs code, hit ctrl + shift + P and search for ```remote ssh: connect to host```
* you should see two options; select ```configure ssh hosts```
    * add new ssh host or
    * configure ssh hosts 
* you should see a few options for config file locations; I selected the first option 
```
C:\Users\<myusername>\.ssh\config
```
* open your config file and provide the following infomation 
```
Host <name you will use to identify your connection>
    HostName <public dns>
    User <ec2-user>
    IdentityFile <"location/of/your/.pem/file"/in/quotes>
```
* save your config file. hit ctrl + shift + P and search for ```remote ssh: connect to host```
* you should now see your ```Host``` name specified in your config file listed as a potential option in your remote ssh connections 
    * note: you will need to ensure the cluster is running in your aws console
* in vs code, select the "><" box in the lower left-hand corner
* this will prompt you to connect to your ec2 using your recently configured config file
* NOTE: if you used a private IP address, you will need a configured VPN with credentials specific to the security group designated to the ec2 instance you setup

### installing repo & dependencies
* for a more detailed description, [follow this guide from github](https://github.com/yafangy/Tutorial-using-Amazon-AWS-EC2-run-scripts-GitHub/tree/master#method-2-clone-git-to-ec2-instance-recommended) 
* once connected to your instance, select "clone repository' via https in vs code to connect to your project 
    * vs code will request authentication with github 
    * note: your instance will likely come with python 2.7 preinstalled. it is possible there are instance dependencies on 2.7, so it is recommended you install an updated python version within your virtual environment 
    * firefox (as of the time of this writing) is not compatible with the vs code git connector; change your default browser if required 
* set up your virtual environment using pipenv
* list all your (1) required packages in your requirements file and (2) your desired extensions 
* if you are using vs code with the python extension, you can startup an interactive notebook (think jupyter) with a ``` # %%``` command

