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
c1<- "tomato4"
c2<-"salmon2"
ggplot(dat, aes(x=city)) + geom_bar(aes(y=..count..),
                                    colour=c1, fill=c2) + ggtitle("Frequency Histogram of Job Location")
table(dat$interact)
c3<-"skyblue3"
c4<-"slategray1"
ggplot(dat, aes(x=interact)) + geom_bar(aes(y=..count..),
                                        colour=c3, fill=c4) + ggtitle("Frequency Histogram of Applicant Interaction with Employer")
table(dat$custserv)
c5<-"bisque2"
c6<-"cornsilk"
ggplot(dat, aes(x=custserv)) + geom_bar(aes(y=..count..),
                                        colour=c5, fill=c6) + ggtitle("Frequency Histogram of Customer Service Applications")
table(dat$manualskill)
c7<-"peru"
c8<-"goldenrod2"
ggplot(dat, aes(x=manualskill)) + geom_bar(aes(y=..count..),
                                        colour=c7, fill=c8) + ggtitle("Frequency Histogram of Applications Requiring Manual Skills")
{% endhighlight %}


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
