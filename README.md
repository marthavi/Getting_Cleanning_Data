# Getting_Cleanning_Data
Assignment week 4

# 1. Merges the training and the test sets to create one data set.
## First I uploaded the X_train data and features data in order to assign the labels for each meassurement. 
x_train <- read.table("./UCI HAR Dataset/train/X_train.txt")
View(x_train)
label <- read.table("./UCI HAR Dataset/features.txt")
View(label)
names(x_train) <- label$V2 
View(x_train)

## Second, I uploaded the y_train and s_train data to merge all train data using the function cbind

y_train <- read.table("./UCI HAR Dataset/train/y_train.txt", sep = "\t", col.names = "activity")
View(y_train)

s_train <- read.table("./UCI HAR Dataset/train/subject_train.txt", sep = "\t", col.names = "subject")
View(s_train)

dt_train <- cbind(y_train, s_train, x_train)
View(dt_train)

## then, I did the same procedure (three steps) for test data: up load the data, label it and bind it.

x_test <- read.table("./UCI HAR Dataset/test/X_test.txt")
names(x_test) <- label$V2 
View(x_test)

y_test <- read.table("./UCI HAR Dataset/test/y_test.txt", sep = "\t", col.names = "activity")
View(y_test)

s_test <- read.table("./UCI HAR Dataset/test/subject_test.txt", sep = "\t", col.names = "subject")
View(s_test)

dt_test <- cbind(y_test, s_test, x_test) 
View(dt_test)

## Finally, in order to merge both (test and train data) I used a rbind function. It returned 10299 rows and 563 columns
test_train <- rbind(dt_test, dt_train)
View(test_train)


# 2. Extracts only the measurements on the mean and standard deviation for each measurement.
## I've created a new variable that storage the query made to look for the names that contain mean or std in the name. For this I used grep.

d <- test_train[, c(1:2,grep("mean\\(\\)|std\\(\\)", names(test_train)))]
View(d)


# 3. Uses descriptive activity names to name the activities in the data set
## For this was necessary to convert numbers in factors. 

activity_labels <- read.table("./UCI HAR Dataset/activity_labels.txt")
View(activity_labels)

d$activity<-factor(d$activity);
d$activity<- factor(d$activity,labels=as.character(activity_labels$V2))


# 4. Appropriately labels the data set with descriptive variable names.
## gsub allow to change the names as required

names(d) <- gsub("^t", "time", names(d))
names(d) <- gsub("^f", "frequency", names(d))
names(d) <- gsub("Acc", "Accelerometer", names(d))
names(d) <- gsub("Gyro", "Gyroscope", names(d))
names(d) <- gsub("Mag", "Magnitude", names(d))
names(d) <- gsub("BodyBody", "Body", names(d))

names(d)

# 5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
## Finally aggregate and order was used to apply the mean to each activity depending of the subject and then to sort the variable first from subject and then from activity 

library(plyr)
tidydata <- aggregate(. ~subject + activity, d, mean)
tidydata <- tidydata[order(tidydata$subject,tidydata$activity),]
View(tidydata)

write.table(tidydata, file = "tidydata.txt",row.name=FALSE)
