################################################################################

This project uses the programming language R to analyse data collected from the
accelerometer and gyroscope of the Samsung Galaxy S II smart-phone. The data and 
details regarding the study for which the data were collected are available from: 

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

The zip file containing the data used for this R programming project were obtained from:

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

The analysis process consisted of: downloading several files of preprocessed data; 
reading each of these files into an R data-frame; merging the data frames together; 
extracting only the requested measurements of "mean" and "standard deviation"; 
obtaining the average of each of these values for  each activity of each participant.

There were 30 participants, identified by "Subject_ID"" in the data and 6 activities,
identified by "Activity_Name" or "Activity_ID". "Activity_Name" is a verbal description
of the activity while "Activity_ID" is a numerical identifier.

The procedure for accomplishing the analysis process with R is described in the 
accompanying CodeBook.

The script for running the analysis is  run_analysis.R

The final product is the file entitled SummaryTable.txt.

################################################################################

################################################################################

# Project Instructions

# A. You should create one R script called run_analysis.R that does the following.
        # 1. Merges the training and the test sets to create one data set.
        # 2. Extracts only the measurements on the mean and standard deviation for 
        #    each measurement. 
        # 3. Uses descriptive activity names to name the activities in the data 
        #    set.
        # 4. Appropriately labels the data set with descriptive variable names. 
        # 5. From the data set in step 4, creates a second, independent tidy data 
        #    set with the average of each variable for each activity and each 
        #    subject.
# B. Submit a link to a Github repo with the code for performing your 
        #    analysis. The code should have a file run_analysis.R in the main 
        #    directory that can be run as long as the Samsung data is in your 
        #    working directory. The output should be the tidy data set you 
        #    submitted for part 1. You should include a README.md in the repo 
        #    describing how the script works and the code book describing the 
        #    variables.

################################################################################

# Rubric

# Has the student submitted a tidy data set? Either a wide or a long form of the
        # data is acceptable if it meets the tidy data principles of week 1 (Each
        # variable you measure should be in one column, Each different observation
        # of that variable should be in a different row).

# Did the student submit a Github repo with the required scripts?

# Was code book submitted to GitHub that modifies and updates the codebooks 
        # available to you with the data to indicate all the variables and 
        # summaries you calculated, along with units, and any other relevant 
        # information?

# I was able to follow the README in the directory that explained what the 
        # analysis files did. 

# As far as you can determine, does it appear that the work submitted for this 
        # project is the work of the student who submitted it? 

################################################################################

# Prelim Analyis

# From ReadMe.txt
        # Num of subjects = 30
        # Num of activites per subjet = 6
                # WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, 
                # STANDING, LAYING
        # Num of type of measurements per activity = 2
                # axial linear acceleration and axial angular velocity
        # Num of measurment direction per type of measurement = 3
                # probably x, y, z
        # Num of type+direction measurements obtained per second = 50
        # Total measurements type per subject (observations) = 6*2*3 = 24
        # Total measurements per second = 24*50 = 1,200
        # Total subjects (observations) = 30
        # Total measuremets type in all (observations) = 6*2*3*30 = 720
        # Total measures all obsv per sec = 6*2*3*50*30 = 36,000
        # Total measures all obsv per sec = 6*2*3*50*30 = 36,000
        # Training set = 70% (21 subjects, 25,200 measures/sec)
        # Test set = 30% (9 subjects, 10,800/sec)         

# Notes on Project Instructions
        # A.2
                # It appears there are 53 (of 561 total) different measure for
                # mean based on file 'features.txt'
                # and 33 for std (standard deviation)
                # total num to extract is 88

# more from ReadMe.txt
        # For each record it is provided:

        # - Triaxial acceleration from the accelerometer (total acceleration) 

                # eg. total_acc_x_test.txt, ...y_test.tst ...z_test.txt

                        # +/- 128 measures per group/line; 2948 groups/lines

        # and the estimated body acceleration.

                # eg. body_acc_x_test.txt, ...y_test.tst ...z_test.txt

                        # +/- 128 measures per group/line; 2948 groups/lines
                        # at least one has 138 measures

        # - Triaxial Angular velocity from the gyroscope. 

                # eg. body_gyro_x_test.txt,...y_test.tst ...z_test.txt 

                        # 128 measures per group/line 2948 groups/ines

        # - A 561-feature vector with time and frequency domain variables. 

                # eg. X_test.txt

                        # 561 measures per group/line 2947 groups/ines
                        # the definition of these variables is in 'features.txt'

        # - Its activity label. 

                # eg. y_test.txt

                        # there are 6 diffent labels (1:6) and these are mostly
                        # in groups of 24 (+/-1) (one has 33)

        # - An identifier of the subject who carried out the experiment.

                # eg. subject_test.txt

                        # 9 subjects, 2947 total observations, 327.44 obs/sub

# and some more from ReadMe.txt
        # The dataset includes the following files:

        # - 'README.txt'

        # - 'features_info.txt': Shows information about the variables used on 
        # the feature vector.

        # - 'features.txt': List of all features.

        # - 'activity_labels.txt': Links the class labels with their activity name.

        # - 'train/X_train.txt': Training set.

        # - 'train/y_train.txt': Training labels.

        # - 'test/X_test.txt': Test set.

        # - 'test/y_test.txt': Test labels.

# Notes from 'features_info.txt'

        # a 't' prefix indicates a time measure; 'f' prefix indicates frequency

        # it seems the data in the Inertia Signals files may be the raw signals
        # obtaied based on this statement in 'features_info.txt': "The features 
        # selected for this database come from the accelerometer and gyroscope 
        #3-axial raw signals tAcc-XYZ and tGyro-XYZ"              

# Example files: 
        # body_acc_x_test
                # total num of measures = aprox. 377,216
                # total num of measures/group = 128
                # total num of groups = 2947
        # body_acc_y_test
                # total num of measures = aprox. 14,740
        # body_gyro_z_test
                # total num of measures = aprox. 14,740
        # X_train.txt
                # this is the largest file, it is 64,460 KB, 561 measures per 
                # group/line 2947 groups/ines or 1,653,267 total data points 
                # so will probably be the most problem to read in and work with
                # may need to use fread() to read it in and if do that should
                # maybe use that for reading in everything
                # no do't use fread() it has a bug
                # used read took ~ 20 seconds
                # don't use read.fwf - is problematic
                # read.tableshould be fine

################################################################################

# Summary of files to include

# for this will look at each data file individually and develop a basic
# summary (dimensions, num. of variables, etc.)

        # features_info.txt - informational about features

        # main file
        # activity_labels.txt - list of actvity names and num's representing them
                # dim = 6 lines; 6r x 2c (space seperator)
        # features.txt - list of each measurement type and num represnetingit
                # dim = 561 lines; 561r x 2c (space seperator)

        # test file
        # subject_test.txt
                # dim = 2947 lines; 2947r x 561c (space seperator) 
        # X_test.txt
        # y_test.txt

        
################################################################################