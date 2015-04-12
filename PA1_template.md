# Reproducible Research: Peer Assessment 1

## Loading and preprocessing the data

Load the data (i.e. read.csv())

```r
unzip("activity.zip")
activity<-read.csv("activity.csv")
```

Process/transform the data (if necessary) into a format suitable for your analysis


## What is mean total number of steps taken per day?

**For this part of the assignment, you can ignore the missing values in the dataset.**

**1. Calculate the total number of steps taken per day**  
Code:

```r
activityWithValues <- activity[!is.na(activity$steps),]
sumStepsByDate <-aggregate(steps ~ date, data=activityWithValues, sum)
```
output:

```r
sumStepsByDate
```

```
##          date steps
## 1  2012-10-02   126
## 2  2012-10-03 11352
## 3  2012-10-04 12116
## 4  2012-10-05 13294
## 5  2012-10-06 15420
## 6  2012-10-07 11015
## 7  2012-10-09 12811
## 8  2012-10-10  9900
## 9  2012-10-11 10304
## 10 2012-10-12 17382
## 11 2012-10-13 12426
## 12 2012-10-14 15098
## 13 2012-10-15 10139
## 14 2012-10-16 15084
## 15 2012-10-17 13452
## 16 2012-10-18 10056
## 17 2012-10-19 11829
## 18 2012-10-20 10395
## 19 2012-10-21  8821
## 20 2012-10-22 13460
## 21 2012-10-23  8918
## 22 2012-10-24  8355
## 23 2012-10-25  2492
## 24 2012-10-26  6778
## 25 2012-10-27 10119
## 26 2012-10-28 11458
## 27 2012-10-29  5018
## 28 2012-10-30  9819
## 29 2012-10-31 15414
## 30 2012-11-02 10600
## 31 2012-11-03 10571
## 32 2012-11-05 10439
## 33 2012-11-06  8334
## 34 2012-11-07 12883
## 35 2012-11-08  3219
## 36 2012-11-11 12608
## 37 2012-11-12 10765
## 38 2012-11-13  7336
## 39 2012-11-15    41
## 40 2012-11-16  5441
## 41 2012-11-17 14339
## 42 2012-11-18 15110
## 43 2012-11-19  8841
## 44 2012-11-20  4472
## 45 2012-11-21 12787
## 46 2012-11-22 20427
## 47 2012-11-23 21194
## 48 2012-11-24 14478
## 49 2012-11-25 11834
## 50 2012-11-26 11162
## 51 2012-11-27 13646
## 52 2012-11-28 10183
## 53 2012-11-29  7047
```

**2. If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day**  
Code and plot:

```r
hist(x = sumStepsByDate$steps)
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png) 

**3. Calculate and report the mean and median of the total number of steps taken per day**  
Code:

```r
means<-mean(sumStepsByDate$steps)
medians<-median(sumStepsByDate$steps)
```

mean and median of the total number of steps taken per day are 1.0766189\times 10^{4} and 10765


## What is the average daily activity pattern?  
**1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)**  

Code and plot:

```r
meanStepsByinterval<-aggregate(steps ~ interval, data=activity, mean)
plot(y=meanStepsByinterval$steps, x=meanStepsByinterval$interval, type="l", ylab="Steps", xlab="Interval",  main="Mean Steps of a given interval")
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png) 

**2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?**  
Code:

```r
maxStep<-meanStepsByinterval[ (meanStepsByinterval$steps==max(meanStepsByinterval$steps)),]
```
At interval 835, the maximum average step is 206.1698113

## Imputing missing values

**Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)**  
Code: 

```r
totalMissing <- sum(is.na(activity))
```
About 2304 rows has missing values.

**2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.**  

We will use the mean of the 5 minute interval to impute missing value.


**3. Create a new dataset that is equal to the original dataset but with the missing data filled in.**  

```r
activityCleaned <- merge(x=activity, y=meanStepsByinterval, by.x="interval", by.y="interval", all.x=TRUE)
activityCleaned$steps.x[is.na(activityCleaned$steps.x)] <-activityCleaned$steps.y
```

```
## Warning in activityCleaned$steps.x[is.na(activityCleaned$steps.x)] <-
## activityCleaned$steps.y: number of items to replace is not a multiple of
## replacement length
```

```r
sum(is.na(activityCleaned))
```

```
## [1] 0
```

```r
names(activityCleaned)[2]='steps'
```

**4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?**  

Cleaned data has more counts on steps less than 5000.
code and plot:

```r
sumStepsCleanedByDate <-aggregate(steps ~ date, data=activityCleaned, sum)
par(mfrow=(c(1,2)))
hist(x = sumStepsByDate$steps, main = "Raw data", xlab="Total steps by date")
hist(x = sumStepsCleanedByDate$steps, main = "Cleaned data" ,xlab="Total steps by date")
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png) 

Code for means and median comparison:

```r
means<-mean(sumStepsByDate$steps)
medians<-median(sumStepsByDate$steps)


meansCleaned<-mean(sumStepsCleanedByDate$steps)
mediansCleaned<-median(sumStepsCleanedByDate$steps)
```

For mean, cleaned data has 9371.4370554 VS 1.0766189\times 10^{4}
For median, cleaned data has 1.0395\times 10^{4} VS 10765

## Are there differences in activity patterns between weekdays and weekends?
**For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.  **

**1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.**  
Code:

```r
activityCleaned$days<- weekdays(strptime(as.character(activityCleaned$date), "%Y-%m-%d"))
activityCleaned$weekend<-'weekday'
activityCleaned$weekend[activityCleaned$days %in% c('Saturday','Sunday')]<-'weekend'
activityCleaned$weekend <-factor(activityCleaned$weekend)
```

**2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.**  

Code and plot:

```r
meanStepsCleanedByinterval<-aggregate(steps ~ interval+weekend, data=activityCleaned, mean)

library(lattice)

xyplot(steps ~ interval | weekend,data=meanStepsCleanedByinterval, layout=c(1,2), type = "l", , main="Mean Steps of a given interval of Cleaned dataset")
```

![](PA1_template_files/figure-html/unnamed-chunk-13-1.png) 

