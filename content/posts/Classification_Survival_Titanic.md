---
title: "Classification of Survival in Titanic"
date: 2018-08-27T23:11:19+05:45
author: false
draft: false
featuredImage: /images/survival_classify/preview.png
featuredImagePreview: /images/survival_classify/preview.png
---

In this blog, I will create a machine learning model which will predict the survival of the people in the Titanic accident. I will use titanic survival dataset and use the knn algorithm to find the survival of the people in the dataset.



> RMS Titanic was a British passenger liner that sank in the North Atlantic Ocean in the early hours of 15 April 1912, after colliding with an iceberg during its maiden voyage from Southampton to New York City. The data is taken from data.world. I have also done exploratory analysis in my previous blog on this data I will not work on EDA in this blog on the same data.

Lets start with loading the packages and dataset.

```R
library(dplyr)
library(caret)
library(MASS)
## importing data
titanic_df <- read.csv("titanic.csv")
```

### Dataset

The titanic datasets has 1309 rows and 14 columns. Lets know about the features or column of this datasets:

*survival - Survival (0 = No; 1 = Yes)
*class - Passenger Class (1 = 1st; 2 = 2nd; 3 = 3rd)
*name - Name
*sex - Sex
*age - Age
*sibsp - Number of Siblings/Spouses Aboard
*parch - Number of Parents/Children Aboard
*ticket - Ticket Number
*fare - Passenger Fare
*cabin - Cabin
*embarked - Port of Embarkation (C = Cherbourg; Q = Queenstown; S = Southampton)
*boat - Lifeboat (if survived)
*body - Body number (if did not survive and body was recovered)
*homedest - destination

```R
head(titanic_df)
```

```
pclass	survived	name	sex	age	sibsp	parch	ticket	fare	cabin	embarked	boat	cabin_1	embarked_1	boat_1	body	home_dest
1	TRUE	Allen, Miss. Elisabeth Walton	female	29.0000	0	0	24160	211.3375	B5	S	2	B5	S	2	NA	St Louis, MO
1	TRUE	Allison, Master. Hudson Trevor	male	0.9167	1	2	113781	151.5500	C22 C26	S	11	C22 C26	S	11	NA	Montreal, PQ / Chesterville, ON
1	FALSE	Allison, Miss. Helen Loraine	female	2.0000	1	2	113781	151.5500	C22 C26	S	NA	C22 C26	S	NA	NA	Montreal, PQ / Chesterville, ON
1	FALSE	Allison, Mr. Hudson Joshua Creighton	male	30.0000	1	2	113781	151.5500	C22 C26	S	NA	C22 C26	S	NA	135	Montreal, PQ / Chesterville, ON
1	FALSE	Allison, Mrs. Hudson J C (Bessie Waldo Daniels)	female	25.0000	1	2	113781	151.5500	C22 C26	S	NA	C22 C26	S	NA	NA	Montreal, PQ / Chesterville, ON
1	TRUE	Anderson, Mr. Harry	male	48.0000	0	0	19952	26.5500	E12	S	3	E12	S	3	NA	New York, NY
```

### Cleaning Data

I will clean the data before training the model with it. Let’s start with finding any missing values.


```
pclass  survived      name       sex       age     sibsp     parch    ticket  
 0         0            0         0        263       0         0         0          
fare    cabin  embarked      boat      body   home_dest 
  1      0         0           0      1188         0
```

We can see that the body column has 1188 missing value whereas age has 263 and fare has 1 missing value. I will remove body column from the data frame as it has around 90% missing value.

I use the median value of the age column to fill up the missing value and remove the row where the fare is missing.

Lets check again is there any missing values.

```
pclass  survived      name       sex       age     sibsp     parch    ticket   0         0          0          0         0        0         0         0          
fare    cabin  embarked      boat      body   home_dest 
  0      0         0           0         0       0
```

The name column holds the name of each person which is a string and it cant be used in KNN. The titanic_df data frame has the home_dest column which is the destination of the people and it’s in the string. The boat column has only that number of boats which had people who were alive otherwise it’s blank. The cabin column has a cabin of the people which was provided for the rich people only and other cabin is unknown. The ticket column gives the ticket number of each person which is different for each person. I will drop these five columns from the data frame.

The embarked column gives Port of Embarkation:

* C = Cherbourg
* Q = Queenstown
* S = Southampton I will create three new features using the emarked data .

```R
library(dummies)
titanic_df <- cbind(titanic_df,dummy(titanic_df$embarked))
head(titanic_df)
```

```
pclass	survived	sex	age	sibsp	parch	fare	embarked	fare_1	embarked_1	titanic_df	titanic_dfC	titanic_dfQ	titanic_dfS
1	TRUE	female	29.0000	0	0	211.3375	S	211.3375	S	0	0	0	1
1	TRUE	male	0.9167	1	2	151.5500	S	151.5500	S	0	0	0	1
1	FALSE	female	2.0000	1	2	151.5500	S	151.5500	S	0	0	0	1
1	FALSE	male	30.0000	1	2	151.5500	S	151.5500	S	0	0	0	1
1	FALSE	female	25.0000	1	2	151.5500	S	151.5500	S	0	0	0	1
1	TRUE	male	48.0000	0	0	26.5500	S	26.5500	S	0	0	0	1
```

```R
titanic_df <- dplyr::select(titanic_df,-embarked)
titanic_df <- dplyr::select(titanic_df,-titanic_df)
titanic_df <- dplyr::select(titanic_df,-sex)
```

The sex column provides the gender of the person I will denote 1 as female and 0 as male which makes it easy to implement in the model.

After cleaning and some manipulation, we have a dataset that is applicable for modelling or classification purpose. Let’s look at the structure of the data.

```
pclass	survived	age	sibsp	parch	fare	embark_C	embark_Q	embark_S	gender
1	TRUE	29.0000	0	0	211.3375	0	0	1	1
1	TRUE	0.9167	1	2	151.5500	0	0	1	0
1	FALSE	2.0000	1	2	151.5500	0	0	1	1
1	FALSE	30.0000	1	2	151.5500	0	0	1	0
1	FALSE	25.0000	1	2	151.5500	0	0	1	1
1	TRUE	48.0000	0	0	26.5500	0	0	1	0
```

### Split the Data

Usually, in Machine Learning Data is divided into two parts, training and testing data. I will divide the data into 70% training and 30% testing data.

```R
set.seed(123)
data <- titanic_df[base::sample(nrow(titanic_df)),] # suffling the data
bound <- floor(0.7 * nrow(data))
df_train <- data[1:bound,]
df_test <- data[(bound+1): nrow(data),]
cat("number of training and test samples are",nrow(df_train), nrow(df_test))
```

* number of training and test samples are 914 392

### Train the Model on Data

I will use K- Nearest Neighbour (knn) algorithm to train our classification model. I will start with k = 1.

```
y_test
knn.pred1 false true
    false   210   60
    true     41   81
Accuracy: 0.7423469
```

```R
knn.pred3<-knn(X_train,X_test,y_train,k =3)
table(knn.pred3, y_test)
```

```
  y_test
knn.pred3 false true
    false   212   57
    true     39   84
```

```R
cat("Accuracy:",mean(y_test==knn.pred3))
```


Accuracy: 0.755102

```R
knn.pred5 <- knn(X_train,X_test,y_train,k = 5)
table(knn.pred5, y_test)
```

```
y_test
knn.pred5 false true
    false   204   56
    true     47   85
```

```R
cat("Accuracy:",mean(y_test==knn.pred5))
```

Accuracy: 0.7372449

```R
knn.pred7 <- knn(X_train,X_test,y_train,k = 20)
table(knn.pred7, y_test)
```

```
 y_test
knn.pred7 false true
    false   222   57
    true     29   84
```
```R
cat("Accuracy:",mean(y_test==knn.pred7))
```

Accuracy: 0.7806122

I kept on increasing the value of k and the best result I found was with K = 20. Further increase in K didn’t improve the performance of the model. So, I got 78% accuracy using the K nearest Neighbour algorithm with k = 20. You can use other algorithms on this problem to get better accuracy.