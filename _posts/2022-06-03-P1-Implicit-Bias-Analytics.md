---
layout: post
title: Exploring Employer Implicit Bias Data
categories: [r, data exploration, business analytics, descriptive statistics]
---

As part of my doctoral degree, I worked with lots of social science data. When I got bored with the social scientific mode of analysis, I began to look for opportunities to reanalyze these data in order to generate important business analytics and insight and hone my data analysis skills.   
<!--more-->

In this small project, I used a dataset published in 2003 collected from an audit study conducted by Dr. Devah Pager. For this study, Dr. Pager hired job applicants of different races and had them apply for a number of jobs, each time selecting one candidate that would identify themselves as an ex-felon. To determine how incarceration and race influence employment chances, Dr. Pager compared callback rates among applicants with and without a criminal background and calculated how those callback rates vary by race.

While Dr. Pager utilized this data in order to analyze the causal effects of a criminal record on the job prospects of applicants of different races, in examining the dataset I realized the data could also be used to generate interesting information about employer implicit bias across various job types. This analysis could inform hiring practices and help employers in certain sectors to identify how factors like race and criminal record influence these practices, even unconciously.


#### I. Basic Dimensions
{% highlight r %}
library(tidyverse)
library(knitr)
dat<-read.csv("kennedy/R/data/crimrec.csv")
str(dat)
head(dat)
nrow(dat)
i<-c("callback", "black", "crimrec", "interact", "city", "custserv", "manualskill")
dat[i]<-lapply(dat[i],factor)
sapply(dat, class)
summary(dat)
dat<-na.omit(dat)

table(dat$city)
c1<-"skyblue4"
c2<-"skyblue3"
c3<-"skyblue1"
ggplot(data=dat, aes(x=city, fill=as.factor(city))) + geom_bar(colour=c1) + scale_fill_manual(values=c(c2,c3)) + labs(fill="Location \n (0: Suburbs, 1: City)") + ggtitle('Frequency Histogram of Job Location') + theme(plot.title=element_text(size=11, margin=margin(10,0,10,0), lineheight=.9))

table(dat$interact)
c4<-"coral4"
c5<-"coral3"
c6<-"coral1"
ggplot(data=dat, aes(x=interact, fill=as.factor(interact))) + geom_bar(colour=c4) + scale_fill_manual(values=c(c5,c6)) + labs(fill="Employer Interaction \n (0: No Interaction, \n 1: Interaction)") + ggtitle('Frequency Histogram of Interaction with Employer') + theme(plot.title=element_text(size=11, margin=margin(10,0,10,0), lineheight=.9))

table(dat$custserv)
c7<-"navajowhite4"
c8<-"navajowhite3"
c9<-"navajowhite"
ggplot(data=dat, aes(x=custserv, fill=as.factor(custserv))) + geom_bar(colour=c7) + scale_fill_manual(values=c(c8,c9)) + labs(fill="Industry \n (0: Non-Customer Service, \n 1: Customer Service)") + ggtitle('Frequency Histogram of Customer Service Applications') + theme(plot.title=element_text(size=11, margin=margin(10,0,10,0), lineheight=.9))


table(dat$manualskill)
c10<-"olivedrab"
c11<-"darkolivegreen4"
c12<-"darkolivegreen3"
ggplot(data=dat, aes(x=manualskill, fill=as.factor(manualskill))) + geom_bar(colour=c10) + scale_fill_manual(values=c(c11,c12)) + labs(fill="Required Skills \n (0: Manual Skills Not Required, \n 1: Manual Skills Required)") + ggtitle('Frequency Histogram of Applications Requiring Manual Skills') + theme(plot.title=element_text(size=11, margin=margin(10,0,10,0), lineheight=.9))

{% endhighlight %}


![fhist_job_location](https://user-images.githubusercontent.com/102122956/172976620-fc0e3c81-0ed6-471c-a01f-ce90f265c83b.png)
![fhist_interact](https://user-images.githubusercontent.com/102122956/172976640-83452e1c-da9b-4e4a-acc2-7a2b34b9bcff.png)
![fhist_custserv](https://user-images.githubusercontent.com/102122956/172976656-39d26100-bd05-43d3-82a0-b25c054b524c.png)
![fhist_manualskill](https://user-images.githubusercontent.com/102122956/172976704-84006438-bacd-42d5-b05e-3a95091c9272.png)


### Tables

Title 1               | Title 2               | Title 3               | Title 4
--------------------- | --------------------- | --------------------- | ---------------------
lorem                 | lorem ipsum           | lorem ipsum dolor     | lorem ipsum dolor sit
lorem ipsum dolor sit | lorem ipsum dolor sit | lorem ipsum dolor sit | lorem ipsum dolor sit
lorem ipsum dolor sit | lorem ipsum dolor sit | lorem ipsum dolor sit | lorem ipsum dolor sit
lorem ipsum dolor sit | lorem ipsum dolor sit | lorem ipsum dolor sit | lorem ipsum dolor sit

Title 1 | Title 2 | Title 3 | Title 4
--- | --- | --- | ---
lorem | lorem ipsum | lorem ipsum dolor | lorem ipsum dolor sit
lorem ipsum dolor sit amet | lorem ipsum dolor sit amet consectetur | lorem ipsum dolor sit amet | lorem ipsum dolor sit
lorem ipsum dolor | lorem ipsum | lorem | lorem ipsum
lorem ipsum dolor | lorem ipsum dolor sit | lorem ipsum dolor sit amet | lorem ipsum dolor sit amet consectetur
