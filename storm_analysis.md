---
title: "Storm Analysis"
author: "Xi Wang"
date: "Sunday, March 22, 2015"
output: html_document
---

### Background
Storms and other severe weather events can cause both public health and economic problems for communities and municipalities. Many severe events can result in fatalities, injuries, and property damage, and preventing such outcomes to the extent possible is a key concern.

This project involves exploring the U.S. National Oceanic and Atmospheric Administration's (NOAA) storm database. This database tracks characteristics of major storms and weather events in the United States, including when and where they occur, as well as estimates of any fatalities, injuries, and property damage.

### Project Introduction
In this project, I will use R to analysis the history data of storm to find out two main items  
- Which types of events are most harmful with respect to population health?  
- Which type of events have the greatest economic consequences?  

### Conclusion
1. Tornado is the most harmful event according to both Fatalities Number and Injuries Number
2. Thunderstorm Wind and River Flood is the most harmful event according to property damage.

### Preparation
1. Useful library
```{r}
library(ggplot2)
library(plyr)
library(dplyr)
```
2. Setting up the working directory
```{r}
setwd("E:/DataScience/RepData_PeerAssessment2")
```
3. Reading in the data
```{r}
raw_data <- read.csv(
  "E:/DataScience/RepData_PeerAssessment2/repdata_data_StormData.csv")
```
### Quick Glance at the data
1. Data Dimension
```{r}
dim(raw_data)
```
2. Parameters included
```{r}
names(raw_data)
```
In order to analysis two questions, we are going to use ***`EVTYPE`***,
***`FATALITIES`***,***`INJURIES`***, and ***`PROPDMG`***.

### QUESTION 1 : Most harmful event based on population health
#### * Getting the proper data frame
```{r}
data_1 <- select(raw_data,one_of("STATE","EVTYPE","FATALITIES","INJURIES"))
```
What do we have here?
```{r}
head(data_1)
```
```{r}
tail(data_1)
```
#### * Computing the summary of FATALITIES and INJURIES by each EVTYPE
```{r}
data_sum_FATAL <- ddply(data_1, .(EVTYPE),summarize,Fatalities=sum(FATALITIES))
data_sum_INJU <- ddply(data_1, .(EVTYPE), summarize, Injuries = sum(INJURIES))
```
#### * One more step - Order the summary to see who is on the top list
```{r}
top_fatal <- arrange(data_sum_FATAL,desc(Fatalities))
top_injur <- arrange(data_sum_INJU,desc(Injuries))
```
Here are the summary of FATALITIES and INJURIES with descending order
```{r}
head(top_fatal)
```
```{r}
head(top_injur)
```
```{r}
 ggplot(top_fatal[1:8,],aes(EVTYPE,Fatalities))+geom_bar(stat="identity",
  colour = "steelblue",fill = "steelblue", width = 0.7) + 
  labs(title = "Most Harmful Events (Fatalities based)", 
       x = "Event", y = "Fatalities Number")
```
```{r}
 ggplot(top_injur[1:8,],aes(EVTYPE,Injuries))+geom_bar(stat="identity",
  colour = "yellow",   fill = "yellow", width = 0.7) + 
  labs(title = "Most Harmful Events (Injuries based)", 
       x = "Event", y = "Injuries Number")
```

### QUESTION 2 : Most harmful event based on economic consequences
#### * Getting the proper data frame
```{r}
data2 <- select(raw_data,one_of("STATE","EVTYPE","PROPDMG","PROPDMGEXP"))
```
What do we have here?
```{r}
head(data2)
```
Here we need to convert the unit all to "Million"
```{r}
rc <- dim(data2)
for(i in 1:rc[1]){
  if(data2[i,4] == "K"){data2[i,5]=data2[i,3]/1000}
  else if(data2[i,4] == "M"){data2[i,5]=data2[i,3]}
  else if(data2[i,4] == "B"){data2[i,5]=data2[i,3]*1000}
  else {data2[i,5]=data2[i,3]}
}
```
Ready for summarizing the total property damage
```{r}
data2 <- rename(data2,Total = V5)

data_total <- ddply(data2, .(EVTYPE),summarize,PROPDMG=sum(Total))
top_total <- arrange(data_total,desc(PROPDMG))
```
Let's see who is on top.
```{r}
head(top_total)
```
Plotting the top 5 candidates.
```{r}
 ggplot(top_total[1:5,],aes(EVTYPE,PROPDMG))+geom_bar(stat="identity",
  colour = "Red",fill = "Red", width = 0.7) + 
  labs(title = "Most Harmful Events (Economic Consequence based)", 
       x = "Event", y = "Property Damage")
```
