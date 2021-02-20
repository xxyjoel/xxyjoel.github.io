---
layout: post
title: cloud gaming on aws
excerpt: "when your laptop doesnt cut it... provision a g3s.xlarge for its NVIDIA Telsa M60 of course"
categories: [aws, ec2, musings]
modified: 2021-02-20
comments: true
---

## overview
A game recently came out - Valhiem - and I wanted to play it with a friend. Given the game is in beta (and doesnt support MacOS) I decided I would set up an instance with windows configured and install steam there. This brief walk-through is for causual gamers and mac users with decent internet connection who want to play steampowered games. 

## steps 
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