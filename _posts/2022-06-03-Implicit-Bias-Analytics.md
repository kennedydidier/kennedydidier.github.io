---
layout: post
title: Exploring Employer Implicit Bias Data
categories: [r dataexploration businessanalytics descriptivestatistics]
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

![fhist_custserv](https://user-images.githubusercontent.com/102122956/173201346-2f6dc552-b80d-4e88-a51d-33864b53b43c.png)![fhist_interact](https://user-images.githubusercontent.com/102122956/173201352-b0ff1f65-2ac7-424a-81c1-c1a7a385bcee.png)![fhist_job_location](https://user-images.githubusercontent.com/102122956/173201354-87e2b238-4a7a-4c11-87df-6ab73da4afc6.png)
![fhist_manualskill](https://user-images.githubusercontent.com/102122956/173201365-0916a1c4-0df1-482d-9831-d5f17aa1f021.png)



These four columns (city, interact, custserv, and manualskill) can help generate some interesting business insights. City is a binary categorical variable that identifies whether the job is located in the city center. Interact is a binary categorical variable that identifies whether the applicant interacted with the employer during their interview (for example, if they spoke to them when they dropped off their application). Custserv is a binary categorical variable that identifies whether the position for which the applicants applied was a customer service industry. Manualskill identifies (1) whether the job requires manual skills. These histograms allow us to visualize how many applications were submitted to positions of each variety.

#### II. Descriptive Statistics

This dataset allows us to observe how the rate of employer callbacks changes depending on an applicant's race and criminal record. In order to measure how location, interaction with the employer, and the type of position modifies these results, I first replicated Dr. Pager's descriptive analysis by analyzing how the odds of recieving a callback differed across race and by criminal record for all applications, regardless of their location, employer interaction, or type.

{% highlight r %}
results<-dat %>% 
  group_by(black, crimrec)
results%>%
  summarise(callback=mean(as.numeric(as.character(callback))))
kable(results)%>%
  kable_styling(font_size=10)

black_results<-results%>%
  filter(black==1)
  
 br_treatment<-black_results%>%
  filter(crimrec==1) %>%
  select(callback)
  
 br_control<-black_results %>%
  filter(crimrec==0) %>%
  select(callback)
  
 br_treatment - br_control
 
 1 - br_treatment/br_control

white_results<-results%>%
  filter(black!=1)
  
 wr_treatment<-white_results %>%
  filter(crimrec==1)%>%
  select(callback)
 
 wr_control<-white_results %>%
  filter(crimrec==0)%>%
  select(callback)
 
 wr_treatment - wr_control
 1 - wr_treatment/wr_control
 
 {% endhighlight %}

According to this analysis, 14.07% of Black applicants without criminal records received callbacks. Comparatively, only 5.07% of Black applicants with a criminal record received callbacks. Having a criminal record decreased the chances of Black applicants recieving a callback by 9%.

34.00% of white applicants without criminal records received callbacks. 16.67% of white applicants with criminal records received callbacks. Having a criminal record decreased the chances of white applicants receiving a callback by 17.33%.

Comparing across racial groups, however, reveals even more information. 16.67% of white applicants with a criminal records received a callback, which is 2.6% higher than the callback rate for Black applicants without a criminal record. There is massive disparity in callback rates across race, though across both white and Black applicants the chances of receiving a callback was cut roughly in half if they had a criminal record.


A core explanation for the racial differences in callback rates can be found in the concept of [Implicit Bias](https://diversity.nih.gov/sociocultural-factors/implicit-bias). This audit study was conducted in Milwaukee, a progressive city with almost equal percentages of Black and White residents and where Black ownership of small businesses is [soaring](https://www.tmj4.com/news/local-news/surge-of-entrepreneurs-black-owned-businesses-continue-to-grow-in-milwaukee-despite-the-pandemic). Thus, it is hard to purport that this discrepancy is the product of purposeful and intentional discrimination on behalf of businesses and hiring managers. Rather, it is likely that it comes from unintentional bias and a lack of training in how to mitigate implicit bias.  

**It is important to try to understand which business and position factors are relevant in either intensifying or reducing racial disparities in callback rates (aka, identifying where implicit bias might be more present and where it is reduced). This generates important insight for businesses and can help them identify whether they should pay extra attention to reducing implicit bias in their hiring practices. Digging into how the odds of receiving a callback change based on the loation, interaction with the employer, and type of job to which the applicant applied allows us to make such observations.**

{% highlight r %}
city_odds<-dat%>%
  group_by(city, black, crimrec)%>%
  summarise(callback=(mean(as.numeric(as.character(callback)))))%>%
  arrange(black, crimrec)
kable(city_odds)%>%
  kable_styling(font_size=10)
{% endhighlight %}

<img width="669" alt="Screen Shot 2022-06-07 at 5 11 23 PM" src="https://user-images.githubusercontent.com/102122956/172483532-00939ba0-f484-4ee0-82a7-200f507dfe00.png">

This table allows us to see how the odds of recieving a callback differ depending on the location of the place of employment. 
Interestingly, we can see here that being located in a city center means a lower chance of a callback for applicants of any race 
or criminal status, _except_ for Black applicants with criminal records. For these applicants, the odds of recieving a callback 
_increases_ from 3.4% to 6.3% if the job is located in the city center. This suggests that, despite the increased competitiveness of jobs in the city center, they still are more open to hiring a Black applicant with a criminal record than suburban businesses.

When comparing across racial groups, it is clear that discrepancies in callback odds based on race and criminal record persist regardless of the location of the job for which the applicant applied. However, for jobs within the city center, Black applicants 
without a criminal record had a higher chance of receiving a callback than White applicants with a criminal record, which is different from the overall results. Further, the odds of a White person without a criminal record were 23.2% and the odds of a Black person without a criminal record were 11.1%, 12.1% different -- a reduction from the overall difference of 20%. This suggests that, compared to businesses in the suburbs, businesses located in the city center demonstrate less systematic implicit bias than suburban businesses.

{% highlight r %}
interact_odds<-dat%>%
  group_by(interact, black, crimrec)%>%
  summarise(callback=(mean(as.numeric(as.character(callback)))))%>%
  arrange(black, crimrec)
kable(interact_odds)%>%
  kable_styling(font_size=10)
{% endhighlight %}

<img width="668" alt="Screen Shot 2022-06-07 at 5 22 21 PM" src="https://user-images.githubusercontent.com/102122956/172485036-de3c038c-84fb-4b09-acd9-a3b9e94883b0.png">

The results of this table are _massively_ informative on the role of bias in employment practices. Here, we can see that across _all_ applicants, a personal interaction with the employer (for example, speaking to the hiring manager when turning in an application) 
increased the odds of receiving a callback. This increase was particularly sharp for White applicants with criminal records and for 
Black applicants without a criminal record. In fact, Black applicants without a criminal record who interacted with their potential employer had an 8.9% higher chance of being called back when compared to White applicants with no criminal record who did not interact with their potential employer.

This suggests that implicit bias most often influences the odds of a callback when employers just examine a resume and do not actually interact with a potential employee. They may unconsciously react to a traditionally "Black" sounding name and react more harshly react to the answer of "yes" to the question of a criminal record when they have not spoken to the applicant in person, regardless of the applicants social skills or presentation. This implies that the influence of implicit bias could very easily be reduced with no training at all simply by an employer personally accepting an application, which allows them to see the person to which the resume is attached.

{% highlight r %}
custserv_odds<-dat%>%
  group_by(custserv, black, crimrec)%>%
  summarise(callback=(mean(as.numeric(as.character(callback)))))%>%
  arrange(black, crimrec)
kable(custserv_odds)%>%
  kable_styling(font_size=10)
{% endhighlight %}

<img width="669" alt="Screen Shot 2022-06-07 at 5 40 32 PM" src="https://user-images.githubusercontent.com/102122956/172487592-772b7648-404f-4371-8026-44111b1b546d.png">

According to this table, the chances of receiving a callback were roughly equivalent for every type of applicant across customer service and non-customer service jobs and thus, roughly equivalent to the overall callback rates. Examining closely, it appears that customer service positions have slightly higher odds of calling an applicant back regardless of their race or criminal record, implying that this industry may have a higher need for employees than non-customer service jobs. Whether a job is in the customer service industry or not does not particularly seem to affect bias, as White applicants with a criminal record were called back more than Black applicants with a criminal record for both customer service industry positions and non-customer service industry positions.

{% highlight r %}
manualskill_odds<-dat%>%
  group_by(manualskill, black, crimrec)%>%
  summarise(callback=(mean(as.numeric(as.character(callback)))))%>%
  arrange(black, crimrec)
kable(manualskill_odds)%>%
  kable_styling(font_size=10)
{% endhighlight %}

<img width="667" alt="Screen Shot 2022-06-07 at 5 43 09 PM" src="https://user-images.githubusercontent.com/102122956/172487959-238e6b99-8a76-4387-b7f0-8c4f11a78e7e.png">

When examining jobs that require manual skills versus those that do not, the results are similar to our customer service results. Jobs requiring manual skills increasedthe odds of White applicants without criminal records receiving callbacks by 8%, and increased the odds of a Black applicant with a criminal record by .7%. However, the odds of a Black applicant without a criminal record receiving a callback for a manual skills job was 2% less than their odds of receiving a callback for a non-manual skills one. This suggests that non-manual skills employers were marginally better at reducing implicity bias. 



The results of these descriptive statistics give us a glimpse of how the odds of recieving a callback differ for different populations of applicants across certain characteristics of the business at which they are applying. One major, directly actionable insight this data suggests is that employer interaction with a potential employee massively decreases racial bias.
