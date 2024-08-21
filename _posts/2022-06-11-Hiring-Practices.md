---
layout: post
title: Predicting Employer Hiring Behavior
categories: [r logisticregression regression structuredlearningalgorithms dataanalysis]
---

Similar to the descriptive analysis I conducted on employer implicit bias using data from a 2003 
project by Dr. Devah Pager, this project uses another of Dr. Pager's datasets to study the workplace 
correlates to employer willingness to hire an applicant with a criminal record. 
<!--more-->
Like my previous project, instead of 
taking a social scientific approach to this data, I sought an opportunity to gain deeper insight into business 
behavior and employer hiring practices. Using [this](https://www.icpsr.umich.edu/web/ICPSR/studies/3599/datadocumentation), 
dataset, which is based upon a survey of employers in Milwaukee, Wisconsin, I developed a structured learning algorithm
to predict the odds that a Milwaukee business would be willing to hire an employee with a criminal record 
based on the business's starting wage, number of employees, and whether they routinely ask about criminal 
record on their applications.

#### I. Basic Dimensions and Transformations

{% highlight r %}
library(tidyverse)
library(tigerstats)
library(finalfit)
library(knitr)
library(kableExtra)
library(foreign)
library(broom)
library(describedata)
dat<-read_dta("kennedy/stata/data/employerhiring-pager.dta")
str(dat)
{% endhighlight %}

Two of my variables of interest, starting wage (_startwage_) and number of employees (_numemp_), are continuous. 
However, the other two, whether the business checks an applicant's criminal record (_checkcr_) and whether 
they are willing to hire someone with a criminal record (_hirecrim_), are binary categorical variables. 
This requires transformation. Further, the dataset contains, in addition to _checkcr_, another binary categorical 
variable called _questcr_ which denotes whether an employer asks an applicant about their record. This seems to kind of 
logically overlap with _checkcr_, so I'll look into that.

{% highlight r %}
i<-c("questcr", "checkcr", "hirecrim")
dat[i]<-lapply(dat[i],factor)
sapply(dat, class)
checkna<-filter(PS2, is.na(hirecrim) | is.na(checkcr)| is.na(questcr))
nrow(checkna)
t1<-dat%>%
  group_by(checkcr, questcr)%>%
  summarise(hirecrim=prop(hirecrim),
            n=n())%>%
  ungroup()
t1
dat<-mutate(dat, checkorask=
              case_when(
                checkcr==1 | questcr==1 ~ 1,
                checkcr==0 & questcr==0 ~0)
              )
dat
t2<-table(dat$checkorask, dat$hirecrim)
rownames(t2)<-c("Doesn't Check or Ask", "Either Checks or Asks")
kable(t2, col.names=c("Would Not Hire", "Would Hire"), caption="Counts")%>%
  kable_styling(font_size=10)%>%
  kable_material(c("striped", "hover"))
pt<-prop.table(t2)
rownames(pt)<-c("Doesn't Check or Ask", "Either Checks or Asks")
kable(pt, col.names=c("Would Not Hire", "Would Hire"), caption= "Proportions")%>%
  kable_styling(font_size=10)%>%
  kable_material(c("striped", "hover"
  ))
  {% endhighlight %}
  
  <img width="520" alt="Screen Shot 2022-06-08 at 5 19 08 PM" src="https://user-images.githubusercontent.com/102122956/172914463-651d6584-919b-412e-b725-9bc23996e2c5.png">

<img width="521" alt="Screen Shot 2022-06-08 at 5 24 11 PM" src="https://user-images.githubusercontent.com/102122956/172720210-c628cfc4-9393-411d-a50a-5104e1d2282c.png">
 
 As these tables demonstrate, 97 (60.6%) of businesses either checked or asked and did not hire applicants with criminal records. 5 (3.1%) neight asked nor checked and reported that they would not hire applicants with criminal records. 11 (6.8%) did not ask or check and did hire applicants with records, and 47 (29.4%) either asked or checked and did hire applicants with criminal records.

**I now want to build a predictive model that allows me to estimate the likelihood that a particular business would hire someone with a criminal record based on this checking or asking variable, the starting wage of the business, and the number of employees the business has.**

{% highlight r %}

datint<-mutate(dat, hirecrim=as.integer(as.character(hirecrim)))
ggplot(datint, aes(x=startwage, y=hirecrim)) + geom_point(size=1) + geom_smooth(method=lm) + geom_smooth()
ggplot(datint, aes(x=numemp, y=hirecrim)) + geom_point(size=1) + geom_smooth(method=lm) + geom_smooth()

{% endhighlight %}

![image](https://user-images.githubusercontent.com/102122956/172723481-a8ed6f87-368e-4bb6-b896-76357f268689.png)
![image](https://user-images.githubusercontent.com/102122956/172723656-de661038-fad5-41c4-99b5-ebb70793f756.png)


I already know that my model will require a logistic regression algorithm, as the dependent variable is inherently non-linear (can only be 0 or 1, as evident in the above graphs). I also want to look at density histograms for these two continuous variables to see if I should apply a normalizing transformation.

{% highlight r %}

startwagedens<-ggplot(datint, aes(x=startwage)) + geom_histogram(aes(y=..density..), binwidth=2, color="pink", fill="white") + geom_density()
startwagedens
numempdens<-ggplot(datint, aes(x=numemp)) + geom_histogram(aes(y=..density..), binwidth=5, color="pink", fill="white") + geom_density()
numempdens

{% endhighlight %}

![image](https://user-images.githubusercontent.com/102122956/172724231-0f7b97f8-9965-4181-b3ce-e46ee1fe341b.png)
![image](https://user-images.githubusercontent.com/102122956/172724433-4fad9859-41b4-44cf-8958-6fedcc3ac671.png)

{% highlight r %}
ladder(dat$startwage)
ladder(dat$numemp)
dat%>%
  mutate(sqrt_startwage=sqrt(startwage),
         ln_numemp=log(numemp))
{% endhighlight %}

#### II. Model Building

{% highlight r %}
dat_copy<-dat[complete.cases(dat), ]
dat_copy<-dat_copy[,-1]
table(dat_copy$hirecrim)
library(caret)
'%ni%'<-Negate('%in%')
options(scipen=999)
set.seed(06082021)
trainDataIndex<-createDataPartition(dat_copy$hirecrim, p=.7, list=F)
trainData<-dat_copy[trainDataIndex, ]
testData<-dat_copy[-trainDataIndex, ]
table(trainData$hirecrim)
set.seed(06082021)
up_train<-upSample(x=trainData[, colnames(trainData) %ni% "hirecrim"],
                   y=trainData$hirecrim)
table(up_train$hirecrim)
logitmod<-glm(hirecrim ~ sqrt_startwage + ln_numemp + checkorask, family="binomial", data=up_train)
summary(logitmod)
p1<-predict(logitmod, newData=testData, type="response")
y_p1_num<-ifelse(p1>0.5, 1, 0)
y_p1<-factor(y_p1_num, levels=c(0,1))
y_act<-testData$hirecrim
mean(y_p1==y_act)
{% endhighlight %}

<img width="314" alt="Screen Shot 2022-06-08 at 8 14 51 PM" src="https://user-images.githubusercontent.com/102122956/172738207-3dcd3711-5238-4e43-8a67-e267666d9c0b.png">


According to this algorithm, my model is accurate 67% of the time. Not ideal, but still gives me a good idea of how these variables are impactful, and makes use of the avaiable variables well.



