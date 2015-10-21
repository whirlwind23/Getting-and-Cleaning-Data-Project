# Getting-and-Cleaning-Data-Project
This repo contains the assignment from coursera course Getting and Cleaning data from Data Science Specialization.

Assuming that data from url (https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip) has been downloaded and zip file extracted to your home directory.

##Step 1

Reading the features file containing column names for data collected and Activity_labels.txt contains labels for each activity

```
features<-read.table("./UCI HAR Dataset/features.txt")
activity_labels<-read.table("./UCI HAR Dataset/activity_labels.txt")
```

Reading the test and train datasets

```
X_test<-read.table("./UCI HAR Dataset/test/X_test.txt")
y_test<-read.table("./UCI HAR Dataset/test/y_test.txt")
subject_test<-read.table("./UCI HAR Dataset/test/subject_test.txt")
X_train<-read.table("./UCI HAR Dataset/train/X_train.txt")
y_train<-read.table("./UCI HAR Dataset/train/y_train.txt")
subject_train<-read.table("./UCI HAR Dataset/train/subject_train.txt")
```

Renaming names for activity id's and subject identification columns

```
colnames(y_train)<-"activitylabels"
colnames(y_test)<-"activitylabels"
colnames(subject_train)<-"subject"
colnames(subject_test)<-"subject"
```

Adding column names from features file to X files

```
colnames(X_train)<- features[,2]
colnames(X_test)<- features[,2]
```

Merging datasets

```
test_dat<-cbind(subject_test,y_test,X_test)
train_dat<-cbind(subject_train,y_train,X_train)
```

Merging test and train data

`all_data<-rbind(test_dat,train_dat) `


## Step2 
Extracting mean and std columns from the data while keeping activity and subject columns

```
meanstdcols<-grep(".*Mean.*|.*Std.*", names(all_data), ignore.case=TRUE)
step2data<-all_data[,c(1,2,meanstdcols)]
```

## Step3 
Converting code in activity_label columns to actual names from activity_labels.txt by using a For loop

`step2data$activitylabels<-as.character(step2data$activitylabels)`

``` 
for(i in 1:6){
  step2data$activitylabels[step2data$activitylabels==i]<-as.character(activity_labels[i,2])}
step2data$activitylabels<-as.factor(step2data$activitylabels)
step2data$subject<-as.factor(step2data$subject)
```


## Step4 
An attempt to parse short names into descriptive labels using gsub

```
names(step2data)<-gsub("^t","Time",names(step2data))
names(step2data)<-gsub("Acc","Accelerometer",names(step2data))
names(step2data)<-gsub("Gyro","Gyroscope",names(step2data))
names(step2data)<-gsub("-mean()"," Mean",names(step2data))
names(step2data)<-gsub("-std()"," St Deviation",names(step2data))
names(step2data)<-gsub("^f","Frequency",names(step2data))
names(step2data)<-gsub("Mag","Magnitude",names(step2data))
names(step2data)<-gsub("angle","Angle",names(step2data))
names(step2data)<-gsub("gravity","Gravity",names(step2data))
``` 

## Step 5 
Creating a crosstable with values of all columns as means for all subjects(30) and 6 different activities

` tidydataset<-aggregate(.~subject+activitylabels,step2data,mean)`

tidy data saved on a file- tidy.txt

` write.table(tidydataset, file = "tidy.txt", row.names = FALSE)`

