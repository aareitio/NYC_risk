---
title: "Measuring Risk in NYC"
author: "Andy Areitio, Claudia Kampbel, Pedro B Franco (INSEAD MBA Class of July 2017); Jorge Bravo"
date: "January 31, 2017"
output: 
 html_document:
    css: Styles/default.css
    theme: paper
    toc: yes
    toc_float:
      collapsed: no
      smooth_scroll: yes
---

# Project Objective:

We intend to analyze and identify patterns regarding car accidents in New York City ("NYC"). In order to acheive this objective we have obtained from the New York Police Department ("NYPD") open source data with almost two years of accidents. The table has 29 columns containing details on each accident reported to the NYPD in these two years.
 
Discalimer: Please note that in order to run this project on a local computer, you will have do download the files in the GITHUB repository and extract the CSV file from the NYPD_Motor_Vehicle_Collisions_2.csv.zip

Please name the file: data/NYPD_Motor_Vehicle_Collisions_Official.CSV

Thank you!


```r
# basic preparatory commands

# clear the working environment
rm(list = ls())

# load the dataset for the project
NYPDfile <- read.csv("./data/NYPD_Motor_Vehicle_Collisions_Official.CSV", sep=",", dec=".")

# load required packages
# install required packages if loading failed
# all packages sould be listed inside install_load()
source("./library.R")
#---install_load("ggplot2", "corrplot", "caret", "C50", "cwhmisc", "stringr", "gmodels")
```

## Project process and document structure

Please find below the process we have used to solve the proposed business problem:

**1. Defining business problem.** As described above, our intentions are to analyze and identify patterns concerning car accidents in the city of New York. We visualize our final output, for this project, as a tool that will indicate, on a simple scale, the danger of a certain postal code given a certain day of the week and time.

**2. Data undestanding.** We have downloaded open source data from the following site:
(https://data.cityofnewyork.us/Public-Safety/NYPD-Motor-Vehicle-Collisions/h9gi-nx95).

Each line of the table represents an incident involving a motor car vehicle in NYC from [xxx] to [xxx].

Please find below a breif description of the columns:
1. DATE (Floating Time Stamp) - Date of the incident
2. TIME (Text) - Time the incident occured
3. BOROUGH (Text) - The Bourough the accident occured
4. ZIP.CODE (Text) - The Zipcode of the location
5. LATITUDE (Number #) - Latitude of the location the accident occured
6. LONGITUDE (Number #) - Longitude of the location the accident occured
7. LOCATION (Location Datatype) - Location, where logitude and latitude are joined and can be refferenced to a map
8. ON.STREET.NAME (Plain text) - Name of the street the accident occured                
9. CROSS.STREET.NAME (Plain text) - Closest cross street           
10. OFF.STREET.NAME (Plain text)               
11. NUMBER.OF.PERSONS.INJURED (Number #) - Total number of injured in accident    
12. NUMBER.OF.PERSONS.KILLED (Number #) - Total number of fatalities     
13. NUMBER.OF.PEDESTRIANS.INJURED (Number #) - Pedestrians injured
14. NUMBER.OF.PEDESTRIANS.KILLED (Number #) - Pedestrian fatalities  
15. NUMBER.OF.CYCLIST.INJURED (Number #) - Cyclists injured  
16. NUMBER.OF.CYCLIST.KILLED (Number #) - Cyclists with fatalities    
17. NUMBER.OF.MOTORIST.INJURED (Number #) - Motorists injured  
18. NUMBER.OF.MOTORIST.KILLED (Number #) - MOtorists killed   
19. CONTRIBUTING.FACTOR.VEHICLE.1 (Plain text) 
20. CONTRIBUTING.FACTOR.VEHICLE.2 (Plain text)
21. CONTRIBUTING.FACTOR.VEHICLE.3 (Plain text)
22. CONTRIBUTING.FACTOR.VEHICLE.4 (Plain text)
23. CONTRIBUTING.FACTOR.VEHICLE.5 (Plain text)
24. UNIQUE.KEY  (Number #) - Unique key for the table                 
25. VEHICLE.TYPE.CODE.1 (Plain text) - Type of Vehicle 1 (sports utility, taxi, passenger, bus, etc)         
26. VEHICLE.TYPE.CODE.2 (Plain text)          
27. VEHICLE.TYPE.CODE.3 (Plain text)         
28. VEHICLE.TYPE.CODE.4 (Plain text)          
29. VEHICLE.TYPE.CODE.5 (Plain text)         

**3. Data preparation.** Preparation of the dataset for subsequent modeling.

+ Check for missing values, exclusion of corresponding observations.
+ Check for outliers, decision on their participation in analysis.
+ Conversion of non-numerical attributes to numerical dummy variables.
+ Range normalization (if required).
+ Dimensionality reduction (if required).
+ Separation of dataset into training and test.

**4. Modelling.** Building the predictive model based on training dataset with target attribute answering the chosen business question. 

For classification trees methodology, modelling process include following steps.

+ Choice of methodology and tool.
+ First application of tool to dataset.
+ Generation of decision tree visualization, decision on simplification possibility.
+ Simplification of the model if possible.
+ Preliminary evaluation of the model using confusion matrix and test dataset. Decision to proceed (if evaluation results are satisfactory) or repeating the modelling process.

**5. Evaluation.** Check of models predictive accuracy on test dataset.

+ Generation of confusion matrix using built model and test dataset.
+ Decision on accepting the model or repeating the modelling process.

**6. Deployment.** Conclusion on project results and description of potential deployment of built model in practice. Remarks about potential ways to improve process and model of this project.


# Business Understanding

People are the most important resource for a lot of companies. And employees turnover has always been one of major HR issues. Retaining workers is becoming a balancing act between hard data research and a human touch. It’s more important than ever to complement intuition with statistical analysis (more on this available, for example on [INSEAD Knowledge](http://knowledge.insead.edu/blog/insead-blog/the-art-of-keeping-employees-from-leaving-3896).

Business problem that this project intends to solve is **prediction of employees to leave in near future using attributes known about each of employees**.

# Data Understanding

## Source of data

For this project we use dataset 'Human Resources Analytics' published by Ludovic Benistant on kaggle ([source](https://www.kaggle.com/ludobenistant/hr-analytics/)) under CC BY-SA 4.0 License. 

## Dataset size and variables

+ Employee satisfaction level *(satisfaction_level)*
+ Last evaluation *(last_evaluation)*
+ Number of projects *(number_project)*
+ Average monthly hours *(average_montly_hours)*
+ Time spent at the company *(time_spend_company)*
+ Whether they have had a work accident *(Work_accident)*
+ Whether they have had a promotion in the last 5 years *(promotion_last_5years)*
+ Department *(sales)*
+ Salary *(salary)*
+ Whether the employee has left, the target variable in this project *(left)*

Below is summary of project dataset structure.


```r
colnames(NYPDfile)
```

```
##  [1] "DATE"                          "TIME"                         
##  [3] "BOROUGH"                       "ZIP.CODE"                     
##  [5] "LATITUDE"                      "LONGITUDE"                    
##  [7] "LOCATION"                      "ON.STREET.NAME"               
##  [9] "CROSS.STREET.NAME"             "OFF.STREET.NAME"              
## [11] "NUMBER.OF.PERSONS.INJURED"     "NUMBER.OF.PERSONS.KILLED"     
## [13] "NUMBER.OF.PEDESTRIANS.INJURED" "NUMBER.OF.PEDESTRIANS.KILLED" 
## [15] "NUMBER.OF.CYCLIST.INJURED"     "NUMBER.OF.CYCLIST.KILLED"     
## [17] "NUMBER.OF.MOTORIST.INJURED"    "NUMBER.OF.MOTORIST.KILLED"    
## [19] "CONTRIBUTING.FACTOR.VEHICLE.1" "CONTRIBUTING.FACTOR.VEHICLE.2"
## [21] "CONTRIBUTING.FACTOR.VEHICLE.3" "CONTRIBUTING.FACTOR.VEHICLE.4"
## [23] "CONTRIBUTING.FACTOR.VEHICLE.5" "UNIQUE.KEY"                   
## [25] "VEHICLE.TYPE.CODE.1"           "VEHICLE.TYPE.CODE.2"          
## [27] "VEHICLE.TYPE.CODE.3"           "VEHICLE.TYPE.CODE.4"          
## [29] "VEHICLE.TYPE.CODE.5"
```

## Distributions of variables {.tabset}

First, let's generate summary report for all variables.


```r
#---summary(HR)
```

We will visualize distributions of variables separately for people who left the company and who stayed to be able to see differences between these groups of people on charts before doing the actual modelling.


```r
#---HR.left <- subset(HR, left == 1)
#---HR.stayed <- subset(HR, left == 0)
```

There are [ ] observations of employees who left the company and [ ] observations of employees who stayed.

### Satisfaction level


```r
#---ggplot(HR.left, aes(HR.left$satisfaction_level)) + geom_density(kernel = "gaussian", fill = '#B8274C', alpha = 0.3) + labs(x = "Satisfaction level of employees who left") + xlim(0, 1)
```


```r
#---ggplot(HR.stayed, aes(HR.stayed$satisfaction_level)) + geom_density(kernel = "gaussian", fill = '#006E51', alpha = 0.3) + labs(x = "Satisfaction level of employees who stayed") + xlim(0, 1)
```

### Last evaluation


```r
#---ggplot(HR.left, aes(HR.left$last_evaluation)) + geom_density(kernel = "gaussian", fill = '#B8274C', alpha = 0.3) + labs(x = "Last evaluation of employees who left") + xlim(0, 1)
```


```r
#---ggplot(HR.stayed, aes(HR.stayed$last_evaluation)) + geom_density(kernel = "gaussian", fill = '#006E51', alpha = 0.3) + labs(x = "Last evaluation of employees who stayed") + xlim(0, 1)
```

### Number of projects


```r
#---ggplot(HR.left, aes(HR.left$number_project)) + geom_histogram(fill = '#B8274C', binwidth = .5) + scale_x_continuous(breaks = seq(0,max(max(HR.left$number_project),max(HR.stayed$number_project)),by = 1), limits = c(0, max(max(HR.left$number_project),max(HR.stayed$number_project))+1)) + labs(x = "Number of projects of employees who left")
```


```r
#---ggplot(HR.stayed, aes(HR.stayed$number_project)) + geom_histogram(fill = '#006E51', binwidth = .5) + scale_x_continuous(breaks = seq(0,max(max(HR.left$number_project),max(HR.stayed$number_project)),by = 1), limits = c(0, max(max(HR.left$number_project),max(HR.stayed$number_project))+1)) + labs(x = "Number of projects of employees who stayed")
```

### Monthly hours


```r
#---ggplot(HR.left, aes(HR.left$average_montly_hours)) + geom_density(kernel = "gaussian", fill = '#B8274C', alpha = 0.3) + labs(x = "Average monthly hours of employees who left") + xlim(min( min(HR.left$average_montly_hours), min(HR.stayed$average_montly_hours)), max( max(HR.left$average_montly_hours), max(HR.stayed$average_montly_hours)))
```


```r
#---ggplot(HR.stayed, aes(HR.stayed$average_montly_hours)) + geom_density(kernel = "gaussian", fill = '#006E51', alpha = 0.3) + labs(x = "Average monthly hours of employees who stayed") + xlim(min( min(HR.left$average_montly_hours), min(HR.stayed$average_montly_hours)), max( max(HR.left$average_montly_hours), max(HR.stayed$average_montly_hours)))
```

### Time at company


```r
#---ggplot(HR.left, aes(HR.left$time_spend_company)) + geom_histogram(fill = '#B8274C', binwidth = .5) + scale_x_continuous(breaks = seq(0,max(max(HR.left$time_spend_company),max(HR.stayed$time_spend_company)),by = 1), limits = c(0, max(max(HR.left$time_spend_company),max(HR.stayed$time_spend_company))+1)) + labs(x = "Time spent at the company (in years) of employees who left")
```


```r
#---ggplot(HR.stayed, aes(HR.stayed$time_spend_company)) + geom_histogram(fill = '#006E51', binwidth = .5) + scale_x_continuous(breaks = seq(0,max(max(HR.left$time_spend_company),max(HR.stayed$time_spend_company)),by = 1), limits = c(0, max(max(HR.left$time_spend_company),max(HR.stayed$time_spend_company))+1)) + labs(x = "Time spent at the company (in years) of employees who stayed")
```

### Work accidents 


```r
#---ggplot(HR.left, aes(HR.left$Work_accident)) + geom_histogram(fill = '#B8274C', binwidth = .5) + labs(x = "Number of employees who experienced and did not experience work accident of employees who left")
```


```r
#---ggplot(HR.stayed, aes(HR.stayed$Work_accident)) + geom_histogram(fill = '#006E51', binwidth = .5) + labs(x = "Number of employees who experienced and did not experience work accident of employees who stayed")
```

### Promotions 


```r
#---ggplot(HR.left, aes(HR.left$promotion_last_5years)) + geom_histogram(fill = '#B8274C', binwidth = .5) + labs(x = "Number of employees who were or were not promoted during last 5 years of employees who left")
```


```r
#---ggplot(HR.stayed, aes(HR.stayed$promotion_last_5years)) + geom_histogram(fill = '#006E51', binwidth = .5) + labs(x = "Number of employees who were or were not promoted during last 5 years of employees who stayed")
```

### Department


```r
#---Departments <- c("sales", "technical", "support", "IT", "product_mng", "marketing", "RandD", "accounting", "hr","management")
#---ggplot(HR.left, aes(HR.left$sales)) + geom_bar(stat = "count", fill = '#B8274C') + labs(x = "Department of employees who left") + scale_x_discrete(limits = Departments)
```


```r
#---ggplot(HR.stayed, aes(HR.stayed$sales)) + geom_bar(stat = "count", fill = '#006E51') + labs(x = "Department of employees who stayed") + scale_x_discrete(limits = Departments)
```

### Salary


```r
#---ggplot(HR.left, aes(HR.left$salary)) + geom_bar(stat = "count", fill = '#B8274C') + labs(x = "Salary level of employees who left") + scale_x_discrete(limits = c("low", "medium", "high"))
```


```r
#---ggplot(HR.stayed, aes(HR.stayed$salary)) + geom_bar(stat = "count", fill = '#006E51') + labs(x = "Salary level of employees who stayed") + scale_x_discrete(limits = c("low", "medium", "high"))
```

## Correlations

Let's check correlations between variables.


```r
#---CorTable <- cor(HR[,1:8])
#---corrplot(CorTable, method = "number", type= "upper")
```

# Data preparation

## Check for missing values

Let's count missing values in the dataset.


```r
#---sum(is.na(HR))
```

Since we don't have missing values, there is no need to exclude any observations from the dataset.

## Check for outliers

Graphical analysis of variables' distributions done on Data Understanding step did not show any suspicious outliers.

## Creation of dummy variables

Previously we identified, that two variables are categorical now: Department and Salary. Now we convert them to dummy variables, creating two dummy variables for Salary and nine dummy variables for Department.


```r
#---dummy <- dummyVars(" ~ .", data = HR, fullRank = TRUE)
#---HR.dm <- data.frame(predict(dummy, newdata = HR))
#---str(HR.dm)
```

## Range normalization

Classification tree methodology does not require scale normalization of input data, so we do not perform this step.

## Dimensionality reduction

Having small number of variables, we decided to avoid dimensionality reduction. 

## Correction of variables format

For using classification tree methodology, we convert part of variables to factor type.


```r
#---columns_to_factors <- c(6:19)
#---HR.dm[,columns_to_factors] <- lapply(HR.dm[,columns_to_factors], factor)
#---str(HR.dm)
```

## Separation into training and test datasets

Let's separate dataset into 80% training dataset and 20% test dataset.


```r
#---set.seed(777)
#---train.index <- createDataPartition(HR$left, p = .8, list = FALSE, times = 1)
#---TrainDS <- HR.dm[ train.index, ]
#---TestDS  <- HR.dm[-train.index, ]
```

There are observations in training dataset and observations in test dataset.

We can check that both datasets are roughly similar in distribution of target variable (whether employee left).


```r
#---round(prop.table(table(TrainDS$left)),3)
#---round(prop.table(table(TestDS$left)),3)
```

# Modelling: Decision Tree

## Choice of methodology and tool

Possible methodologies for solving classification problems include logistic regression, support vector machine, decision tree, Naive Bayes classification, k-nearest neighbor. For the purpose of this exercise we will use decision tree methodology. It should provide an output in a form of clear set of rules, that should be easy to communicate to management and easy to implement (for example, in company's HR system).

Our model should predict categorical response variable (if employee leaves) with two possible outcomes: yes (1) or no (0). For that reason, decision tree should be a classification tree. 

There are many packages in R for modeling decision trees: for example, rpart, party, RWeka, ipred, randomForest, gbm, C5.0. C5.0 is one of most up-to-date algorithms with following advantages ([source](http://en.proft.me/2016/11/9/classification-using-decision-trees-r/)):

+ An all-purpose classifier that does well on most problems.
+ Highly automatic learning process, which can handle numeric or nominal features, as well as missing data.
+ Less data cleaning required.
+ Excludes unimportant features.
+ Can be used on both small and large datasets.
+ Non parametric method (have no assumptions about the space distribution and the classifier structure).
+ Results in a model that can be interpreted without a mathematical background (for relatively small trees).
+ More efficient than other complex models.

C4.5, which was a C5.0 predecessor, became quite popular after ranking #1 in the Top 10 Algorithms in Data Mining pre-eminent paper published by Springer LNCS in 2008 ([source](https://en.wikipedia.org/wiki/C4.5_algorithm)). Taking all advantages into account, we are using C5.0 algorithm for the modelling.

C5.0 builds decision trees from a set of training data using the concept of information entropy. At each node of the tree, C5.0 chooses the attribute of the data that most effectively splits its set of samples into subsets enriched in one class or the other. The splitting criterion is the normalized information gain (difference in entropy). The attribute with the highest normalized information gain is chosen to make the decision. The C5.0 algorithm then recurs on the smaller sublists.

## First application of tool to dataset

For a first iteration, we apply C5.0 algorithm to the whole training dataset. Variable "Leave" serves as target variables and all other variables - as predictors. After the initial modelling we'll see which predicting variables are less helpful in predicting leave of employees and can be removed from modelling process.


```r
#---ClTree1 <- C5.0(TrainDS[-7],TrainDS$left)
#---summary(ClTree1)
```

## Simplification of the model

As we see from results of the first iteration, only five variables are mainly used by the model for making sufficiently precise prediction: average_montly_hours; satisfaction_level;	time_spend_company;	last_evaluation; number_project.

We will build a model only with these variables and will later evaluate performance of this simplified model on test dataset.


```r
#---ClTree2 <- C5.0(TrainDS[c(4, 1, 5, 2, 3)],TrainDS$left)
#---summary(ClTree2)
```


```r
# save results of modeling to .txt file
# data saved in file enables visualization of tree using free GraphViz software
# GraphViz can be installed from http://www.graphviz.org/
#---C5.0.graphviz(ClTree2, './/Tree2.txt')
```

# Evaluation

To evaluate the model we apply it to test dataset and build the confusion matrix of results. It shows how many mistakes of different type the model does and how many observations are classified correctly.


```r
#---PredictionTree <- predict(ClTree2, TestDS)
#---CT.Tree <- CrossTable(TestDS$left, PredictionTree, prop.chisq = FALSE, prop.c = FALSE, prop.r = FALSE, dnn = c('actual leave', 'predicted leave'))
#---CT.Tree.DF <- as.data.frame(CT.Tree)
```

As we can see from the table, for [ ] out of [ ] observations in test dataset model correctly predicted if employee will leave. This includes [ ] observations where model correctly predicted that employee will not leave and [ ] observation where model correctly predicted leave. Thus [ ] observations from test dataset were classified correctly.

Dataset was initially imbalanced ([ ] of all employees in dataset did not leave). Even that we already see significant information gain vs random gassing from conclusion above, let's check how successfully model identifies observations of both positive and negative target variable.

Model correctly identified [ ]% of positive observations (the ones where employee left). Only [ ] of employees who in fact left were not identified by the model.

Model also correctly identified [ ]% of negative observations. Only for [ ] of employees model generated false positive prediction.


# Deployment

As a result of the project, we built a model that proved to be a successful predictor of possible leave of employees. It turned out, that most powerful predictors of leave are working hours, satisfaction level, time spent at company, last evaluation, and number of projects. But this data should be used in a systematic logical way, described by decision tree we built - none of variables along can not predict a leave (this we saw from correlation analysis at the beginning).

Should this model be deployed in a real business environment, it can be integrated into HR IT system - letting know HR and line managers about risk of each of employees' leave. Being implemented in HR system, it can also generate a report, which signs were used by the algorithm to predict the leave (is it because employee work critically long hours or because of combination of big number of projects and low satisfaction?).

Potential ways to improve this project include the following.

+ It's possible to build predictive models using other methodologies and tools, in order to find ways to improve predictive power even further.
+ It makes sense to integrate estimation of employee's value for the company in the model - so that company will try to stop from leaving only employees who generate enough positive results.
+ Tool can be further improved to advice HR manager what parameters of employee's work-life should be changes the first in order to decrease risk of leaving most significantly.
