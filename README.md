# run_analysis
The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set.
# 1. Merges the training and the test sets to create one data set.
# Load necessary libraries
library(dplyr)

# Load the data from UCI HAR Dataset directory
train_data <- read.table("./UCI HAR Dataset/train/X_train.txt")
test_data <- read.table("./UCI HAR Dataset/test/X_test.txt")

# Load the activity and subject data
train_activity <- read.table("./UCI HAR Dataset/train/y_train.txt")
test_activity <- read.table("./UCI HAR Dataset/test/y_test.txt")
train_subject <- read.table("./UCI HAR Dataset/train/subject_train.txt")
test_subject <- read.table("./UCI HAR Dataset/test/subject_test.txt")

# Merge training and test datasets
data_combined <- rbind(train_data, test_data)
activity_combined <- rbind(train_activity, test_activity)
subject_combined <- rbind(train_subject, test_subject)

# 2. Extracts only the measurements on the mean and standard deviation for each measurement.
# Load feature labels
features <- read.table("./UCI HAR Dataset/features.txt")

# Identify columns that are mean and std
mean_std_features <- grep("-(mean|std)\\(\\)", features$V2)

# Subset the data to only include the mean and std columns
data_mean_std <- data_combined[, mean_std_features]

# 3. Uses descriptive activity names to name the activities in the data set
# Load activity labels
activity_labels <- read.table("./UCI HAR Dataset/activity_labels.txt")

# Replace activity codes with descriptive names
activity_combined$V1 <- factor(activity_combined$V1, levels = activity_labels$V1, labels = activity_labels$V2)

# 4. Appropriately labels the data set with descriptive variable names
# Assign column names from the features list
names(data_mean_std) <- features$V2[mean_std_features]

# Add subject and activity data to the dataset
final_data <- cbind(subject_combined, activity_combined, data_mean_std)

# Assign descriptive column names to subject and activity
names(final_data)[1:2] <- c("Subject", "Activity")

# 5. Creates a second, independent tidy data set with the average of each variable for each activity and each subject.
tidy_data <- final_data %>%
  group_by(Subject, Activity) %>%
  summarise(across(starts_with("tBodyAcc"), mean), .groups = 'drop')

# Write the tidy data set to a file
write.table(tidy_data, "tidy_data.txt", row.name = FALSE)

