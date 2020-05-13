---
title: "EDA on Titanic"
date: 2018-05-17T23:08:21+05:45
author: false
draft: false
featuredImage: /images/eda_titanic/preview.jpg
featuredImagePreview: /images/eda_titanic/preview.jpg
---

In this post, I am going to do Exploratory Data Analysis(EDA) on Titanic disaster datasets from kaggle Titanic: Machine Learning Disaster Competition. Before building machine learning model, I want to do EDA on this dataset find some idea about the features and structure of the dataset.

>RMS Titanic was a British passenger liner that sank in the North Atlantic Ocean in the early hours of 15 April 1912, after colliding with an iceberg during its maiden voyage from Southampton to New York City. There were an estimated 2,224 passengers and crew aboard, and more than 1,500 died, making it one of the deadliest commercial peacetime maritime disasters in modern history. RMS Titanic was the largest ship afloat at the time it entered service.
-- [Wikipedia](https://en.wikipedia.org/wiki/RMS_Titanic).

### Import and clean data

In this project we will use ggplot2,dplyr,VIM,mice packages of R.The kaggle dataset has three .csv file test,train,gender_submission.This is good for Machine Learning but not for EDA so I will create a single data frame joining 3 data frames.

```R
library(ggplot2)
library(dplyr)
library(VIM)
library(mice)
train.df <- read.csv("train.csv")
test.df  <- read.csv("test.csv")
target.df   <- read.csv("gender_submission.csv")
 
##creating dataframe
titanic.df <- cbind(test.df,target.df$Survived)
colnames(titanic.df)[12]<- "Survived"
train.df<- train.df[c(1,3,4,5,6,7,8,9,10,11,12,2)]
titanic.df <- rbind(train.df,titanic.df)
```
I loaded three .csv and joined to form titanic.df.Heres how the titanic.df looks like.

```
  PassengerId Pclass                                                Name
## 1           1      3                             Braund, Mr. Owen Harris
## 2           2      1 Cumings, Mrs. John Bradley (Florence Briggs Thayer)
## 3           3      3                              Heikkinen, Miss. Laina
## 4           4      1        Futrelle, Mrs. Jacques Heath (Lily May Peel)
## 5           5      3                            Allen, Mr. William Henry
## 6           6      3                                    Moran, Mr. James
##      Sex Age SibSp Parch           Ticket    Fare Cabin Embarked Survived
## 1   male  22     1     0        A/5 21171  7.2500              S        0
## 2 female  38     1     0         PC 17599 71.2833   C85        C        1
## 3 female  26     0     0 STON/O2. 3101282  7.9250              S        1
## 4 female  35     1     0           113803 53.1000  C123        S        1
## 5   male  35     0     0           373450  8.0500              S        0
## 6   male  NA     0     0           330877  8.4583              Q        0
```

Let’s see the structure of titanic.df.


```
## 'data.frame':    1309 obs. of  12 variables:
##  $ PassengerId: int  1 2 3 4 5 6 7 8 9 10 ...
##  $ Pclass     : int  3 1 3 1 3 3 1 3 3 2 ...
##  $ Name       : Factor w/ 1307 levels "Abbing, Mr. Anthony",..: 109 191 358 277 16 559 520 629 417 581 ...
##  $ Sex        : Factor w/ 2 levels "female","male": 2 1 1 1 2 2 2 2 1 1 ...
##  $ Age        : num  22 38 26 35 35 NA 54 2 27 14 ...
##  $ SibSp      : int  1 1 0 1 0 0 0 3 0 1 ...
##  $ Parch      : int  0 0 0 0 0 0 0 1 2 0 ...
##  $ Ticket     : Factor w/ 929 levels "110152","110413",..: 524 597 670 50 473 276 86 396 345 133 ...
##  $ Fare       : num  7.25 71.28 7.92 53.1 8.05 ...
##  $ Cabin      : Factor w/ 187 levels "","A10","A14",..: 1 83 1 57 1 1 131 1 1 1 ...
##  $ Embarked   : Factor w/ 4 levels "","C","Q","S": 4 2 4 4 4 3 4 4 4 2 ...
##  $ Survived   : int  0 1 1 1 0 0 0 0 1 1 ...
```

I found titanic.df has 1309 rows and 12 columns and The 12 variables in titanic.df is:

* Survived: Survival 0 = No, 1 = Yes 
* Pclass: Ticket class 1 = 1st, 2 = 2nd, 3 = 3rd 
* Sex: Sex Age: Age in years 
* Sibsp: No of siblings/spouses aboard the Titanic 
* Parch: of parents/children aboard the Titanic 
* Ticket: Ticket number 
* Fare: Passenger fare 
* Cabin: Cabin number 
* Embarked: Port of Embarkation C = Cherbourg, Q = Queenstown, S = Southampton

### Missing Data

I want to find is there any missing value in “titanic.df”.

```R
sum(is.na(titanic.df))
## [1] 264
```

Out of 1309 observations, titanic.df has 264 missing value. I want to find the distribution of missing value in our data frame. I am going to use the VIM and mice package to show the pattern of missing value in titanic.df.

```R
##finding any missing value in dataframe
mice_plot <- aggr(titanic.df, col=c('green','red'),
                  numbers=TRUE, sortVars=TRUE,
                  labels=names(titanic.df), cex.axis=.7,
                  gap=3, ylab=c("Missing data","Pattern"))
## 
##  Variables sorted by number of missings: 
##     Variable        Count
##          Age 0.2009167303
##         Fare 0.0007639419
##  PassengerId 0.0000000000
##       Pclass 0.0000000000
##         Name 0.0000000000
##          Sex 0.0000000000
##        SibSp 0.0000000000
##        Parch 0.0000000000
##       Ticket 0.0000000000
##        Cabin 0.0000000000
##     Embarked 0.0000000000
##     Survived 0.0000000000
```
From above code, I got a figure and a table showing pattern of missing value distribution. The red denotes missing value and green denote normal value. From the figure, we can see Fare and Age only has missing values. Age has more than 20% missing values and Fare has less than 1% missing value. We cant drop 264 rows with the missing value that may not give us valuable result after analysis. We also cant drop Age column due to missing value so we need to put some value in place of missing value. In this dataset, we will put mean of Age in the missing value. We will drop the row with missing fare as it has only one missing value.

```R
titanic.df$Age[is.na(titanic.df$Age)] = mean(titanic.df$Age, na.rm = TRUE)
titanic.df <- na.omit(titanic.df)
#missing values
sum(is.na(titanic.df))
## [1] 0
str(titanic.df)
## 'data.frame':    1308 obs. of  12 variables:
##  $ PassengerId: int  1 2 3 4 5 6 7 8 9 10 ...
##  $ Pclass     : int  3 1 3 1 3 3 1 3 3 2 ...
##  $ Name       : Factor w/ 1307 levels "Abbing, Mr. Anthony",..: 109 191 358 277 16 559 520 629 417 581 ...
##  $ Sex        : Factor w/ 2 levels "female","male": 2 1 1 1 2 2 2 2 1 1 ...
##  $ Age        : num  22 38 26 35 35 ...
##  $ SibSp      : int  1 1 0 1 0 0 0 3 0 1 ...
##  $ Parch      : int  0 0 0 0 0 0 0 1 2 0 ...
##  $ Ticket     : Factor w/ 929 levels "110152","110413",..: 524 597 670 50 473 276 86 396 345 133 ...
##  $ Fare       : num  7.25 71.28 7.92 53.1 8.05 ...
##  $ Cabin      : Factor w/ 187 levels "","A10","A14",..: 1 83 1 57 1 1 131 1 1 1 ...
##  $ Embarked   : Factor w/ 4 levels "","C","Q","S": 4 2 4 4 4 3 4 4 4 2 ...
##  $ Survived   : int  0 1 1 1 0 0 0 0 1 1 ...
##  - attr(*, "na.action")=Class 'omit'  Named int 1044
##   .. ..- attr(*, "names")= chr "1044"
```

### Summary of data

```R
summary(titanic.df)
```

```
##   PassengerId         Pclass                                    Name     
##  Min.   :   1.0   Min.   :1.000   Connolly, Miss. Kate            :   2  
##  1st Qu.: 327.8   1st Qu.:2.000   Kelly, Mr. James                :   2  
##  Median : 654.5   Median :3.000   Abbing, Mr. Anthony             :   1  
##  Mean   : 654.7   Mean   :2.294   Abbott, Mr. Rossmore Edward     :   1  
##  3rd Qu.: 981.2   3rd Qu.:3.000   Abbott, Mrs. Stanton (Rosa Hunt):   1  
##  Max.   :1309.0   Max.   :3.000   Abelson, Mr. Samuel             :   1  
##                                   (Other)                         :1300  
##      Sex           Age            SibSp            Parch       
##  female:466   Min.   : 0.17   Min.   :0.0000   Min.   :0.0000  
##  male  :842   1st Qu.:22.00   1st Qu.:0.0000   1st Qu.:0.0000  
##               Median :29.88   Median :0.0000   Median :0.0000  
##               Mean   :29.86   Mean   :0.4992   Mean   :0.3853  
##               3rd Qu.:35.00   3rd Qu.:1.0000   3rd Qu.:0.0000  
##               Max.   :80.00   Max.   :8.0000   Max.   :9.0000  
##                                                                
##       Ticket          Fare                     Cabin      Embarked
##  CA. 2343:  11   Min.   :  0.000                  :1013    :  2   
##  1601    :   8   1st Qu.:  7.896   C23 C25 C27    :   6   C:270   
##  CA 2144 :   8   Median : 14.454   B57 B59 B63 B66:   5   Q:123   
##  3101295 :   7   Mean   : 33.295   G6             :   5   S:913   
##  347077  :   7   3rd Qu.: 31.275   B96 B98        :   4           
##  347082  :   7   Max.   :512.329   C22 C26        :   4           
##  (Other) :1260                     (Other)        : 271           
##     Survived     
##  Min.   :0.0000  
##  1st Qu.:0.0000  
##  Median :0.0000  
##  Mean   :0.3777  
##  3rd Qu.:1.0000  
##  Max.   :1.0000  
```

We cleaned the missing value of titanic.df and now we can do further analysis on the variables of the data frame.

### Survival

We will look at Survived variable to find the survival of people.

```R
#distribution of survival
g <- ggplot(titanic.df,aes(Survived))
g + geom_bar(aes(y = (..count..)/sum(..count..))) + 
          scale_y_continuous(labels=scales::percent)+
          theme_minimal()+ylab("Percent")
```

In above figure, 0 means not Survived and 1 mins survived. We can see in the figure that more than 60% people were not able to survive the disaster.

### Age Distribution

```R
#Age distribution
g <-ggplot(titanic.df,aes(Age))+theme_minimal()
g + geom_density()
```

```R
g + geom_histogram(binwidth = 5)
```

Two figure of Age distribution shows that Titanic was filled with the passenger aged 80 years to infants. The peak in above plot shows that the maximum number of people were from (25- 35) age group.

```R
g <-ggplot(titanic.df,aes(Age))+
  theme_bw()+
  facet_wrap(~Survived)
g + geom_density()
```

This density plot shows us the distribution of age of passenger on basis of their survival. The plot shows that kids were likely to survive the disaster.

### Sex Distribution

```R
#Sex Distribution
titanic.df$Survived <- as.factor(titanic.df$Survived)
g <-ggplot(titanic.df,aes(Sex,fill=Sex))+
  theme_bw()
g+geom_bar(aes(y = (..count..)/sum(..count..))) + 
          scale_y_continuous(labels=scales::percent) +
          theme_minimal()+ylab("Percent")
```

This plot shows more than 60% of Titanic passenger were Male.

```R
g <-ggplot(titanic.df,aes(Sex,fill=Sex))+
  theme_bw() +
  facet_wrap(~Survived) +
  labs(x="",y="")

g + geom_bar()
```

This plot shows that more female survived the Titanic disaster than the male even though the population of the male was bigger than female in Titanic.

### Pclass Distribution

```R
g <-ggplot(titanic.df,aes(Pclass,fill=Sex)) +
  theme_bw()
g + geom_bar()
```

The passenger of Titanic was categorized into three classes. From above figure, we can see that 3rd class passengers were more than half of the Titanic passengers.

```R
g <-ggplot(titanic.df,aes(Survived,fill=Survived)) +
  theme_bw() +
  facet_wrap(~Pclass)

g + geom_bar()
```

This plot shows the survival of passenger on basis of Passenger class. We can see that 3rd and 2nd class passengers were more likely to not survive the disaster.1st class passengers were more likely to survive the disaster.

###  Embark

```R
titanic.df$Embarked <- as.factor(titanic.df$Embarked)
g <-ggplot(titanic.df,aes(Survived,fill=Survived)) +
  theme_bw() +
  facet_wrap(~Embarked)

g + geom_bar()
```

 Titanic’s first voyage was to New York before sailing to the Atlantic Ocean it picked passengers from three ports Cherbourg, Queenstown, Southampton. The above plot shows that many passengeriTitanicic embarked from the port of Southampton.

### Fare distribution

```R
g <-ggplot(titanic.df,aes(Fare,fill=Survived)) +
  theme_bw()
g + geom_histogram(binwidth = 10)
```
 This plot shows the distribution of fare paid by the passengers. We can see that more than 600 passengers paid in the range of( 10 to 20) fare. We also see that many passengers paid in the range of (10 to 100) fare.

### Parch Distribution

```R
g <-ggplot(titanic.df,aes(Parch,fill=Survived)) +
  theme_bw()
g + geom_bar()
```

 This plot shows the distribution of parent and children in passengers of Titanic. We can see that around 1000 of passengers were traveling without any parents or children.

### SibSp Distribution

```R
g <-ggplot(titanic.df,aes(SibSp,fill=Survived)) + 
  theme_bw() + labs(x="",y="")

g + geom_bar()
```
 This plot shows the distribution of Siblings and spouse of the passengers. More than 800 of people were traveling without siblings or spouse. This plot shows that SibSp was distributed from 0 to Eight sibling/spouse.

### Conclusion

This was all about the EDA on Titanic Dataset. We looked at variables Age, Sex, Fare, Parch, SibSp, Embark, Pclass , Survived of the Titanic disaster dataset. We didn’t look on name, passengerId and cabin variable of data because the name and passengerId are just for identity purpose and they don’t have any relation to the survival of people.

I will use the idea I got from this analysis project on building Machine Learning models for this same dataset which is in Kaggle.