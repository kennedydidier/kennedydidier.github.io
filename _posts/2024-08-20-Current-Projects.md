---
layout: post
title: Current Projects
categories: [r, python, sql]
---

While I can't post code from my current projects, here are descriptions of a few I am working on! 

<!--more-->

#### I. Puck and Player Tracking Data Import

For the last fifteen months I have been working on writing Python scripts to capture, unnest, and feed live puck and player tracking data from Sportlogiq APIs into the NHL Database. 
Specifically, I have focused on writing a loop that we can use for our more data-intense calls (sometimes we can get upwards of a million records per game!) that enables us to call 
sequential chunks of game time without missing updates but also without re-calling data we've already ingested. I have also helped to create protocal tables and normalize the puck and 
player tracking data we collect, written scripts to write relevant portions of the data to the appropriate tables in the NHL database, and written assertions to make sure that no "bad"
or incomplete data makes its way into the database. 

#### II. Orientation 
I have also been involved in the creation of an Orientation model using puck and player tracking data. Using player and puck location data, our goal is to write an algorithm that enables
 us to identify the direction a player is facing (which we can then implement to improve the accuracy of our shot, possession, and pass models). 

#### III. HHI/Economic Modeling

I am also working with a coworker to develop some new economics-based statistics for hockey, including using the Herfindahl-Hirschman Index (HHI), Hoover Index, and/or Gini Coefficients to
 measure team competitiveness and track talent concentration, and how these stats influence playoff performance.


#### IV. New Stats Pages for NHL.com
I am also involved in the conception, development, and building of new and useful stats pages for NHL.com, increasing our offerings of player and team stats to the public.


In addition to these, my daily resonsibilities include writing MySQL queries to generate data reports for NHL PR and media partners, as well as various departments across the league.
 
