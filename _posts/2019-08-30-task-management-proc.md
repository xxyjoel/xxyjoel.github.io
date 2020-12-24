---
layout: post
title: task management procedure 
excerpt: "how i distill and manage tasks, requests and projects requirements from multiple sources / inputs."
categories: [musings]
comments: true
image:
  feature: /img/task%20management%20proc-application%20view%20.png
  feature: /img/notebook-sample3.JPG
  feature: /img/notebook-sample4.JPG
---
# Task Management Procs:
## Overview / Assessment of Procedure:

<strong>An effective task management procedure must</strong>
* collect information and tasks from more than 1 environment
* provide clear prioritization ability 
* capture any & all requests and 
* minimize record keeping effort required 

Collecting information from many environments means capturing requests via multiple email accounts, in person meetings, project management applications (JIRA, Azure DevOps, etc.) and your code base.  

Clear prioritization is required to narrow scope and increase focus potential. This will increase success rate of individual tasks and, in turn, deliverables themselves. 

Capturing all requests is required to maintain competence. While not all tasks must be completed by the individual, the individual must know when resources are constrained and tasks either (1) cannot be completed or (2) require additional resources. 

<strong>Additional thoughts around interaction</strong>
It is ideal that each application has 1 central repository for anything that is “task” related as not to confuse the user between applications. That is, if application A has 2 locations where tasks / lists are hidden, B has 5, C has 1, it is assumed that the probability of cohesion between the lists would be minimal. Thus, a 1:1 rule (1 list per application) is applied. 

![Procedure Image](/img/task%20management%20proc-application%20view%20.png)

### Notebook: 
This is the “central” task management tool. It is interacted with daily and contains tasks that should be completed within 24 hours. Additionally, notes and thoughts pertaining to tasks are included here. It is dated and paginated for reference, should it be required. 

<strong>Symbols used</strong> 
* A square box denotes a task to be completed in 24 hours
* A square box with a checkmark denotes a completed task
* A square box with a circle around it denotes a HIGH PRIORITY task to be completed in 24 hours 
* Items with a strikethrough line denote a task not completed, added back to digital task management, or added to current day’s list

<strong>Additional categorization</strong>  
* Content with rectangles around written items denote subjects. 

<strong>Common subjects include</strong>
* Tasks
* Personal thoughts & solutioning 
* Meeting notes 
* Date / time

<strong>Exceptions</strong>
* IF both the left and right pages are filled AND tasks still remain open THEN cross out and either add back to digital task management application OR add to current task management page 

![Notebook sample image 4](/img/notebook-sample4.JPG)
![Notebook sample image 3](/img/notebook-sample3.JPG)

### Digital Records
#### Google Tasks
This task management application is used for all future state task prioritization requirements. All individual tasks listed here should be small enough such that they can be completed within a 24-hour timeframe as they will eventually be added to the written notebook.

##### List types & details 
<strong>Tasks List</strong>
* For digitally tracking and prioritizing future tasks
* Tasks are created and prioritized based on date and time 
* Tasks can be added to be completed at any future date
  * New items that are to be completed same day should be added to the notebook 

<strong>Exceptions</strong>
* If the notebook is not available and the task(s) must be completed same day, you can add an item to the gtasks list 

<strong>Backlog List</strong>
* Projects / ideas I would like to do but do not have a specific completion timeframe outlined 
* Items are drawn from this bucket when there is “nothing left” on the standard task list. Purpose being to prevent stagnation.
* Items that have had a date but have been pushed back / rescheduled more than 3x are also listed here

#### Simple Note
I have found this to be the most reliable, easy to use, cross-device note taking application. Generally this is used when the notebook is not present. Lists are primarily utilized for their reference and search capability. This method does not have any date enforcement or prioritization capabilities. Its main purpose is to (1) record information in a formal meeting setting where writing cannot record sufficient information and (2) to record thoughts such as date ideas, books to read, travel destinations, etc.

* Yearly goals, both achievements and habitual changes, are listed here
* Requirements gathering notes are taken in simple note in the list “Meeting Notes”
  * Name of parties, data and subject are noted in the title 
  * “ – “ denotes a reference item 
  * “ * “ denotes a task to be transcribed / added to the task pipeline
* Discussion topics are listed in “Blog & post topic list” 

All other lists are self-explanatory and use a simple “ – “ per item.  
* Reading list 
* Tax deductions 
* Business ideas
* IOUs (to and from)
* Computer & profile setup
* Political reform ideas
* Bucket list
* Misc philosophical thoughts
* Data ideas
* Code notes (think notepad replacement for code related items)
* Workspace (think notepad replacement for non-code related items)

#### Project Specific Repo:
All tasks to be completed should be added to gtasks prior to exiting / pushing completed code. Context of the feature to be completed should be stored in the project readme, TODO section, code comments or project management application (e.g. JIRA). 

* Project & code notes are stored here
* Tasks transcribed from a project go no deeper than the product feature level (description, acceptance criteria, etc) 
* Further contextual details can be stored within the readme or code comments
* This context should NOT be a source for managing or prioritizing tasks against unrelated tasks 

<strong>Note:</strong> This location is not checked daily and tasks should be added to task management application (gtasks or notebook) prior to exiting repo or closing work 

#### Google Email 
Personal email account aggregation tool. Includes joel.proctor@outlook.com & xxyjoel@gmail.com. This account also has quick links to google calendar and google task management applications. All items starred are to be converted into task in gtasks.

<strong>Types of tasks generated from starred emails:</strong>
* action needed: turn into task in gtasks and prioritize immediately 
* response needed: set 5 day response follow up task. 
* information required for future event: add as task for day of event 

#### Outlook Email 
Work email account aggregation tool. As a consultant I often have 3+ work accounts active at a time. 

* Flags must be turned into tasks same day. 
* Tasks are prioritized in google tasks - tasks list

### Day to day procedure:
##### Determining which tasks to select for today 
* Review current unfinished task in the notebook from the prior day (if applicable). 
  * IF the task should move to backlog (gtasks)
    * Copy the item from paper to gtasks with appropriate priority date 
    * See “Backlogging and Rescheduling Tasks” below on how to determine when to reschedule a task, and when to backlog it
  * IF the task should move to today’s current task list. 
    * Cross out the previous days task
    * Rewrite the same task on todays task list
* Review unaddressed outlook and gmail items 
  * Add starred / flagged items to gtask list
* Review gtasks for items selected for current day 
  * IF tasks listed for today’s date in gtasks is still a top priority, add it to today’s task list (in notebook)
  * IF task listed for today’s data in gtasks is not a top priority, reprioritize. Actions to be taken can include:
    * Move to another date / time
    * Move to backlog
    * Delete

##### Completing a task
* Check off the task in the notebook when complete 
* Add any footnotes where required

##### Adding new tasks
* Tasks collected throughout the day can either be added digitally or via paper
  * IF the task is added digitally, add a description and date.
    * No further work is needed as the day to day procedure will catch the task when it is requiring completion
  * IF the task is added manually on paper, make sure to add context. 
    * No further work is needed as the day to day procedure will catch the task when it is requiring completion

##### Backlogging and rescheduling tasks
* IF a task has not been transcribed (i.e. added to the notebook) AND has not been completed
  * Reprioritize the task by assigning a new date 
* IF a task has been rescheduled more than twice, move item to backlog 

## Conclusion:
This procedure is optimum for persons who prefer minimal upkeep and maximum flexibility. While a specific and current daily focus with loose long-term prioritization grants the user peace of mind – in knowing all tasks are captured and priorities have been assigned – its shortcomings are apparent when too many requests (pertaining to many projects) exist. 

<strong>Potential solutions to the shortcomings of this procedure</strong>
* Ability to group tasks by type
  * This would require a new digital task management tool as google tasks have some serious limitations (e.g. no subtask date assignment, no tagging, etc.)
* Increased usage and reliance on project management software
  * by capturing detailed priorities within your prefered PM software, you can effectively and effeciently rely on this procedure for reminders, removing the requirement to duplicate detailed information 
  
  

