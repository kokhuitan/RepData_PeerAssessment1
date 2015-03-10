---
title: 'Reproducible Research: Peer Assessment 1'
author: "Tan Kok Hui"
date: "Tuesday, March 10, 2015"
output: html_document
---

**Loading and preprocessing the data**

1. Load the data.


```r
data0<-read.csv("repdata-data-activity/activity.csv")
```

2. Process/transform the data (if necessary) into a format suitable for your analysis *by removing NA*.



```r
data<-data0[complete.cases(data0),]
```

**What is mean total number of steps taken per day?**

1. Calculate the total number of steps taken per day.


```r
datasum<-aggregate(data$steps, list(data$date), sum)
colnames(datasum) <- c("date","dailysteps")
```

2a. Make a barplot of the total number of steps taken each day


```r
barplot(datasum$dailysteps, 
        names.arg=datasum$date, 
        horiz = TRUE, 
        ylab="Date", 
        xlab="Total number of steps each day")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png) 

2b. Make a histogram of the total number of steps taken each day


```r
hist(datasum$dailysteps, 
     main="Histogram of total number of steps taken each day", 
     xlab="Total number of steps each day")
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png) 

3. Calculate and report the mean and median of the total number of steps taken per day


```r
mean(datasum$dailysteps)
```

```
## [1] 10766.19
```

```r
median(datasum$dailysteps)
```

```
## [1] 10765
```

**What is the average daily activity pattern?**

1.Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
avgsteps<-aggregate(data$steps, list(data$interval), mean)
colnames(avgsteps) <- c("interval","avgintervalsteps")
plot(avgsteps, type="l")
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7-1.png) 

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
avgsteps[which.max(avgsteps$avgintervalsteps),1]
```

```
## [1] 835
```

**Imputing missing values**

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
dim(data0)[1]-dim(data)[1]
```

```
## [1] 2304
```

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
3. Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
d <- as.data.frame(data0)   #make a copy of data0
d[is.na(d)] <- 0            #assign all NA to 0
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
datasum2<-aggregate(d$steps, list(d$date), sum)
colnames(datasum2) <- c("date","dailysteps")
hist(datasum2$dailysteps, 
     main="Histogram of total number of steps taken each day", 
     xlab="Total number of steps each day")
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11-1.png) 

```r
mean(datasum2$dailysteps)
```

```
## [1] 9354.23
```

```r
median(datasum2$dailysteps)
```

```
## [1] 10395
```

**Are there differences in activity patterns between weekdays and weekends?**

1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
data$day<-weekdays(as.Date(data$date))
for(i in 1:length(data[,1])){
  if(data$day[i]=="Saturday"|data$day[i]=="Sunday"){
    data$day[i]<-"weekend"
  }
  else{
    data$day[i]<-"weekday"
  }
}
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
avgsteps2<-aggregate(data$steps, by=list(data$interval,data$day), mean)
colnames(avgsteps2) <- c("interval","day","avgintervalsteps")
byday <- split(avgsteps2, avgsteps2$day)
par(mfrow=c(2,1))
for (i in 1:2) {
    tmpdf <- byday[[i]]
    plot(tmpdf$interval, tmpdf$avgintervalsteps,
         main = tmpdf$day[1],
         xlab = "interval",
         ylab = "average steps", type="l")
}
```

![plot of chunk unnamed-chunk-13](figure/unnamed-chunk-13-1.png) 
