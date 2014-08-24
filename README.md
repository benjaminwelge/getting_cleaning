### Description on how run_analysis.R works

run_analysis <- function(){

	###############################
	## This script accomplishes the following tasks:
	###############################
	## 1. Merges the training and the test sets to create one data set.
	## 2. Extracts only the measurements on the mean and standard deviation for each measurement. 
	## 3. Uses descriptive activity names to name the activities in the data set
	## 4. Appropriately labels the data set with descriptive variable names. 
	## 5. Creates a second, independent tidy data set with the average of each variable for each activity and each subject.

	## making sure some dependencies are met
	require("data.table")

	## we obviously don't have to create the directory every time - so we check if it already exists. If not we create it
	if(!file.exists("C:\\data")){

		dir.create("C:\\data")
	}
	
	## here we set the working directory and specify the URL of the orginal data set	
	setwd("C:\\data\\")
      fileUrl = "http://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
	
	## if we haven't downloaded the dataset already, we download it. This was for testing, when the script was run several times
	if(!file.exists("C:\\data\\UCI_HAR_Dataset.zip")){

		download.file(fileUrl, destfile = "./UCI_HAR_Dataset.zip", method = "auto")
	}

	## When the file is downloaded or already existed, we unzip it with standard settings
	unzip("./UCI_HAR_Dataset.zip")

	########################
	## Read in the meta-data
	########################
	## now we begin to read the original files, beginning with the features.txt file which contains most column names
	col_labels <- read.table("C:\\data\\UCI HAR Dataset\\features.txt", header = FALSE)
      
	## now extract only the second column, so we actually just have the column names
	col_names <- col_labels$V2
	
	## now we begin to read the activity_labels.txt file, which contains the correct labels for the activity numbers used in the dataset
	activity_labels <- read.table("C:\\data\\UCI HAR Dataset\\activity_labels.txt", header = FALSE)
	
	## now we extract only the second column again, so we actually just have the column names
      activity_names <- activity_labels$V2

	#############################
	## Read in the actual user data from test dataset
	#############################
	## now we read in the first test data set from the file X_text.txt
	data_test_x <- read.table("C:\\data\\UCI HAR Dataset\\test\\X_test.txt", header = FALSE)
	
	## we also apply the proper column names directly as we already read them from the meta data files
	colnames(data_test_x) <- col_names
	
	## now we read the next dataset from the file y_test.txt
	data_test_y <- read.table("C:\\data\\UCI HAR Dataset\\test\\y_test.txt", header = FALSE)
	
	## again we make sure this data set gets a proper label as well - we name it "Activity"
      colnames(data_test_y) <- c("Activity")
	
	## now we read the last dataset from the test data from the file subject_test.txt
	data_test_subject <- read.table("C:\\data\\UCI HAR Dataset\\test\\subject_test.txt", header = FALSE)
	
	## we also apply a proper column name here and call it "Subject"
	colnames(data_test_subject) <- c("Subject")

	##############################
	## Read in the actual user data from training dataset
	##############################
	## now we read in the first training data set from the file X_text.txt
	data_train_x <- read.table("C:\\data\\UCI HAR Dataset\\train\\X_train.txt", header = FALSE)

	## we also apply the proper column names directly as we already read them from the meta data files
	colnames(data_train_x) <- col_names

	## now we read the next dataset from the file y_train.txt
	data_train_y <- read.table("C:\\data\\UCI HAR Dataset\\train\\y_train.txt", header = FALSE)
	
	## again we make sure this data set gets a proper label as well - we name it "Activity"
	colnames(data_train_y) <- c("Activity")

	## now we read the last dataset from the training dataset from the file subject_train.txt
	data_train_subject <- read.table("C:\\data\\UCI HAR Dataset\\train\\subject_train.txt", header = FALSE)
	
	## we also apply a proper column name here and call it "Subject"
	colnames(data_train_subject) <- c("Subject")

	###############################
	## Data Merging
	###############################
	## After read each data file we are stitching it all together, we start by stitching the complete test dataset together
	data_test_x <- cbind(data_test_x, data_test_y)
	data_test_x <- cbind(data_test_x, data_test_subject)

	## now we stich all training data together in one table
	data_train_x <- cbind(data_train_x, data_train_y)
	data_train_x <- cbind(data_train_x, data_train_subject)

	## at the end we merge the test datset with the training dataset
	all_data <- rbind(data_test_x, data_train_x)
 
	###############################
	## Subsetting, Aggregation and Export
	###############################
 	
	## we are only interested in the mean and standard-deviation columns of the data    
	mean_and_std <- grepl("mean\\(\\)|std\\(\\)", col_labels$V2)
	all_data <- all_data[,mean_and_std]

	## Now we aggregate the relevant data by Subject and Activity and calculate means
	all_agg <- aggregate(. ~ Subject + Activity, data = all_data, FUN = mean)
	## Before exporting the tidy dataset we replace the activity codes with readable labels
	all_agg$Activity <- factor(all_agg$Activity, labels = activity_names)

	## now we write tidy dataset into a new file and format it nicely
      write.table(all_agg, file="./tidy_data_set.txt", sep="\t", row.names=FALSE)
}
