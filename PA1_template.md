---
title: 'Reproducible Research: Peer Assessment 1'
author: "Shan"
date: "May 3, 2016"
output: html_document
---

## Loading and preprocessing the data

The data for this assignment is contained in a zipped file on the course Github repository. It can be accessed from there on the fly at the time of analysis.


```r
# set path
setwd("E:\\SS\\Coursera Data Science Specialization\\Reproducible Research\\Week 2\\Project")

# download file
download.file("https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip", 
"E:\\SS\\Coursera Data Science Specialization\\Reproducible Research\\Week 2\\Project\\data")

# unzip file
unzip("E:\\SS\\Coursera Data Science Specialization\\Reproducible Research\\Week 2\\Project\\data",
      exdir = "E:\\SS\\Coursera Data Science Specialization\\Reproducible Research\\Week 2\\Project\\data1")
```


**Show any code that is needed to**

1.  Load the data (i.e. read.csv())

```r
activity <- read.csv("./data1/activity.csv")
```

2.  Process/transform the data (if necessary) into a format suitable for your analysis


```r
summary(activity)
```

```
##      steps                date          interval     
##  Min.   :  0.00   2012-10-01:  288   Min.   :   0.0  
##  1st Qu.:  0.00   2012-10-02:  288   1st Qu.: 588.8  
##  Median :  0.00   2012-10-03:  288   Median :1177.5  
##  Mean   : 37.38   2012-10-04:  288   Mean   :1177.5  
##  3rd Qu.: 12.00   2012-10-05:  288   3rd Qu.:1766.2  
##  Max.   :806.00   2012-10-06:  288   Max.   :2355.0  
##  NA's   :2304     (Other)   :15840
```

```r
str(activity)
```

```
## 'data.frame':	17568 obs. of  3 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : Factor w/ 61 levels "2012-10-01","2012-10-02",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
```

```r
names(activity)
```

```
## [1] "steps"    "date"     "interval"
```

```r
activity$date <- as.Date(activity$date, "%Y-%m-%d")
```

### What is the total number of steps taken per day?

  **1) Make a histogram of the total number of steps taken each day **
  
 The following histogram displays the total number of steps taken per day, ignoring missing values.


```r
dat <- aggregate(activity[1],by=activity[2],FUN=sum,na.rm=TRUE)

hist(dat$steps,
     breaks=20,
     col = "cyan",
     main = "Histogram of Total Number of Steps Taken each Day",
     xlab = "Steps per Day")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png)

  **2) Calculate and report the mean and median total number of steps taken per day**


```r
with(dat, mean(steps),na.rm=TRUE) # mean
```

```
## [1] 9354.23
```

```r
with(dat, median(steps),na.rm=TRUE) # median
```

```
## [1] 10395
```


**Mean:** Ignoring missing values, the mean number of steps taken per day is 9,354.

**Median:** Ignoring missing values, the median number of steps taken per day is 10,395.



### What is the average daily activity pattern?

**1) Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the  average number of steps taken, averaged across all days (y-axis)**

 Following is a time-series plot of the average number of steps taken during each 5-minute interval during the day.


```r
dat.interval <- aggregate(activity[1],by= activity[3], FUN = mean, na.rm=TRUE)
plot(dat.interval$interval ,  dat.interval$steps, type='l', xlab= "Interval", ylab="Number of steps", main="Average number of steps taken")
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6-1.png)

**2) Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?**


```r
which.max(dat.interval$steps)
```

```
## [1] 104
```

```r
dat.interval$interval[ 104]      # maximum interval
```

```
## [1] 835
```

```r
round(dat.interval$steps[dat.interval$interval==835],1)  # maximum steps per interval
```

```
## [1] 206.2
```

The maximum average steps per period occur at interval 835, with an average of 206.2 steps per 5 minutes.

## Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA). 
The presence of missing days may introduce bias into some calculations or summaries of the data.

**1) Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)**


```r
nrow(activity)        # total number of records
```

```
## [1] 17568
```

```r
sum(is.na(activity))  # missing records
```

```
## [1] 2304
```

The total number of records in the dataset is 17,568. Of these, 2,304 contain missing values for number of steps taken.

**2) Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc. **

   Missing values for steps per interval are replaced by the mean number of steps for that interval, 
   calculated on the non-missing rows. A new dataset is created that contains these imputed values.


### Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
   library(plyr)
   impute.mean <- function(x) replace(x, is.na(x), mean(x, na.rm = TRUE))
   activity.impute <- ddply(activity, ~interval, transform, steps = impute.mean(steps))
```



#### Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. 

Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?



#### The following is a histogram of the total number of steps per day using imputed values for missing values.



```r
activity.imp.dat <- aggregate(activity.impute[1],activity.impute[2],sum)

hist(activity.imp.dat$steps,
     breaks=20,
     col = "green",
     main = "Histogram of Total Number of Steps Taken per Day (Imputed Data)",
     xlab = "Steps per Day") 
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10-1.png)

```r
mean(activity.imp.dat$steps)
```

```
## [1] 10766.19
```

```r
median(activity.imp.dat$steps)
```

```
## [1] 10766.19
```

**Mean:** Ignoring missing values, the mean number of steps taken per day, rounded to the nearest step, is 10,766.

**Median:** Ignoring missing values, the median number of steps taken per day, rounded to the nearest step, is 10,766.

***Yes, values differ from the estimates from the first part of the assignment.***

The impact of imputing missing data on the estimates of the total daily number of steps are as follows:

The imputation has increased both the mean and the median values from those calculated in the first part of the assignment.

***Also, given the number of missing values replaced, the median values contain the mean values imputed to them. 
This approach may overweigh the central tendency and underweigh the upper and lower extremes.
Other system of imputation may convey more reliable results.***


### Are there differences in activity patterns between weekdays and weekends?



For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating 
whether a given date is a weekday or weekend day.

- New Factor Variable


```r
day <- weekdays(activity.impute$date)    #creating a vector day
day <- ifelse(day %in% c("Sunday","Saturday"),"weekend", "weekday") # converting to weekday and weekend

activity.impute <- cbind(activity.impute,day) # adding the vector to dataframe
```



**Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).** 




The panel plot containing a time series plot follows :



```r
library(ggplot2)
activity.impute.interval <- aggregate(activity.impute[1],by= activity.impute[c(3,4)], FUN=mean,na.rm=TRUE)

adata.impute.interval <- aggregate(activity.impute[1],
                                   by=activity.impute[c(3,4)],
                                   FUN=mean,
                                   na.rm=TRUE)


ggplot(activity.impute.interval, aes(x= interval , y= steps))+ geom_line()+facet_wrap(~day,nrow=2)
```

![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12-1.png)












































































