---
title: 'Reproducible Research: Peer Assessment 1'
output:
  html_document:
    keep_md: yes
  pdf_document: default
---


## Loading and preprocessing the data
Load data:

```r
  data = read.csv(unz("activity.zip", "activity.csv"))
```
Process/transform the data (if necessary) into a format suitable for your analysis


## What is mean total number of steps taken per day?
Calculate the total number of steps taken per day

```r
data_groupby_date <- aggregate(steps ~ date, data, sum)
```

If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day

```r
hist(data_groupby_date$steps,xlab="Steps",ylab="Frequency", main = "Histogram of Total Steps")
```

![](PA1_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

Calculate and report the mean and median of the total number of steps taken per day

```r
mean(data_groupby_date$steps)
```

```
## [1] 10766.19
```

```r
median(data_groupby_date$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

Make a time series plot (i.e.}type="l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
data_groupby_interval <- aggregate(steps ~ interval, data, mean)
plot(data_groupby_interval$interval,data_groupby_interval$steps,type="l",xlab="Interval",ylab="Steps")
```

![](PA1_files/figure-html/unnamed-chunk-5-1.png)<!-- -->
Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?
The maximum point (peak) is at the 105 index, the peak value is 835

```r
data_groupby_interval[which.max(data_groupby_interval$steps),1]
```

```
## [1] 835
```

## Imputing missing values

Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
missing <- data[!complete.cases(data),]
nrow(missing)
```

```
## [1] 2304
```
Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

We take the second approach, i.e. use mean for that 5 minutes interval.

find indexes of missing values:

```r
precalculated_means <- data_groupby_interval$steps
total_days <- length(unique(data$date))
missing_id = which(is.na(data$steps)==TRUE)
```

Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
data_filled = data
data_filled$steps[missing_id] <- rep(precalculated_means,total_days)[missing_id]
```

Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

Histogram

```r
data_filled_groupby_date <- aggregate(steps ~ date, data_filled, sum)
hist(data_filled_groupby_date$steps,xlab="Steps",ylab="Frequency", main = "Histogram of Total Steps")
```

![](PA1_files/figure-html/unnamed-chunk-10-1.png)<!-- -->
Mean is 10766.19 and Median is 10766.19

```r
mean(data_filled_groupby_date$steps)
```

```
## [1] 10766.19
```

```r
median(data_filled_groupby_date$steps)
```

```
## [1] 10766.19
```
Impact is negligible
## Are there differences in activity patterns between weekdays and weekends?
Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```r
data_filled$weekday = weekdays(as.Date(data_filled$date))
```

Make a panel plot containing a time series plot (i.e. type="l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

```r
data_weekdays <- subset(data_filled, weekday != 'Saturday' & weekday != 'Sunday')
nrow(data_weekdays)
```

```
## [1] 12960
```

```r
data_weekends <- subset(data_filled, weekday == 'Saturday' | weekday == 'Sunday')
nrow(data_weekends)
```

```
## [1] 4608
```

```r
par(mfrow=c(2,1))
with(aggregate(steps ~ interval, data_weekends, mean),plot(interval,steps,type="l",main="Weekend"))
with(aggregate(steps ~ interval, data_weekdays, mean),plot(interval,steps,type="l",main="Weekday"))
```

![](PA1_files/figure-html/unnamed-chunk-13-1.png)<!-- -->



