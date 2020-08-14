# GAFB_-Getting_and_Cleaning_Data
Getting and Cleaning coursera
# GAFB_-Getting_and_Cleaning_Data
Getting and Cleaning coursera

## Get the data 

## Download the data and put in folder data
if(!file.exists("./data")){dir.create("./data")}
fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileUrl,destfile="./data/Dataset.zip",method="curl")

##Unzip the file

unzip(zipfile="./data/Dataset.zip",exdir="./data")

## unzip the file from UCI HAR dataset and get a list of files


path_rf <- file.path("./data" , "UCI HAR Dataset")
files<-list.files(path_rf, recursive=TRUE)
files

## Reading data from the files

##Read activity files

dataActivityTest  <- read.table(file.path(path_rf, "test" , "Y_test.txt" ),header = FALSE)
dataActivityTrain <- read.table(file.path(path_rf, "train", "Y_train.txt"),header = FALSE)

##Read subject files
dataSubjectTrain <- read.table(file.path(path_rf, "train", "subject_train.txt"),header = FALSE)
dataSubjectTest  <- read.table(file.path(path_rf, "test" , "subject_test.txt"),header = FALSE)

##Read features files
dataFeaturesTest  <- read.table(file.path(path_rf, "test" , "X_test.txt" ),header = FALSE)
dataFeaturesTrain <- read.table(file.path(path_rf, "train", "X_train.txt"),header = FALSE)

## Review the properties of the varaibles (activity, subject and features)

##Activity properties
str(dataActivityTest)
str(dataActivityTrain)


##Subject properties
str(dataSubjectTrain)
str(dataSubjectTest)

##Features properties
str(dataFeaturesTest)
str(dataFeaturesTrain)


##1. Merges the training and the test sets to create one data set.

## Concatenate files by rows
dataSubject <- rbind(dataSubjectTrain, dataSubjectTest)
dataActivity<- rbind(dataActivityTrain, dataActivityTest)
dataFeatures<- rbind(dataFeaturesTrain, dataFeaturesTest)

##Set names of variables
names(dataSubject)<-c("subject")
names(dataActivity)<- c("activity")
dataFeaturesNames <- read.table(file.path(path_rf, "features.txt"),head=FALSE)
names(dataFeatures)<- dataFeaturesNames$V2

##Merge Columns 
dataCombine <- cbind(dataSubject, dataActivity)
Data <- cbind(dataFeatures, dataCombine)


## 2.Extracts only the measurements on the mean and standard deviation for each measurement

subdataFeaturesNames<-dataFeaturesNames$V2[grep("mean\\(\\)|std\\(\\)", dataFeaturesNames$V2)]

selectedNames<-c(as.character(subdataFeaturesNames), "subject", "activity" )
Data<-subset(Data,select=selectedNames)

str(Data)

## 3. Uses descriptive activity names to name the activities in the data set

##Read from txt file 
activityLabels <- read.table(file.path(path_rf, "activity_labels.txt"),header = FALSE)

##Check 

head(Data$activity,30)


##4 Appropriately labels the data set with descriptive variable names
## t replaces time
##f replaces frecuency
## A replaces Accelerometer
## G replaces Gyroscope
## M replace Magnitude
## B replace Body
names(Data)<-gsub("^t", "time", names(Data)) 
names(Data)<-gsub("^f", "frequency", names(Data))  
names(Data)<-gsub("Acc", "Accelerometer", names(Data)) 
names(Data)<-gsub("G", "Gyroscope", names(Data))  
names(Data)<-gsub("M", "Magnitude", names(Data))  
names(Data)<-gsub("B", "Body", names(Data))  

##5 creates a second, independent tidy data set with the average of each variable for each activity and each subject

library(plyr);
Data2<-aggregate(. ~subject + activity, Data, mean)
Data2<-Data2[order(Data2$subject,Data2$activity),]
write.table(Data2, file = "tidydata.txt",row.name=FALSE)

## Generate codebook
library(knitr)
knit2html("codebook.Rmd")


