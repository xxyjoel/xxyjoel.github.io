---
layout: post
title: postgres + docker + windows
excerpt: "setting up a [mostly] persistent cost effective and scalable storage option"
categories: [data-engineering, analytics, architecture, windows, postgres, docker]
comments: true 
---
## overview
Sample datasets are great for testing *models*, but if you want to test *theories* you need to gather data of your own. Collection methods range from open apis to web scraping (now legal in the us). While we will mention these collection methods, my primary focus is to determine the most cost effective way of storing and scaling the data needed to test your theories and hypothesis. While there are many guides written, none fit my specific use case and none walked you through the entire process, start to finish. 

## tenets 
1. fast is better than perfect - we dont care about optimum future structure; innovation is iterative 
2. reduce complexity where possible - simple solutions are better than complex ones

## assumptions
* you have a general idea of what you want to test or the topic your are interested in
* you have a list of sources you beleive will have valuable data necessary for proving you theories 
* you are unsure how much data you will need at what time
* you are unsure how many read and write operations you will need to execute
* you want to have the *option* to scale, should you need it 
* prior to determining the value of the data collected, you want to minimize costs 
* initial data format (from source) expected: json, key / value
* ideal data format for analysis: tabular, relational   

## approach
Given our assumptions, postgres seems like a good place to start. It is open source and easily portable to a cloud provider should you need to scale beyond your local machine. As an intermidiary step, should you need to increase the size of your storage on your local machine and avoid paying exorbitant fees, I might try using google drive. While you will likely run into performance issues, you can get (as of the time of this writing) 30 TB for 299/mo. This includes unlimited reads / writes. 

### setup 
### docker & choco
**installation**
We will be dockerizing our application. [Choco](https://chocolatey.org/packages?q=docker) has the most recent version of [docker desktop](https://chocolatey.org/packages/docker-desktop/2.2.0.4), which you can install by opening your cli (I prefer powershell core) and running the command:
```
choco install docker-desktop
```

You can check your system information by opening your cli and running the command:
```
systeminfo.exe
``` 

NOTE:
The current version Docker requires windows 10 pro as other versions do not have Hyper-V enabled. If you would like to install Docker and do not want to upgrade to windows 10 pro, you can can install the legacy desktop solution for windows, the [docker toolbox](https://docs.docker.com/toolbox/toolbox_install_windows/) wich "provides a way to use Docker on Windows systems that do not meet minimal system requirements for the [Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/)." 

*from docker documentation*
Docker Toolbox (legacy) includes the following Docker tools:
* Docker CLI client for running Docker Engine to create images and containers
* Docker Machine so you can run Docker Engine commands from Windows terminals
* Docker Compose for running the docker-compose command
* Kitematic, the Docker GUI
* the Docker QuickStart shell preconfigured for a Docker command-line environment
* Oracle VM VirtualBox

Really great blog post by Stefan Scherer on the topic [here](https://stefanscherer.github.io/how-to-install-docker-the-chocolatey-way/)

NOTE: 
You may also need to enable virtualization on your device. Open powershell and run the system info command again:
```
systeminfo.exe
``` 

OUTPUT
```
Hyper-V Requirements:      VM Monitor Mode Extensions: Yes
                           Virtualization Enabled In Firmware: No
                           Second Level Address Translation: Yes
                           Data Execution Prevention Available: Yes
```
To alter this setting we need to go into bios. How to get into bios differs by manufacturer, so google it if you need help. 

Once installed, hit the windows key and search Docker Desktop. You may need to restart your computer post choco download for the changes to take effect. 

Create a docker hub account and you are good to go. 

### postgres image
Now that docker is installed, we can find the [postgres image](https://hub.docker.com/_/postgres) on docker hub. Open your cli and type:
```
docker pull postgres
```

OUTPUT
```
Using default tag: latest
latest: Pulling from library/postgres
77fc17f10df0: Pull complete                                                                                             59f4081d08e6: Pull complete                                                                                             77fc17f10df0: Pull complete                                                                                             77fc17f10df0: Pull complete                                                                                             77fc17f10df0: Pull complete                                                                                             77fc17f10df0: Pull complete                                                                                             77fc17f10df0: Pull complete                                                                                             77fc17f10df0: Pull complete                                                                                             77fc17f10df0: Pull complete                                                                                             1a8434055eed: Pull complete                                                                                             77fc17f10df0: Pull complete                                                                                             77fc17f10df0: Pull complete                                                                                             77fc17f10df0: Pull complete                                                                                             77fc17f10df0: Pull complete                                                                                             Digest: 77fc17f10df082677fc17f10df082662d77f10df062d7b27ceea777fc17f10df0
Status: Downloaded newer image for postgres:latest
docker.io/library/postgres:latest
```
WARNING: [Problem with this approach](https://elanderson.net/2018/02/setup-postgresql-on-windows-with-docker/) is if you ever need to rebuild the container for some reason, e.g. a new version of Postgres is released, your data (users, databases and other objects) will be lost. Create a directory to serve as the mountpoint for postgres datafiles. Enter the following command in your cli

```
mkdir -p $HOME/[my]/[preferred]/[location]/docker/volumes/postgres
```

Start up a postgres instance 

```
docker run --rm   --name pg-docker -e POSTGRES_PASSWORD=docker -d -p 5432:5432 -v $HOME/docker/volumes/postgres:/var/lib/postgresql/data  postgres
```











```
#docker run -p 5432:5432 --name yourContainerName -e POSTGRES_PASSWORD=yourPassword -d postgres
docker run --name some-postgres -e POSTGRES_PASSWORD=mysecretpassword -d postgres
```


```
docker create -v /var/lib/postgresql/data --name PostgresData alpine
```



NOTE: if you would like to interact with your containers from your ide, visual studio code has a [docker extension](https://github.com/microsoft/vscode-docker) that works great. 
