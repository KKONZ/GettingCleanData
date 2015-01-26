##*Course Project*
=================

>Date Downloaded _January 25th 2015_

*Rule 1* Merge data, these steps highlight merging the data tables together
```{r}
xTest<- read.csv("./data/UCI HAR dataset/test/X_test.txt",header=FALSE,sep="",comment.char="",colClasses="numeric")
yTest<- read.csv("./data/UCI HAR dataset/test/y_test.txt",header=FALSE,sep="")
subTest<- read.csv("./data/UCI HAR dataset/test/subject_test.txt",header=FALSE,sep="")
xTrain<- read.csv("./data/UCI HAR dataset/train/X_train.txt",header=FALSE,sep="",comment.char="",colClasses="numeric")
yTrain<- read.csv("./data/UCI HAR dataset/train/y_train.txt",header=FALSE,sep="")
subTrain<-read.csv("./data/UCI HAR dataset/train/subject_train.txt",header=FALSE,sep="")
CombineTest<- cbind(xTest,yTest,subTest)
CombineTrain<- cbind(xTrain, yTrain, subTrain)
CompleteData<- rbind(CombineTest, CombineTrain)

```
*Rule 2* Extracts only the measurements on the mean and standard deviation for each measurement. 
```{r}
colNames<-grepl("mean\\(\\)|std\\(\\)|activity|subject", names(CompleteData))
CompleteData<-CompleteData[,colNames]
```

*Rule 3* Uses descriptive activity names to name the activities in the data set
```{r}
names(yTest)<-"activity.labels"
names(subTest)<- "subjectID"

names(yTrain)<-"activity.labels"
names(subTrain)<- "subjectID"

names(CompleteData)<- c(as.character(varNames$V2),"activity.labels","subjectID")

CompleteData$activity.labels<-as.factor(CompleteData$activity.labels)
levels(CompleteData$activity.labels)<- list(WALKING="1",WALKING_UPSTAIRS="2", WALKING_DOWNSTAIRS="3", SITTING="4", STANDING="5",LAYING="6")


```
*Rule 4* Appropriately labels the data set with descriptive variable names. 
```{r}
names(CompleteData)<- c("tBodyAcc_meanX","tBodyAcc_meanY","tBodyAcc_meanZ","tBodyAcc_stdX","tBodyAcc_stdY","tBodyAcc_stdZ","tGravityAcc_meanX","tGravityAcc_meanY","tGravityAcc_meanZ","tGravityAcc_stdX"
                        ,"tGravityAcc_stdY","tGravityAcc_stdZ","tBodyAccJerk_meanX","tBodyAccJerk_meanY","tBodyAccJerk_meanZ","tBodyAccJerk_stdX","tBodyAccJerk_stdY","tBodyAccJerk_stdZ","tBodyGyro_meanX","tBodyGyro_meanY" 
                        ,"tBodyGyro_meanZ","tBodyGyro_stdX","tBodyGyro_stdY","tBodyGyro_stdZ","tBodyGyroJerk_meanX","tBodyGyroJerk_meanY","tBodyGyroJerk_meanZ","tBodyGyroJerk_stdX","tBodyGyroJerk_stdY","tBodyGyroJerk_stdZ" 
                        ,"tBodyAccMag_mean","tBodyAccMag_std","tGravityAccMag_mean","tGravityAccMag_std","tBodyAccJerkMag_mean","tBodyAccJerkMag_std","tBodyGyroMag_mean","tBodyGyroMag_std","tBodyGyroJerkMag_mean","tBodyGyroJerkMag_std"
                        ,"fBodyAcc_meanX","fBodyAcc_meanY","fBodyAcc_meanZ","fBodyAcc_stdX","fBodyAcc_stdY","fBodyAcc_stdZ","fBodyAccJerk_meanX","fBodyAccJerk_meanY","fBodyAccJerk_meanZ","fBodyAccJerk_stdX"
                        ,"fBodyAccJerk_stdY","fBodyAccJerk_stdZ","fBodyGyro_meanX","fBodyGyro_meanY","fBodyGyro_meanZ","fBodyGyro_stdX","fBodyGyro_stdY","fBodyGyro_stdZ","fBodyAccMag_mean","fBodyAccMag_std"
                        ,"fBodyAccJerkMag_mean","fBodyAccJerkMag_std","fBodyGyroMag_mean","fBodyGyroMag_std","fBodyGyroJerkMag_mean","fBodyGyroJerkMag_std","activity","subjectID" )

```

*Rule 5* From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
```{r}
Melt<-melt(CompleteData, id=c("subjectID","activity")) 
TidyData<- dcast(Melt, subjectID+activity ~ variable,fun.aggregate=mean)

CleanData<- dcast(Melt,subjectID+activity+variable~...,fun.aggregate=mean)
write.table(CleanData, "TidyData.txt",append = TRUE,quote=FALSE,sep=" ",dec=".",row.names=FALSE,col.names=c("ID","ActType","VarMeas","Mean"))


```
Code Book: *ID: is the identifying number for the 30 participants *ActType: is the activity that the participant was recorded doing *VarMeas: is the variable that was measured *Mean: is the average for the given activity

## This section summarizes the data and how it was collected
==================================================================
Human Activity Recognition Using Smartphones Dataset
Version 1.0
==================================================================
Jorge L. Reyes-Ortiz, Davide Anguita, Alessandro Ghio, Luca Oneto.
Smartlab - Non Linear Complex Systems Laboratory
DITEN - Università degli Studi di Genova.
Via Opera Pia 11A, I-16145, Genoa, Italy.
activityrecognition@smartlab.ws
www.smartlab.ws
==================================================================

The experiments have been carried out with a group of 30 volunteers within an age bracket of 19-48 years. Each person performed six activities (WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, LAYING) wearing a smartphone (Samsung Galaxy S II) on the waist. Using its embedded accelerometer and gyroscope, we captured 3-axial linear acceleration and 3-axial angular velocity at a constant rate of 50Hz. The experiments have been video-recorded to label the data manually. The obtained dataset has been randomly partitioned into two sets, where 70% of the volunteers was selected for generating the training data and 30% the test data. 

The sensor signals (accelerometer and gyroscope) were pre-processed by applying noise filters and then sampled in fixed-width sliding windows of 2.56 sec and 50% overlap (128 readings/window). The sensor acceleration signal, which has gravitational and body motion components, was separated using a Butterworth low-pass filter into body acceleration and gravity. The gravitational force is assumed to have only low frequency components, therefore a filter with 0.3 Hz cutoff frequency was used. From each window, a vector of features was obtained by calculating variables from the time and frequency domain. See 'features_info.txt' for more details. 

For each record it is provided:
======================================

- Triaxial acceleration from the accelerometer (total acceleration) and the estimated body acceleration.
- Triaxial Angular velocity from the gyroscope. 
- A 561-feature vector with time and frequency domain variables. 
- Its activity label. 
- An identifier of the subject who carried out the experiment.

The dataset includes the following files:
=========================================

- 'README.txt'

- 'features_info.txt': Shows information about the variables used on the feature vector.

- 'features.txt': List of all features.

- 'activity_labels.txt': Links the class labels with their activity name.

- 'train/X_train.txt': Training set.

- 'train/y_train.txt': Training labels.

- 'test/X_test.txt': Test set.

- 'test/y_test.txt': Test labels.

The following files are available for the train and test data. Their descriptions are equivalent. 

- 'train/subject_train.txt': Each row identifies the subject who performed the activity for each window sample. Its range is from 1 to 30. 

- 'train/Inertial Signals/total_acc_x_train.txt': The acceleration signal from the smartphone accelerometer X axis in standard gravity units 'g'. Every row shows a 128 element vector. The same description applies for the 'total_acc_x_train.txt' and 'total_acc_z_train.txt' files for the Y and Z axis. 

- 'train/Inertial Signals/body_acc_x_train.txt': The body acceleration signal obtained by subtracting the gravity from the total acceleration. 

- 'train/Inertial Signals/body_gyro_x_train.txt': The angular velocity vector measured by the gyroscope for each window sample. The units are radians/second. 

Notes: 
======
- Features are normalized and bounded within [-1,1].
- Each feature vector is a row on the text file.

For more information about this dataset contact: activityrecognition@smartlab.ws

License:
========
Use of this dataset in publications must be acknowledged by referencing the following publication [1] 

[1] Davide Anguita, Alessandro Ghio, Luca Oneto, Xavier Parra and Jorge L. Reyes-Ortiz. Human Activity Recognition on Smartphones using a Multiclass Hardware-Friendly Support Vector Machine. International Workshop of Ambient Assisted Living (IWAAL 2012). Vitoria-Gasteiz, Spain. Dec 2012

This dataset is distributed AS-IS and no responsibility implied or explicit can be addressed to the authors or their institutions for its use or misuse. Any commercial use is prohibited.

Jorge L. Reyes-Ortiz, Alessandro Ghio, Luca Oneto, Davide Anguita. November 2012.

### This is from the Coursera Data Science Getting Clean Data Assignment page

The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. You will be graded by your peers on a series of yes/no questions related to the project. You will be required to submit: 1) a tidy data set as described below, 2) a link to a Github repository with your script for performing the analysis, and 3) a code book that describes the variables, the data, and any transformations or work that you performed to clean up the data called CodeBook.md. You should also include a README.md in the repo with your scripts. This repo explains how all of the scripts work and how they are connected.  

One of the most exciting areas in all of data science right now is wearable computing - see for example this article . Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site where the data was obtained: 

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones 

Here are the data for the project: 

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip 

 You should create one R script called run_analysis.R that does the following. 
Merges the training and the test sets to create one data set.
Extracts only the measurements on the mean and standard deviation for each measurement. 
Uses descriptive activity names to name the activities in the data set
Appropriately labels the data set with descriptive variable names. 
From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

Good luck!


