# Getting and Cleaning Data Project

The purpose of this project is to demonstrate the ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. Required to submit is: 1) a tidy data set, 2) a link to a Github repository with the script for performing the analysis, and 3) a code book that describes the variables, the data, and any transformations or work that performed to clean up the data called CodeBook.md. A README.md file must be included in the repo. This repo explains how all of the scripts work and how they are connected.

The run_analysis.R file does the following:
1. Merges the training and the test sets to create one data set.<br>
   data.all <- rbind(data.train, data.test)

2. Extracts only the measurements on the mean and standard deviation for each measurement.<br>
   mean_std.select <- grep('mean|std', features)<br>
   data.sub <- data.all[,c(1,2,mean_std.select + 2)]

3. Uses descriptive activity names to name the activities in the data set<br>
   activity.labels <- read.table('./UCI HAR Dataset/activity_labels.txt', header = FALSE)<br>
   activity.labels <- as.character(activity.labels[,2])<br>
   data.sub$activity <- activity.labels[data.sub$activity]

4. Appropriately labels the data set with descriptive variable names.<br>
   name.new <- names(data.sub)<br>
   name.new <- gsub("[(][)]", "", name.new)<br>
   name.new <- gsub("^t", "TimeDomain_", name.new)<br>
   name.new <- gsub("^f", "FrequencyDomain_", name.new)<br>
   name.new <- gsub("Acc", "Accelerometer", name.new)<br>
   name.new <- gsub("Gyro", "Gyroscope", name.new)<br>
   name.new <- gsub("Mag", "Magnitude", name.new)<br>
   name.new <- gsub("-mean-", "_Mean_", name.new)<br>
   name.new <- gsub("-std-", "_StandardDeviation_", name.new)<br>
   name.new <- gsub("-", "_", name.new)<br>
   names(data.sub) <- name.new

5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.<br>
   data.tidy <- aggregate(data.sub[,3:81], by = list(activity = data.sub$activity, subject = data.sub$subject), FUN = mean)<br>
   write.table(x = data.tidy, file = "tidy_data.txt", row.names = FALSE)
