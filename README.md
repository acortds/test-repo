---
title: "README: Getting and Cleaning Data"
author: "Adrian Cortinas"
output: html_notebook
---


This is an [R Markdown](http://rmarkdown.rstudio.com) Notebook. When you execute code within the notebook, the results appear beneath the code. 

Try executing this chunk by clicking the *Run* button within the chunk or by placing your cursor inside it and pressing *Cmd+Shift+Enter*. 

```{r}
# Download dataset
setwd("/tmp")
fileURL <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileURL, destfile = "projectiles FUCI HAR Dataset.zip",method = "curl")
unzip("projectiles FUCI HAR Dataset.zip")
setwd("./UCI HAR Dataset")
rm(list = ls())

# Load packages
library(dplyr)
library(tidyr)

# Data files
LabelURL    <- "activity_labels.txt"
ColNamesURL <- "features.txt"
XtestURL    <- "test/X_test.txt"
YtestURL    <- "test/y_test.txt"
SubTestURL  <- "test/subject_test.txt"
XtrainURL   <- "train/X_train.txt"
YtrainURL   <- "train/y_train.txt"
SubTrainURL <- "train/subject_train.txt"

# Read data files
ColNames    <- read.table(ColNamesURL)
# Replace all punctuation characters with ":"
ColNames$V2 <- gsub("[-()]", ":", ColNames$V2)
# Remove repeating ":", f.e. "::" -> ":"
ColNames$V2 <- gsub("(:)+", ":", ColNames$V2)
# Remove ":" at the end of the line
ColNames$V2 <- gsub(":$", "", ColNames$V2)
# For range numbers, replace , with a C, so they can be easily manipulated when needed
ColNames$V2 <- gsub(",", "C", ColNames$V2)

LabelDesc   <-
  read.table(LabelURL    , col.names = c("act_id", "activity"))
Ytrain      <- read.table(YtrainURL   , col.names = c("act_id"))
Ytest       <- read.table(YtestURL    , col.names = c("act_id"))
Xtrainsub   <- read.table(SubTrainURL , col.names = c("sub_id"))
Xtestsub    <- read.table(SubTestURL  , col.names = c("sub_id"))
Xtrain      <-
  read.table(XtrainURL   , header = FALSE  , col.names = ColNames[, 2])
Xtest       <-
  read.table(XtestURL    , header = FALSE  , col.names = ColNames[, 2])

# Merge Activity ID + Subject ID
TestActSub  <- data.frame(Ytest  , Xtestsub)
TrainActSub <- data.frame(Ytrain , Xtrainsub)

# Merge ActSub + Data Set
XTestData   <- cbind(TestActSub,  Xtest)
XTrainData  <- cbind(TrainActSub, Xtrain)

# Merges the training and the test sets to create one data set. (Step 1)
AllData     <- rbind(XTestData, XTrainData)
write.csv(AllData, "AllData.csv")

# remove variables no longer used
rm(list = ls(pattern = "^X(.*)|^Y(.*)|^C(.*)|^S(.*)|^T(.*)"))

# From the data set in step 4, creates a second, independent tidy data set
# with the average of each variable for each activity and each subject

# Transforms the data and saves the result into SummData (Step 5):
# SummData <- AllData %>%

# Select all columns except all the angle ones. angle is a function like mean and stddev:
#   select (1:556) %>%

# Adds Description of Activity (Step 3):
#   mutate(activity = LabelDesc$activity[AllData$act_id]) %>%

# Transforms all columns to rows and place them into two variables, test and value:
#   gather (test, value, 3:556) %>%

# Splits test column into 4 columns:
#   separate(test,c("dimension","funct","axis","other"),fill="right") %>%

# Select only those rows that contains the words "mean" and "std" (Step 2):
#   filter(funct == "mean" | funct == "std") %>%

# Groups the resulting data set by these columns:
#   group_by(activity, sub_id, dimension,funct) %>% 

# Gets the mean of the value column by the former 4 columns:
#   summarise(value=mean(value)) %>% 

# Now we put the mean and std rows into their own column:
#   spread (funct, value) %>% 

# Sort the resulting data set by activity and subject id:
#   arrange(activity, sub_id) %>% 

# Rename the columns to something meaningful (Step 4):
#   rename(subject_id=sub_id,average_mean=mean,average_std_dev=std)

SummData <- AllData %>%
  select (1:556) %>%
  mutate(activity = LabelDesc$activity[AllData$act_id]) %>%
  gather (test, value, 3:556) %>%
  separate(test, c("dimension", "funct", "axis", "other"), fill = "right") %>%
  filter(funct == "mean" | funct == "std") %>%
  group_by(activity, sub_id, dimension, funct) %>%
  summarise(value = mean(value)) %>%
  spread (funct, value) %>%
  arrange(activity, sub_id) %>%
  rename(
    subject_id = sub_id,
    average_mean = mean,
    average_std_dev = std
  )

write.csv(SummData, "SummData.csv")
```

Add a new chunk by clicking the *Insert Chunk* button on the toolbar or by pressing *Cmd+Option+I*.

When you save the notebook, an HTML file containing the code and output will be saved alongside it (click the *Preview* button or press *Cmd+Shift+K* to preview the HTML file).
