---
Title: "Getting and Cleaning Data"
Author: "Adrian Cortinas"
---

# Description
This repository is for John Hopkins' Getting and Cleaning Data Course Project assignment from Coursera. 

The task is to download raw data based on Human Activity Recognition Using Smartphones data set from Univerity of California, Irvine, produce a unique data set from all different experiment files, to then produce a clean set of data with the mean values of two variables. The final product is to create two csv files with these two data sets.

# Requirements
The script needs dplyr and tidyr libraries installed in order for it to work. You can install them by executing these commands:

```
install.packages("tidyr","dplyr")
```

You may need to restart R or R Studio after this is done.

# Contents
- README.md - This file
- CodeBook.md - Description of all elements used in the R script.
- run_analysis.R - R Script to run and perform the analysis

# Execution
run_analysis.R contains all the instructions to download, merge and produce aforementioned files. All you need to do is execute (source) run_analysis.R and it will create the two files, RawData.csv and TidyData.csv.

```
source("path_to_file/run_analysis.R")
```
