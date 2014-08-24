### Description on how run_analysis.R works

First the script makes sure some dependencies are met. For instance: require("data.table"). We obviously don't have to create our working-directory every time, so the script checks if it already exists. If not, it create it: dir.create("C:\\data")

Then the script sets the working directory and specify the URL of the orginal data set. If the data-set haven't been downloaded already, the script will do a fresh download. This was for testing, when the script was run several times in order to shorten the run time.

When the file was downloaded or existed it will be unzipped with default settings. The next step is to read the meta data from the dataset. Now the script begins to read the original files, beginning with the features.txt file which contains column names. In the next step it extracts only the second column, so we actually just have the column names, and applies it to the variable.

Now the script begins to read the activity_labels.txt file, which contains the correct labels for the activity numbers used in the dataset. Like before the script only extracts the second column again, and reapplies.

In the following steop the script reads in the first test data set from the file named X_text.txt. Since we already read the meta data, the script also assures that a proper column names gets applied:  colnames(data_test_x) <- col_names

Now the scipt reads the next dataset from the file y_test.txt and again it makes sure this data set gets a proper label assigned as well - we name it "Activity". Now we read the last dataset from the test data from the file subject_test.txt. The scipt also applies a proper column name here and calls it "Subject".

The whole process of reading in the test data will be repeated once more for the training dataset, which can be found in the following files: X_text.txt, y_train.txt and subject_train.txt

The next big step is about merging the data from the test and training dataset together in one big dataset. The script starts by stitching together all three test data sets using the cbind command. The same process will be done for the training data sets. Now we have one big training and one big test data set. At the very end we use the rbind command to stitch both data sets together to one big data set, which we call all_data.

As we are only interested in the coloumns that represent mean-values and standard-deviation, we subset these columns from our entire data set. Then we aggregate the relevant data by Subject and Actitvity. Before we can write the new tiday data set to a file, we need to replace the activity codes with acutal meaningful names. For this we use the file activity_names which we read before and apply it.

At the end we get one output file called tidy_data_set.txt.

For more information please read the CodeBook.md file.
