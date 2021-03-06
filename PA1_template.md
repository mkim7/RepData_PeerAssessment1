Peer Assessment 1
========================================================

This assignment uses data from a personal activity monitoring device. Tha data consists of 2 months of data, and include the number of steps taken in 5 minute intervals each day.

## Set global options

```r
opts_chunk$set(echo=TRUE, cache=TRUE, results="asis")
```

## Loading and preprocessing the data

### 1. Load the data

```r
setwd("C:/Users/Owner/Documents/R/data")
activity = read.csv("./activity.csv", header=T)
```

### 2. Process/transform data

```r
activity$date <- as.Date(activity$date, format="%Y-%m-%d")
activity$interval <- as.numeric(activity$interval)
totalsteps <- aggregate(steps~date, data=activity, sum, na.rm=TRUE)
```

## What is mean total number of steps taken per day?

### 1. Make a histogram of the total number of steps taken each day


```r
hist(totalsteps$steps, main="Steps Taken Each Day", xlab="Steps")
```

![plot of chunk histogram1](figure/histogram1.png) 

### 2. Calculate and report the mean and median total number of steps taken per day

Mean


```r
meansteps <- mean(totalsteps$steps)
```

The mean is 1.0766 &times; 10<sup>4</sup>.

Median


```r
mediansteps <- median(totalsteps$steps)
```

The median is 10765.

## What is the average daily activity pattern?

### 1. Make a time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y axis)


```r
intervalsteps <- aggregate(steps~interval, data=activity, mean, na.rm=TRUE)
plot(steps~interval, data=intervalsteps, type="l")
```

![plot of chunk timeseries](figure/timeseries.png) 

### 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
intervalindex<-intervalsteps[which.max(intervalsteps$steps),]$interval
```

The 5-minute interval with index 835 has the maximum number of steps.

## Imputing missing values

### 1. Calculate and report the total number of missing values in the dataset.


```r
missingrows <- sum(is.na(activity$steps))
```

2304 rows are missing.

### 2. Devise a strategy for filling in all the missing values in the dataset. 
Missing values will be filled in with the mean for that 5-minute interval. The following function computes the mean number of steps for a particular 5-minute interval.


```r
meanforinterval <- function(interval) {
        intervalsteps[intervalsteps$interval==interval,]$steps
        }
```

### 3. Create a new dataset that is equal to the original dataset, but with the missing data filled in.


```r
activity_noNA <- activity
count = 0
for (i in 1:nrow(activity_noNA)) {
        if(is.na(activity_noNA[i,]$steps)){
                activity_noNA[i,]$steps <- meanforinterval(activity_noNA[i,]$interval)
                count=count+1
        }
}
```

### 4. Make a histogram of the total number of steps taken each day. Calculate and report the mean and median total number of steps. Do these values differ from those in the first part of the assignment? What is the impact of imputing missing data?


```r
totalsteps2 <- aggregate(steps~date, data=activity_noNA, sum)
hist(totalsteps2$steps, main="Steps Taken Each Day (Imputed NA)", xlab="Steps")
```

![plot of chunk histogram2withmeanmedian](figure/histogram2withmeanmedian.png) 

```r
meansteps2 <- mean(totalsteps2$steps)
mediansteps2 <- mean(totalsteps2$steps)
```

The mean is 1.0766 &times; 10<sup>4</sup>.
The median is 1.0766 &times; 10<sup>4</sup>.

The mean is the same as before, because the mean value for a particular 5-minute interval was used to impute missing data.
The median has changed slightly.

## Are there differences in activity patterns between weekdays and weekends? (use weekdays() function and the dataset with filled-in missing values)

### 1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend"


```r
activity_noNA$day = ifelse(as.POSIXlt(as.Date(activity_noNA$date))$wday%%6==0, "weekend", "weekday")
activity_noNA$day=factor(activity_noNA$day, levels=c("weekday", "weekend"))
```

### 2. Make a panel plot (x-axis as 5-minute interval and y-axis as the average number of steps taken, averaged across all week days or weekend days)


```r
intervalsteps2 = aggregate(steps~interval+day, activity_noNA, mean)
library(lattice)
xyplot(steps~interval|factor(day), data=intervalsteps2, aspect=1/2, type="l")
```

![plot of chunk panelplot](figure/panelplot.png) 

