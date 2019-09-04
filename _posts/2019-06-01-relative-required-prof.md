---
layout: post
title:  "Verticals in Data & Analytics"
excerpt: "Building an innovation lab and measuring relative value of a skill."
categories: [musings]
comments: true
images: 
    feature: /img/d&a-verticals.png
images: 
    feature: /img/d&a-verticals-viz.png
images:     
    feature: /img/d&a-verticals-infra.png
images: 
    feature: /img/d&a-verticals-decision.png
    
---

# Verticals in Data & Analytics 
<br>
<strong> Background </strong>
<br>
Given the Data & Analytics (DA) field is so new, I wanted to build a framework that could (1) improve my narrative (2) understand skill & tool requirements to master each DA vertical and (3) decide, based on my current skill sets and interests, where I can add the most value.
<br>
Initail assumptions
- Three specialization (verticals) exist: (1) decision - akin to advanced analytics work - (2) infrastructure - architecture, systems & networking - and (3) visualizaiton, data modeling, report development and requirements gathering.  
![verticals](/img/d&a-verticals.png)
![decision](/img/d&a-verticals-decision.png)
![infra](/img/d&a-verticals-infra.png)
![viz](/img/d&a-verticals-viz.png)
<br>
To start, I pulled data on DA roles of all levels to determine which skills and tools were required. While many roles require the same tool or skill (a business analyst and data scientist using python for example) they do not always require the same skill level. A 'required proficiency' value was created to differentiate. Admittedly this took a bit of finesse, but based on the variance (or consistency) of a specifc role requiring the same skill, you could infer the skills' importance or 'required proficiency'. 
<br>
<br>
[instert role & skill matrix]<br>
[insert required proficiency calculation]<br>
[insert required prof. distribution]<br>
<br>
<br>
To add to the complexity, we may run into an exception where a BA role has referenced python as a required skill 10/10 times. The same occurs with a DS. However, it stands to reason based on median pay and open position qty that the level or proficiency of the python scripting skill required is far greater for the DS than the BA. To combat this we created a relative required proficiency (RRP) using factors such as total quantity of skills required per role, average pay, median pay and total positions open.    
<br>
<br>
[insert rrp calculation]<br>
[insert new role and skill matrix w/rrp values]<br>
<br>
<br>
TODO
- how my skill sets compare 
- most valuable skills (not roles)
- forecast skill valuation (to make case for where you should be spending your time 
- how companies can leverage innovation labs (and view it as a cost saving revenue generation machine)
- https://github.com/indeedeng/imhotep
- https://opensource.indeedeng.io/
- https://jobs.github.com/api
    - determine how to parse skills from text blob 
    - possible to hardcode list of all tools and technologies and search for key words
