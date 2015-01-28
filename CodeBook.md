################################################################################
CodeBook

To run the analysis place a sub-directory called 'data' in the same directory as
run_analysis.R . The 'data' directory should contain all the files required for
analysis that were obtained in the Samsung data set available for this link:

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

The required files are:

'subject_test.txt'
'subject_train.txt'
'X_test.txt'
'X_train.txt'
'activity_labels.txt'
'features.txt'
'y_test.txt'
'y_train.txt'

Following is the code used in the project along with explanations of methods used.

################################################################################

# Load libraries #
library(dplyr)
library(tidyr)

# Load helper functions #
readData2 <- function(file_to_read){             
        thisFile <- paste0('./data/',file_to_read,
                           collapse=NULL)
        thisDF <- read.table(thisFile, header = FALSE, sep = "")     
}

# read and return as df, all the data files required for analysis
subjTestDF <- readData2('subject_test.txt')
subjTrainDF <- readData2('subject_train.txt')
xTestDF <- readData2('X_test.txt')
xTrainDF <- readData2('X_train.txt')
activitiesDF <- readData2('activity_labels.txt')
featuresDF <- readData2('features.txt')
yTestDF <- readData2('y_test.txt')
yTrainDF <- readData2('y_train.txt')

# Rename column names of subjTest/TrainDF and yTrain/TestDF
names(subjTrainDF) <- "Subject_ID"
names(subjTestDF) <- "Subject_ID"
names(yTrainDF) <- "Activity_ID"
names(yTestDF) <- "Activity_ID"
names(activitiesDF) <- c("Activity_ID", "Activity_Name")

# create a vector with all the column names, get form featuresDF, that have been
# cleaned up for use as variable names in R
featuresCleanedDF <- featuresDF
featuresCleanedDF$V2 <- gsub('[[:punct:]]', '', featuresCleanedDF$V2)
featuresCleanedDF$V2 <- gsub('mean', 'Mean', featuresCleanedDF$V2)
featuresCleanedDF$V2 <- gsub('std', 'Std', featuresCleanedDF$V2)
cleanNamesVect <- c(featuresCleanedDF$V2, "Subject_ID", "Activity_ID")

### merge the columns of the data sets
mergedAllTrainDF <- cbind(xTrainDF, subjTrainDF, yTrainDF)
mergedAllTestDF <- cbind(xTestDF, subjTestDF, yTestDF)
### merge the train and test data
mergedAllDataPoints <- rbind(mergedAllTrainDF,mergedAllTestDF)
# replace the column names of the mergedAllDataPoints with the legal R names
# created previously in featuresCleanedDF$V2
names(mergedAllDataPoints) <- cleanNamesVect

# create a vector of the column numbers to keep and sort it in ascending order
keepCols <- sort(c(grep("Mean",names(mergedAllDataPoints)), 
                    grep("Std",names(mergedAllDataPoints)),
                    grep("_ID",names(mergedAllDataPoints))))


# create df that has only the Mean, Std, & ID columns
onlyMeanStdData <- mergedAllDataPoints[,c(keepCols)]
# convert Actvity ID #'s to Activity Names
activityNamesMeanStdData <- merge(onlyMeanStdData,activitiesDF, by='Activity_ID')
# make name shorter 
finalDF <- activityNamesMeanStdData
# move the Subject and Activity Name cls to the front with Actvity Name firrst
finalDF <- finalDF[,c(ncol(finalDF),1:(ncol(finalDF)-1))]
finalDF <- finalDF[,c(ncol(finalDF),1:(ncol(finalDF)-1))]
finalDF <- finalDF[,c(2,1,3:(ncol(finalDF)))]
# remove Actviity_ID column
finalDF <- finalDF[,-3]
# to use dplyr and tidyr functions 
# will need to put in a in a 'data frame tbl' using the tbl_df()

finalTblDF <- tbl_df(finalDF)
finalTblDF <- arrange(finalTblDF, Activity_Name, Subject_ID)

groupActvSubj <- group_by(finalTblDF, Activity_Name, Subject_ID) 

### dg <- group_by(df, sex)
# the names of the columns you want to summarize
### cols <- names(dg)[-1]
# the dots component of your call to summarise
### dots <- sapply(cols ,function(x) substitute(mean(x), list(x=as.name(x))))
### do.call(summarise, c(list(.data=dg), dots))
# get the column names, except those for the group
columnNames <- names(groupActvSubj) [-c(1,2)]

dots <- lapply(columnNames ,function(x) substitute(mean(x), list(x=as.name(x))))

summaryTblDF <- do.call(summarise, c(list(.data=groupActvSubj), dots))

write.table(summaryTblDF, file = 'SummaryTable.txt', row.names = FALSE)