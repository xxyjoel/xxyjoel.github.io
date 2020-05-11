---
layout: post
title: data quality checklist
excerpt: "collection of items to review when assessing data quality of a RDBMS"
categories: [data-governance, data-quality, rdbms]
comments: true
---

## overview
while sifting through reddit's [r/datascience](https://www.reddit.com/r/datascience/) I came across [this post](https://www.reddit.com/r/datascience/comments/fzweaf/my_giant_data_quality_checklist/?utm_source=share&utm_medium=ios_app&utm_name=iossmf) by [TB_R](https://www.reddit.com/user/TB_R/) and really liked what he had put together; I have added a few additional checklist items included by myself and redditors in the comment feed. 

## list
### Chapter 1 : 
**General Structure**
* Is there an established map of the database?
* Is it visible to all users?
* Is there a public strategy of how it is tested for truth?
* Are there known party(ies) responsible for updating it when there are structural changes?
* How often is it updated?
* When is the next scheduled update?
* Is there a change log to track prior changes?
* Is there a group of humans you can go to with questions?
* Is there a group of humans you can go to with general conversational topics?
* The user list from a DBA is great for this.
* Consider having a monthly meeting and yearly summit per database. Remember, this is YOUR responsibility. Not the owner of the database.
* If the source of data is an application or form, is there a map from every entry field to the corresponding field in the database?
* Capacity / Uptime
* How concerned should you be with locking up the database with poorly written queries?
* Who do you go to when it is dead?
* Is the data backed up / archived anywhere?
* For how long?
* Do you remove old values from the production database after archiving?
* Are queries logged?
* How often is the database refreshed, if it is part of an ETL?
* Automatically check, at the appropriate time, if it has been updated. Have a plan in place for when it is inevitably not updated.
* What is the official way to request access to the database?
* Is there a time-out process of old user ID’s?
* Have a plan in place for when an automated procedure’s stored credentials are suddenly invalid.
* Privileges per user or role.
* Staging strategy: DEV, ITU, PROD.
* Which RDBMS am I using: Mysql (which fork), PostgreSQL, Oracle.
* Which version am I using: semver, Docker image label, etc...?
* Tables, Views, Materialized Views, JSON support.
* Can I scale it properly?
* High availability mode.
* Proper JDBC support.
* How much does it cost: licensing model, requirements, etc...?
* UTC as my default timezone: SAP Walldorf, Zeitzone Europe/Berlin (UTC+1) I'm looking at you.
* Does my database solve the "Year 2038 problem"?
* Does my database has some quirks?

**Tables**
* Is there any rhyme or reason to the naming of tables?
* If there are very similar tables (ie. CUSTOMER, CUSTOMER_NEW, CUSTOMER_NEW_NEW), do the alternates need to exist?
* Is this table “one row that is constantly kept updated” or “one row for each time the data changes”?

**Joins**
* Should there be at least one “Child” for every “Parent”?
* Is there a maximum expected “Children” for every “Parent”?
* Should there be “Children” who don’t have “Parents”?
* Is every table relatable to every other table?

**On joining fields**
* Do the field types match, or do you need to do a conversion?
* Queries / Processes
* Is the query properly notated?
* With the code removed, the notes should be copy and paste-able to a non-technical business partner.
* The notes should represent reality. Please.
* Is it possible to do an “anti-query”?
* Reverse your filters, but mirror your production process. In high value pipelines, this can be a huge preventative measure for errors, and an easy way to provide “free” extra value.
* Is it possible to do the query by hand on a small subset of records?
* If so, do this periodically. Seriously. I know you got into this line of business to not do this stuff manually, but doing this periodically can help verify all systems are working properly.
* Is the process entirely automated?
* If not, don’t launch.
* This includes any of the tests below that say “periodically”, if possible.
* Is it possible to store the results of every query, along with a timestamp of run (especially as part of an ETL process?)
* Are you choosing a “whitelist” approach, or a “blacklist” approach?
* If there are changes after launch — will you be applying those changes to previous projects, or leaving them in place with old (and potentially inaccurate) data?

### Chapter 2 : Data Types
**All / General**
* Are null values allowed?
* Are the fields expected to change over time? If the answer is “no”, take hashes of lines at the time of development and test periodically to see if they change. If the answer is “yes”, take extracts with timestamps in the name and only use those (instead of production) for reproducibility.
* *Expert level* — Create a X / Y grid of every field against every other field. Have all parties involved in development (especially the humans that are the source of the data) write assumptions and rules that come up in each cell (if this field is “beer” this other field should be “$5.99”) Document all of these. If something looks wrong and the customer says “that’s ok”, DOCUMENT THIS. With audio evidence if possible. Video isn’t bad either.

**General Text (Free entry)**
* If there is a default size, why is the default size set to the size it is?
* Is the field having an identity crisis — does it only contain integers, dates, locations, etc.? If so — should it be converted to the correct type? What do you do with those that can’t be converted?
* Does the text contain invisible characters that will cause it to print strangely (line-break)?
* Does the text contain special (or international) characters? Should it? If you are planning on exporting to .csv downstream, are you removing commas upstream (or tabs, or pipes, etc.).
* Is there a list of “stop words” that should be applied? Especially inappropriate words. Don’t let an f-bomb get into a shareholder report.

**Categorical Text (Unique list of values)**
* Store the unique values at the time you are developing, and the counts of each value. Periodically test for new/missing values. Have a plan in place for when this inevitably happens.
* Periodically test for wild changes in distribution. Communicate these to anyone who will listen. This will make you a hero. Provide this list to stakeholders during development. This is always interesting, because often it is vastly different than their estimate.
* If there are multiple categorical fields, is there an hierarchy that is documented and should be followed (if veh_type is “car”, then veh_brand can only be “audi”, “ford”, “toyota” etc.). Have a plan in place for when this inevitably breaks.
* Is the field indexed for fast grouping?
* If the values are text numbers (one, two, three) — should they be converted to integers (1, 2, 3)?

**Boolean (True/False)**
* If these represent a “switch” in the process, should the downstream impact also be represented in data? (if override_price=0 then discount_price should never be null)
* Store the distribution at the time of developing, and periodically test for changes. Communicate these to anyone who will listen.
* Does True or False have a different name to you customers? IE, 0 = No Sale, 1 = Sale.
* What should you do with nulls, if there are any?

**ID (Keys, AutoNum)**
* Is the ID an AutoNum? If so, are there missing values?
* Is the ID a complex key? Do characters in the complex key relate to the data in any way? Periodically test that this remains true.
* Are duplicates allowed?
* Are you operating under the assumption that the ID for any given record will never change (maintaining an extract or join to a private table)? Have a plan in place for when they inevitably do.
* If you remove the ID from the equation, are there any two completely identical rows?

**General Number/Integer**
* Does null = 0?
* Are negative values expected?
* Are decimals expected? If not, is there rounding occurring upstream? What are the rounding rules?
* Is there a maximum/minimum expected value? Record the minimum and maximum value at the time of development. Periodically test to see if this record has been broken.
* Is there an expected distribution? Periodically test to see if this has changed.
* Is the number secretly categorical (only certain allowed values)?
* Does the number increase in steps instead of linearly (100, 150, 200, 250…)?
* Is the number secretly a phone number or postal code?
* Is there any chance that the number has been cut off to fit in a smaller field (I’m looking at you, Smallint)?
* Is any number field the result of math on other number fields (price*tax=total)?
* Is there a business preferred format for showing numbers (commas, negative signs)?

**Currency**
* Is there an assumed currency? If not, is there a categorical field that will tell you what currency it is?
* If the record also has a “country” — does the currency match the country?
* Are you converting these currencies to a single currency downstream? If so, store the conversion rates at the time of development.
* Is there a maximum sane currency? Record the maximum and test frequently. Have a plan in place for when it is inevitably broken.
* Does null = 0?
* Does the value represent a “current” value, or a “point in time” value (current price of product, price of product between 2018 and 2019)?
* Are there negative values? Does negative represent a debit or a credit to the company?

**General Contact Information**
* Is there a schedule for updating contact information?
* When was the information last updated?
* When was the information last successfully used?
* If you use this information for any purpose, store the information at the time of use, and the reason for use. If possible, get as many employees as possible to do this in the same location using the same format.
* Do any two customers have the same piece of contact information?
* Does any contact information match your own company/office information?
* Does any customer’s information match that of a current or former employee?
* Is there a no-marketing list that you should be aware of? Ask multiple people and get excited when there are multiple answers!

**Phone Numbers**
* Is there an assumed country code? If not, is the country code stored in another field?
* Do not assume the country of residence is the country code of the phone number.
* Does the phone number follow the rules of that country code?
* Is there an assumed area code?
* Is the area code a toll free area code?
* Is the area code a legitimate area code?
* Is the phone number stored as text?
* Is there any rhyme or reason to the formatting of the phone number? If it is text, is there a single format? What do you do with numbers that do not match that format?
* Is there a preferred format for displaying phone numbers?
* Are you removing “bogus” phone numbers (All the same digits, counting up, etc.).
* If there is a dialer campaign that returns invalid phone numbers, is anything being done with that information?

**Mailing Address**
* Is there an assumed country? If not, is the country stored in another field?
* Is the address free entry text, or cleaned via a service?
* If it is cleaned, what happens to un-cleanable records?
* Is the Postal Code -> State/Region -> Country strictly enforced?
* Is the mailing address in an expected area of business?
* Is there an additional “APT” or “PO BOX” field? Should you be aware of this?
* If there is a latitude/longitude field, does it match the address? Do many customers have the same latitude/longitude?

**Sensitive PII**
* Are these fields humanely appropriate for modeling or analysis (ethnicity, income)?
* Do you need to be aware of HIPAA guidelines?

**Financial Information**
* God help you if your company is storing unmasked financial information.
* Did two different customers make a transaction with the same credit card? Or the same last four digits and expiration date?
* See what your most popular card type is and and provide this to your marketing department if there is a clear winner, for use with reward programs.

**Date / Time**
* Is this a system recorded timestamp field, or human entry?
* Is the time being stored with an assumed time zone?
* Store the minimum date-time during development. Periodically check to see if this has changed.
* Are there any dates with missing information? For example, there are sales every day of February except the 12th.
* Are there records outside business hours?
* Are there any fields that look like they were entered before their timestamp (people trying to create records in the past)?
* Are there any dates that are in the future?
* Do you need to have a specific game plan for leap years?
* Do you need to have a specific game plan for holidays?
* When counting records by day or hour, are there any outliers?Communicate this to stakeholders as soon as possible. Check this frequently — it’s valuable information and will make you famous when you catch something. Additionally, it will ruin your model if there was a rare event.
* If there are multiple date fields, should they have an order of operations (field2 should always be after field1).
* Does everyone know what “biweekly” means?
* Does everyone know what “midnight” means?
* Is there a business preferred date format?
* For forecasting purposes - keep a separate table telling you what dates to NOT take into account when training forecast models. IE - Hurricanes, marketing campaigns, giant international pandemics.
* Do you have a plan for daylight savings time?