# Reproducible Research Project 1
Qatelyn  
Thursday, June 11, 2015  


First load data and required libraries

```r
library("ggplot2")
library("impute")
library("Hmisc")
raw_data<-read.csv("activity.csv")
```

#What is mean total number of steps taken per day?

For this part of the assignment, we can ignore the missing values in the dataset. Steps:

1. Calculate the total number of steps taken per day

```r
total_steps <- tapply(raw_data$steps, raw_data$date, FUN = sum, na.rm = TRUE)
```

2. If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day

```r
hist(total_steps,breaks=20,main="Histogram of total number of steps",xlab="Total steps")
```

![](figure/unnamed-chunk-3-1.png) 

3. Calculate and report the mean and median of the total number of steps taken per day

mean:

```r
mean(total_steps,na.rm=TRUE)
```

```
## [1] 9354.23
```

median:

```r
median(total_steps,na.rm=TRUE)
```

```
## [1] 10395
```

#What is the average daily activity pattern?

Steps:

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
average_steps <- aggregate(x=list(steps=raw_data$steps), by=list(interval=raw_data$interval),
                      FUN=mean, na.rm=TRUE)
plot(average_steps,type="l",main="Average steps per interval")
```

![](figure/unnamed-chunk-6-1.png) 

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

Interval:

```r
average_steps$interval[which.max(average_steps$steps)]
```

```
## [1] 835
```

#Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1.Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

Number of missing values:

```r
sum(is.na(raw_data$steps))
```

```
## [1] 2304
```

2.Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

3.Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
imputed_data <- raw_data
imputed_data$steps <- impute(imputed_data$steps, fun=mean)
```

4.Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
imputed_total_steps <- tapply(imputed_data$steps, imputed_data$date, FUN = sum, na.rm = TRUE)
hist(imputed_total_steps,breaks=25,main="Histogram of total number of steps with imputed data")
```

![](figure/unnamed-chunk-10-1.png) 

3. Calculate and report the mean and median of the total number of steps taken per day


```r
mean<-mean(total_steps,na.rm=TRUE)
median<-median(total_steps,na.rm=TRUE)
```

*Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1.Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
imputed_data$daytype <-  ifelse(as.POSIXlt(imputed_data$date)$wday %in% c(0,6), 'weekend', 'weekday')
```

2.Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
imputed_averaged_steps <- aggregate(x=list(steps=imputed_data$steps), by=list(interval=imputed_data$interval,daytype=imputed_data$daytype),
                      FUN=mean, na.rm=TRUE)
ggplot(imputed_averaged_steps, aes(interval, steps)) + 
    geom_line() + 
    facet_grid(daytype ~ .) +
    xlab("5-minute interval") + 
    ylab("avarage number of steps")
```

![](figure/unnamed-chunk-13-1.png) 
