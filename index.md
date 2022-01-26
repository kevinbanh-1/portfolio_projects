```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```


# Introduction

![](C:\\Users\\kevin\\OneDrive\\Documents\\Portfolio Projects\\Google Analytics\\Capstone\\divvy_logo.jpg){width=75%}

In this case study, we will be focusing on Divvy, a program of the Chicago Department of Transportation and operated by LYFT, focused on providing bikes for users to rent and ride around the city. The service allows users to rent bikes from one station and return it at any other Divvy station. Using secondary data collected by Divvy themselves which was updated on January 6th, 2022, analysis will be done to answer key questions to help Divvy restructure their ad campaign.


# Business Task

We are interested in restructuring Divvy's ad campaign to better target and transition casual riders to become Divvy members. Casual riders are people who buy the single-ride pass and the full day pass while Divvy members purchase the annual memberships.

The key business questions that will help us achieve our task are:

1. What is the difference in the riding habits between casual riders and Divvy members?

2. Why would casual riders choose the annual memberships?

3. How can Divvy restructure its marketing program to influence casual riders to become members?


# Data Sources

We will be using the data source [https://divvy-tripdata.s3.amazonaws.com/index.html](https://divvy-tripdata.s3.amazonaws.com/index.html) provided by Divvy. We will be using data over the course of 12 months during 2021.

This data source was provided by Divvy. All bike riders are identified by their unique ride_id # thus there is no bias towards one group. 

Some important things to note is that Divvy has removed all bike rides that are less than 60 seconds to eliminate riders who are just having issues docking their bike i.e having a false start in addition to trips made by their own employees. It is possible that people have spent more than 60 seconds since we don't have any data on this thus our data might not be as credible. 

Since this data is updated monthly and is very recent, our analysis will be accurate for the business task at hand.

# Necessary libraries
```{r}
# Packages to connect to BigQuery
library(bigrquery)
library(DBI)

# Data processing and manipulation
library(dplyr)
library(plyr)
library(readr)
library(sjmisc)

# Data visualizations
library(ggplot2)
library(RColorBrewer)
```


# Getting our data

## Merging all 12 months of data
```{r, eval = FALSE}
# Merge all of our csv files over the course of 12 months into one csv file
cyclist <- list.files(path = "C:\\Users\\kevin\\OneDrive\\Documents\\Portfolio Projects\\Google Analytics\\Capstone",
                       pattern = "*.csv", full.names = TRUE) %>%
  lapply(read_csv) %>%
  bind_rows()
```



# Data Cleaning

```{r, eval = FALSE}
glimpse(cyclist)
```


Already there are several things sticking out. The **started_at** and **ended_at** attributes have both the date and time for each record. We will need to split this data up into separate column so that the analysis process is easier.

Another problem is **start_station_name**, **start_station_id**, and **end_station_name**, **end_station_id**. These attributes from a glance already have quite a bit of null values so we might delete these attributes all together since we don't need them to answer our business question.

The identification attributes (**ride_id**, **start_station_id**, **end_station_id**) should be factors as these are unique identifiers.

