Reproducible Research - Programming assignment 1
========================================================

It is now possible to collect a large amount of data about personal movement using activity monitoring devices such as a Fitbit, Nike Fuelband, or Jawbone Up. These type of devices are part of the “quantified self” movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

(source: coursera.org)

## Data loading and handling of missing values

Ok, let's start downloading and opening our dataset.


```r
download.file("https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip", "repdata.zip", method = "wget")
unzip("repdata.zip")
table<-read.csv("activity.csv")
```

Let's take a glance to our data.


```r
head(table, 3)
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
```

Looks like we have a problem with NAs.


```r
sum(is.na(table[, 1]))
```

```
## [1] 2304
```

Yeah, we do.

Because we can't make any assumption about our missingness mechanism(s), we can't use any imputational mechanism. We just say goodbye to missing values.


```r
table <- na.omit(table)
```


## What is mean total number of steps taken per day?


```r
stepsByDay <- aggregate(table[, 1], list(table[, 2]), sum)
stepsByDay <- aggregate(table[, 1], list(table[, 2]), sum)
summary(stepsByDay)
```

```
##        Group.1         x        
##  2012-10-02: 1   Min.   :   41  
##  2012-10-03: 1   1st Qu.: 8841  
##  2012-10-04: 1   Median :10765  
##  2012-10-05: 1   Mean   :10766  
##  2012-10-06: 1   3rd Qu.:13294  
##  2012-10-07: 1   Max.   :21194  
##  (Other)   :47
```

A graph will make it clearer, right?


```r
hist(stepsByDay[, 2], main="Number of Steps in a Day", xlab = "Steps")
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6.png) 

Not this one, let's try the wonderful breaks argument...


```r
hist(stepsByDay[, 2], breaks = 20, main="Number of Steps in a Day", xlab = "Steps")
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7.png) 

## What is the average daily activity pattern?

Time for another plot.


```r
intervals<-aggregate(table[, 1], list(table[, 3]), mean)
intervals[which.max(intervals[, 2]),]
```

```
##     Group.1     x
## 104     835 206.2
```


```r
plot(intervals[, 2]~intervals[, 1], type="l", xlab = "Interval", ylab = "Mean activity")
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9.png) 

## Are there differences in activity patterns between weekdays and weekends?

First, we'll set the sys locale.
That's for - wait for it - reproducibility.


```r
Sys.setlocale("LC_TIME", "C")
```

```
## [1] "C"
```

Second, dissociate by day.
(Saturday separate from Sunday, two possibly different activity-showing days)


```r
table$day <- weekdays(as.Date(table$date))
sun<- table[which(table[, 4] == "Sunday"), ]
sat<- table[which(table[, 4] == "Saturday"), ]
wd<- table[which(table[, 4] != "Saturday"), ]
wd<- wd[which(wd[, 4] != "Sunday"), ]


intervalsWD<-aggregate(wd[, 1], list(wd[, 3]), mean)
intervalsSUN<-aggregate(sun[, 1], list(sun[, 3]), mean)
intervalsSAT<-aggregate(sat[, 1], list(sat[, 3]), mean)
```



```r
layout(matrix(c(1,2,3,4), 2, 2, byrow = TRUE))
plot(intervals[, 2]~intervals[, 1], type="l", main = "Total", xlab = "Interval", ylab = "Mean activity")
plot(intervalsSUN[, 2]~intervalsSUN[, 1], type="l", main = "Sunday", xlab = "Interval", ylab = "Mean activity")
plot(intervalsSAT[, 2]~intervalsSAT[, 1], type="l", main = "Saturday", xlab = "Interval", ylab = "Mean activity")
plot(intervalsWD[, 2]~intervalsWD[, 1], type="l", main = "Weekdays", xlab = "Interval", ylab = "Mean activity")
```

![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12.png) 
