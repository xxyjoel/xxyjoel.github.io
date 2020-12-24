---
layout: post
title: rfm analysis core template  
excerpt: "sample of how to pull data, categorize and score customers based on their purchasing behavior."
categories: [code]
modified: 2014-09-14
comments: true
---
## overview 
Revenue and client quantity distributions are rarely the same. This scoring mechanism seeks to normalize customer behavior by assigning specific actions to newly labeled cohorts. While its origins date back to the 1930's, there are virtually limitless variations on this scoring mechanism and it remains a powerful tool that can be utilized in a wide variety of environments with limited data. 


```r
# REQUIRED PACKAGES
require(dplyr)
require(stats)
require(data.table)
require(RODBC) 
require(DBI)
require(RODBCext)

#PULL CONNECTION 
my_connection <- odbcDriverConnect('driver={SQL Server};server=SERVER;database=DATABASE;trusted_connection=true')

#sales order table needed for instead

sales <- sqlQuery(my_connection, '
select 
  Qty
, Amt
, CustomerDimKey
, DateDimKey
from 
  TABLE WITH DATA
group by 
  Qty
, Amt
, CustomerDimKey
, DateDimKey
                 ') 

sales_customer <- sqlQuery(my_connection, '
select 
  SUM(Qty) as Sales
, CustomerDimKey
                           
from 
  TABLE WITH DATA
group 
  by CustomerDimKey
                           
                 ') 

top_filter       <- 20
min_purchase_qty <- 100

#FILTER OUT TOP CUSTOMERS TO GET A LIST OF TARGET CUSTOMERS 
customers_top    <- sales_customer[order(-sales_customer$Sales), ]
customers_target <- customers_top[(top_filter + 1):(nrow(customers_top)),]

#FILTER OUT CUSOTMERS WHO HAVE NOT PURCHASED MORE THAN 5 ITEMS
customers_target <- customers_target[customers_target$Sales > min_purchase_qty,]

#FILTER TARGET CUSTOMERS IN TRANSACTION LIST 
sales_transactions <- sales %>% 
                        filter(CustomerDimKey %in% customers_target$CustomerDimKey)

#convert to date 
sales_transactions$DateDimKey <- as.character(sales_transactions$DateDimKey)
sales_transactions$DateDimKey <- as.Date(sales_transactions$DateDimKey, "%Y %m %d")

#filter out 0 value sales qty and group by date 
sales_transactions <- aggregate(.~ DateDimKey + CustomerDimKey, sales_transactions, sum)
sales_transactions <- sales_transactions[sales_transactions$Qty != 0,]

#locate first and last sold date by customer and merge to main transaction df
customer_first_sale <- aggregate(sales_transactions$DateDimKey, by=list(sales_transactions$CustomerDimKey), min)
  colnames(customer_first_sale) <- c("CustomerDimKey", "FirstSoldDate")
  
customer_last_sale  <- aggregate(sales_transactions$DateDimKey, by=list(sales_transactions$CustomerDimKey), max)
  colnames(customer_last_sale) <- c("CustomerDimKey", "LastSoldDate")

sales_transactions <- merge(sales_transactions, customer_first_sale, by = c("CustomerDimKey"))
sales_transactions <- merge(sales_transactions, customer_last_sale, by = c("CustomerDimKey"))

#calculate cumsum qty ordered
sales_transactions <- arrange(sales_transactions, 
             CustomerDimKey, 
             DateDimKey)

sales_transactions$PurchasedQtyCumSum <- as.vector(ave(sales_transactions$Qty, 
                                                    sales_transactions$CustomerDimKey, 
                                                    FUN = cumsum))

sales_transactions$PurchasedAmtCumSum <- as.vector(ave(sales_transactions$Amt, 
                                                    sales_transactions$CustomerDimKey, 
                                                    FUN = cumsum))

#transaction count
sales_transactions <- data.table(sales_transactions)
sales_transactions <- sales_transactions[, TransactionCount := sequence(.N), by = CustomerDimKey]

#age 
today <- Sys.Date()
sales_transactions$AgeAtTrans <- as.numeric(sales_transactions$DateDimKey - sales_transactions$FirstSoldDate)
sales_transactions$CustomerAge <- as.numeric(today - sales_transactions$FirstSoldDate)

#calculate metrics for segmentation 
#recency 
sales_transactions$DaysSinceLastTransaction <- today - sales_transactions$LastSoldDate

#frequency of order 
sales_transactions$OrdersPerDay <- sales_transactions$TransactionCount / sales_transactions$AgeAtTrans
sales_transactions$TransFreq    <- sales_transactions$AgeAtTrans / sales_transactions$TransactionCount

#monetary avg order size
sales_transactions$AvgOrderSize <- sales_transactions$PurchasedQtyCumSum / sales_transactions$TransactionCount
sales_transactions$AvgOrderAmt  <- sales_transactions$PurchasedAmtCumSum / sales_transactions$TransactionCount

#LTV metrics 
#can parameterize using quantile percentages
sales_transactions$AvgAmtPerDay <- sales_transactions$AvgOrderAmt * sales_transactions$OrdersPerDay
sales_transactions$LowerLTV     <- as.numeric(quantile(sales_transactions$CustomerAge, 0.25)) * 
                                                       sales_transactions$AvgAmtPerDay

sales_transactions$MidQLTV     <- as.numeric(quantile(sales_transactions$CustomerAge, 0.50)) * 
                                                      sales_transactions$AvgAmtPerDay

sales_transactions$UpperQLTV     <- as.numeric(quantile(sales_transactions$CustomerAge, 0.75)) * 
                                                       sales_transactions$AvgAmtPerDay

#scoring mechanism
Rscore <- function(sales_transactions) {
#browser()
  score <- vector()
  
  DaysSinceLastTransaction <- as.numeric(sales_transactions$DaysSinceLastTransaction)
  highest   <- as.numeric(quantile(DaysSinceLastTransaction, 0.50))
  high      <- as.numeric(quantile(DaysSinceLastTransaction, 0.60))
  benchmark <- as.numeric(quantile(DaysSinceLastTransaction, 0.70))
  low       <- as.numeric(quantile(DaysSinceLastTransaction, 0.80))
  lowest    <- as.numeric(quantile(DaysSinceLastTransaction, 0.90))
  
  for (i in 1:length(DaysSinceLastTransaction)) {
    if(DaysSinceLastTransaction[i] < 0 ||
       DaysSinceLastTransaction[i] >= 0 & 
       DaysSinceLastTransaction[i] <= highest) {
      score[i] <- 5
    } else if(DaysSinceLastTransaction[i] > highest & 
       DaysSinceLastTransaction[i] <= high) {
      score[i] <- 4
    } else if(DaysSinceLastTransaction[i] > high & 
       DaysSinceLastTransaction[i] <= benchmark) {
      score[i] <- 3
    } else if(DaysSinceLastTransaction[i] > benchmark & 
       DaysSinceLastTransaction[i] <= low) {
      score[i] <- 2
    } else if(DaysSinceLastTransaction[i] > low & 
       DaysSinceLastTransaction[i] <= lowest) {
      score[i] <- 1
    } else {
      score[i] <- 0
    }
  }
  sales_transactions$RScore <- score
  
  return(sales_transactions)
}
  sales_transactions <- Rscore(sales_transactions)

#scoring mechanism
Fscore <- function(sales_transactions) {
  #browser()
  score <- vector()
  
  TransFreq <- as.numeric(sales_transactions$TransFreq)
  highest   <- as.numeric(quantile(TransFreq, 0.20))
  high      <- as.numeric(quantile(TransFreq, 0.40))
  benchmark <- as.numeric(quantile(TransFreq, 0.60))
  low       <- as.numeric(quantile(TransFreq, 0.80))
  lowest    <- as.numeric(quantile(TransFreq, 0.95))
  
  for (i in 1:length(TransFreq)) {
    if(TransFreq[i] < highest) {
      score[i] <- 5
    } else if(TransFreq[i] < high & 
              TransFreq[i] >= highest) {
      score[i] <- 4
    } else if(TransFreq[i] < benchmark & 
              TransFreq[i] >= high) {
      score[i] <- 3
    } else if(TransFreq[i] < low & 
              TransFreq[i] >= benchmark) {
      score[i] <- 2
    } else if(TransFreq[i] < lowest & 
              TransFreq[i] >= low) {
      score[i] <- 1
    } else {
      score[i] <- 0
    }
  }
  sales_transactions$FScore <- score
  
  return(sales_transactions)
}
  sales_transactions <- Fscore(sales_transactions)

Mscore <- function(sales_transactions) {
  #browser()
  score <- vector()
  
  AvgOrderSize <- as.numeric(sales_transactions$AvgOrderSize)
  highest   <- as.numeric(quantile(AvgOrderSize, 0.90))
  high      <- as.numeric(quantile(AvgOrderSize, 0.80))
  benchmark <- as.numeric(quantile(AvgOrderSize, 0.70))
  low       <- as.numeric(quantile(AvgOrderSize, 0.60))
  lowest    <- as.numeric(quantile(AvgOrderSize, 0.50))
  
  for (i in 1:length(AvgOrderSize)) {
    if(AvgOrderSize[i] > highest) {
      score[i] <- 5
    } else if(AvgOrderSize[i] > high & 
              AvgOrderSize[i] <= highest) {
      score[i] <- 4
    } else if(AvgOrderSize[i] > benchmark & 
              AvgOrderSize[i] <= high) {
      score[i] <- 3
    } else if(AvgOrderSize[i] > low & 
              AvgOrderSize[i] <= benchmark) {
      score[i] <- 2
    } else if(AvgOrderSize[i] > lowest & 
              AvgOrderSize[i] <= low) {
      score[i] <- 1
    } else {
      score[i] <- 0
    }
  }
  sales_transactions$MScore <- score
  
  return(sales_transactions)
}
  sales_transactions <- Mscore(sales_transactions)
  
AssignCohort <- function(sales_transactions) {
  #browser()
  
  flag <- vector()
  
  Rscore <- sales_transactions$RScore
  Fscore <- sales_transactions$FScore
  Mscore <- sales_transactions$MScore
  
  DaysSinceLastTransaction <- sales_transactions$DaysSinceLastTransaction
  TransFreq                <- sales_transactions$TransFreq   
  
  for (i in 1:length(Rscore)) { 
    
    if (Rscore[i] <= 2 &
        Fscore[i] >= 3 &
        Mscore[i] >= 3 ) {
      
         flag[i] <- 'At Risk'
         
    } else if 
       (Rscore[i] >= 4 &
        Fscore[i] >= 4 &
        Mscore[i] >= 4) {
      
         flag[i] <- 'VIPs'
         
    } else if 
      (Rscore[i] <= 5 &
       Fscore[i] <= 5 &
       Fscore[i] >  2 &
       Mscore[i] <= 2 ) {
        
         flag[i] <- 'UpSell'
        
    } else if 
      (Rscore[i] <= 2 &
       Fscore[i] <= 2 &
       Mscore[i] <= 2 ) {
        
         flag[i] <- 'Lost'
         
    } else if 
      (Rscore[i] >= 3 &
       Fscore[i] <= 2 &
       Mscore[i] >= 3 ) {
        
        flag[i] <- 'Seasonal'
    } else if 
      ((Rscore[i] + Fscore[i] + Mscore[i]) >= 12) {
      
      flag[i] <- 'Healthy'
      
    } else if 
      ((Rscore[i] + Fscore[i] + Mscore[i]) < 9) {
      
      flag[i] <- 'Un-Healthy'
      
    } else if 
      ((Rscore[i] + Fscore[i] + Mscore[i]) >= 9 ||
       (Rscore[i] + Fscore[i] + Mscore[i]) < 12) {
      
      flag[i] <- 'Un-Assigned'
      
    }
  }
  return(flag)
}
  sales_transactions$Cohort <- AssignCohort(sales_transactions)
 
last_recorded_sales <- sales_transactions %>% 
  group_by(CustomerDimKey) %>% 
  filter(DateDimKey == max(DateDimKey))

AtRisk      <- as.numeric(nrow(last_recorded_sales[last_recorded_sales$Cohort == "At Risk", ])) 
Healthy     <- as.numeric(nrow(last_recorded_sales[last_recorded_sales$Cohort == "Healthy", ])) 
UnHealthy   <- as.numeric(nrow(last_recorded_sales[last_recorded_sales$Cohort == "Un-Healthy", ])) 
Unassigned  <- as.numeric(nrow(last_recorded_sales[last_recorded_sales$Cohort == "Un-Assigned", ]))
Lost        <- as.numeric(nrow(last_recorded_sales[last_recorded_sales$Cohort == "Lost", ]))
Seasonal    <- as.numeric(nrow(last_recorded_sales[last_recorded_sales$Cohort == "Seasonal", ]))
UpSell      <- as.numeric(nrow(last_recorded_sales[last_recorded_sales$Cohort == "UpSell", ]))
VIPs        <- as.numeric(nrow(last_recorded_sales[last_recorded_sales$Cohort == "VIPs", ]))

CohortCnts      <- c(AtRisk, Healthy, UnHealthy, Unassigned, Lost, Seasonal, UpSell, VIPs)
CohortCntLabels <- data.frame(Cohorts=c('AtRisk','Healthy','UnHealthy','Unassigned', 'Lost', 'Seasonal', 'UpSell', 'VIPs')) 
CohortCntLabels <- cbind(CohortCntLabels, CohortCnts)
```
