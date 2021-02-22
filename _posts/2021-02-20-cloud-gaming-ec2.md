---
layout: post
title: cloud gaming on aws
excerpt: "when your laptop doesnt cut it... provision a g3s.xlarge for its NVIDIA Telsa M60 of course"
categories: [aws, azure, ec2, vms, musings]
modified: 2021-02-20
comments: true
---

## overview
A game recently came out - Valhiem - and I wanted to play it with a friend. Given the game is in beta (and doesnt support MacOS) I decided I would set up an instance with windows configured and install steam there. This brief walk-through is for causual gamers and mac users with decent internet connection who want to play steampowered games. 

## notes
- I initially went through this process and was (1) unimpressed with the performance, (2) frustrated with the lack of transparency in the supoprted regions, and (3) frustrated that I had to get approval for vCPU increases in each of the supported regions. This led to unnecessary conversations with suppport.  
- going through this process highlighted some clear knowledge gaps, as I found myself asking questions like "why disable hyper-v" and "what does parsec offer that RDP (or teamsviewer) doesnt? is that the cause of the poor performance? 
## conclusion ##
 I recommend this setup for those of use who are indeed casual gamers; you enjoy *ocassionally* playng with your frieds, but dont want to shell out 1500 bucks for a rig (or even 500 on a (very) dedicated potato). To put it in a formula you robots can understand, assuming you'd need to upgrade your rig (~1500) every 3 years, that means you budget ~500 a year for the hardware. This setup will run you ~$0.80/hr. So, if you game less than 625 hours a year (52 hours a month or 12 hours a week) then it is actually *more* cost effective to game virtually... COOOOOOL.    

## azure steps
1. sign into your azure account 
2. configure a resource group (something like "cloud gaming blah")
3. provision a virtual machine - aside from the required fields, you can leave all else as the default values
    * note: You will need to carefully determine which regions have the supported / recommended graphics card, NVIDIA's Tesla M60; I selected the US Central South as the NV6 was available under the non-standard category
    * re security groups and ip: for the purpose of this test, I made the vm public 
4. fire up your vm and connect via RDP - you can download the app from the app store and open the downloaded file from your azure dashboard 
5. install the latest nvidia driver (see sources below)
6. install teamsviewer and parsec (see sources below)
7. connect to the vm via teamsviewer 
8. close your rdp session
9. on your vm, go to your directory 
```
C:\Program Files\NVIDIA Corporation\NVSMI
```
10. run the following command 
```
nvidia-smi
```
11. *"This will get you a table that gives you both what mode your Tesla is set to (which will be TCC by default, check under the heading TCC/WDDM), and the GPU_ID, which is the thing under the Bus-Id heading"*
12. run 
```
nvidia-smi -g {GPU_ID} -dm 0
e.g. nvidia-smi -g X019:00:00.5 -dm 0  
```
13. restart if needed
14. navigate to your display devices in device manager, disable hyper-v
    * your text should now be tiny as the tesla gpu is enabled 
15. connect with parsec (and enable parsec on start)
16. download steam and your games!
* if you are having audio issues, I recommend you install vb-audio (link in sources)


## aws steps 
1. configure your ami - I selected the windows 2019 base container  
2. as for your instance size, select the g3s.xlarge for its NVIDIA Telsa M60
3. if you want to make it a bit cheaper, you can select the spot pricing option, set a maximum price close to the on demand price (0.409 at the time of this writing for my region) and check "persistent request"
4. next, up your storage to ~100 (or whatever space your game(s) are going to need)
5. security - to make things easy (and because the instance will primarily be in a terminated or deleted state), i allowed all IP addresses 
6. create or select an existing key pair 
7. download windows remote desktop connection from the app store
8. connect to your pc!   
9. note: if you do not know your windows password or user name, you can decrypy your pem key in the aws console. go to ec2 > instances > your instance > get windows password under the "security" menu item.
10. create a new passowrd in Local Users & Groups if youd like an easier to remember login
11. next, open Server Manager and disable IE Enhanced Security (top right hand corner) for admins and regular users 
12. use internet explorer to download google chrome
13. once installed you’re going to want to download DirectX and the NVIDIA Tesla M60 drivers.
14. once thats installed, we need to run the following commands in cmd prompt to ensure the propper graphics card is used
```
takedown /f C:\Windows\System32\Drivers\BasicDisplay.sys
```
```
cacls C:\Windows\System32\Drivers\BasicDisplay.sys /G Administrator:F
```
```
del C:\Windows\System32\Drivers\BasicDisplay.sys
```

15. install razer surround drivers for game sound
16. install steam and youre good to go
* as noted above, i ran into some serious performance issues with this method  

![Valhiem game](/img/valhiem-azure-vm.PNG)

## sources 
* [wicked awesome azure guide](https://medium.com/azure-cloud/a-killer-guide-for-cloud-gaming-on-azure-march-2020-1aa56d13fba3)
* [a slightly outdated aws post](https://medium.com/@bmatcuk/gaming-on-amazon-s-ec2-83b178f47a34)
* [another slightly outdated aws post](https://lg.io/2015/07/05/revised-and-much-faster-run-your-own-highend-cloud-gaming-service-on-ec2.html)
* [main aws post used in the above](https://medium.com/tensoriot/cloud-gaming-on-amazon-web-services-4be806c0051b)
* [vb audio](https://vb-audio.com/Cable/)
* [teams viewer](https://www.teamviewer.com/en-us/)
* [parsec](https://parsec.app/)
* [nvidia driver](https://www.nvidia.com/Download/driverResults.aspx/158195/en-us)
* [tcc module mode docs](https://docs.nvidia.com/nsight-visual-studio-edition/reference/index.html#tesla-compute-cluster)