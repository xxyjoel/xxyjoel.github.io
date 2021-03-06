---
layout: post
title: fantasy football workload prediction theory
excerpt: "suggested improvements on the current player projected point scoring mechanism."
categories: [musings, football, theory]
comments: true
---
### Overview
Having played fantasy for half a decade (ppr), I started to wonder if there was a more effective mechanism at predicting player scores. The most valuable players in ppr leagues are your 3rd down backs, and when looking at workload comparisons they take the cake. It stands to reason that workload rates are a strong indication of total points scored. 

To have something to compare against, I want to breakdown the current ‘projected score’ system. I will be referring to Yahoo Fantasy, but having played ESPN and used Fantasy Pros et al I am making the assumption scoring mechanisms only differ slightly. If this is incorrect, call it out in the comments below. 

We have three metrics we look at to assess draft & sit/start opportunities:
* Player projected score – This is linear (we know this as projected points increase or decrease linearly over future games when a player over or underperforms point projections for current game, respectively) and based on historical completion rates (we know this as the breakdown is listed in the player game profile; e.g. 44 completed yrds, 1 td)
* General position matchup – This is a simple ‘red / yellow / green’ status that ranks the general player position (not the player himself) against the opposing team; i.e. historically, how well have WRs performed against Team X. This should be used as a probability metric to determine how likely it is the player will under or over perform the player projected score. 
* Player matchup – this is a simple star metric (1-5) that is determined by the players relative projected points, compared to other like positions. This is an assumption and needs to be tested. Look at projected points to star ratings. If inverse correlation or no correlation exist, then reject hypothesis and accept null. 

To make the effort more attractive, intuition tells us workload would be easier to predict than completion rates – rush yards, completed passes, touch downs, etc – which is the current basis for projected player scores. 

Potential factors, not currently included in projections, at play in determining estimated player workload:
* Weather – If a pass-catcher or a kicker, weather will likely adversely affect completion rates and cause in increase in run plays reducing total workload. 
* Opposing defense – Good corners will box out star players, centers prevent runs, etc. 
* Offensive line – They create runs, prevent sacks, and more; often overlooked, I am of the belief offensive lines are critical to both run and pass play success (or failure…think McCaffery week 2…). They create openings for running backs and give generous time to the quarterback. 

It is inherently difficult to measure much of their performance, and an entire analysis could be done on team performance as a result of the o-line. 

EDA is needed to vet assumptions. Further iterations would look at player to player matchups, team and coach composition. Momentum and the network effect are real; it is up to us to figure out how to measure it.

**UPDATE**
2020-03-01: 
* general workload prediction theory lead to a win in my work league 8 people (i know i know...) + 2 round finals with winner of additional 8 man league 

summary of general theory formula:  
* est. total snaps by qb * est. pct of snaps by player * avg. points per snap by player

noted improvements & future hypothesis:   
* rbs received expected snap values when game was close in score (+/- 8) and game clock was early (< 4th qtr) 
* rbs received more than expected snap values at lower point values when rb team had large lead and game clock was late (4th qtr)
* wrs received expected snap values when game was close in score (+/- 8) and game clock was early (< 4th qtr) 
* wrs received more than expected snap values at higher point values when wr team had large deficit and game clock was late (4th qtr)
* when calculating est pct of snaps by player, skill of player relative to skill of group and relative to qb passing / running ability should be taken into effect 

**NOTE**
* interchangable positions (like passing te & wr) on certain formations are not accounted for