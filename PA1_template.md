Introduction
============

It is now possible to collect a large amount of data about personal
movement using activity monitoring devices such as a Fitbit, Nike
Fuelband, or Jawbone Up. These type of devices are part of the
quantified self movement a group of enthusiasts who take measurements
about themselves regularly to improve their health, to find patterns in
their behavior, or because they are tech geeks. But these data remain
under-utilized both because the raw data are hard to obtain and there is
a lack of statistical methods and software for processing and
interpreting the data.

This assignment makes use of data from a personal activity monitoring
device. This device collects data at 5 minute intervals through out the
day. The data consists of two months of data from an anonymous
individual collected during the months of October and November, 2012 and
include the number of steps taken in 5 minute intervals each day.

The data for this assignment can be downloaded from the repository.

Dataset: Activity monitoring data \[52K\]

### The variables included in this dataset are:

**steps**: Number of steps taking in a 5-minute interval (missing values
are coded as NA)

**date**: The date on which the measurement was taken in YYYY-MM-DD
format

**interval**: Identifier for the 5-minute interval in which measurement
was taken

The dataset is stored in a comma-separated-value (CSV) file and there
are a total of 17,568 observations in this dataset.

Objectives
----------

1.Code for reading in the dataset and/or processing the data

2.Histogram of the total number of steps taken each day

3.Mean and median number of steps taken each day

4.Time series plot of the average number of steps taken

5.The 5-minute interval that, on average, contains the maximum number of
steps

6.Code to describe and show a strategy for imputing missing data

7.Histogram of the total number of steps taken each day after missing
values are imputed

8.Panel plot comparing the average number of steps taken per 5-minute
interval across weekdays and weekends

### 1.Code for reading in the dataset and/or processing the data

In order to manipulate and effectively analyse the data we are going to
need a couple of packages.

    require(lubridate)

    ## Loading required package: lubridate

    ## Warning: package 'lubridate' was built under R version 3.2.5

    ## 
    ## Attaching package: 'lubridate'

    ## The following object is masked from 'package:base':
    ## 
    ##     date

    require(dplyr)

    ## Loading required package: dplyr

    ## Warning: package 'dplyr' was built under R version 3.2.5

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:lubridate':
    ## 
    ##     intersect, setdiff, union

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

The dataset has already been downloaded,unzipped and saved in the same
directory as this project. Now we can read the dataset into R.

    act<-read.csv("activity.csv")

We can look at the file briefly to get an overview of the data.

    str(act)

    ## 'data.frame':    17568 obs. of  3 variables:
    ##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
    ##  $ date    : Factor w/ 61 levels "2012-10-01","2012-10-02",..: 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...

We can see the date variable isn't in the right format, and NA values
exist within the data. We need to note this as we go further into our
analysis.

We can now convert the date variable to a more suitable format for
analysis

    act$date<-ymd(act$date)
    str(act)

    ## 'data.frame':    17568 obs. of  3 variables:
    ##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
    ##  $ date    : Date, format: "2012-10-01" "2012-10-01" ...
    ##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...

We can now see the date variable has been converted to a **date**
variable. We are done with reading/processing the data and can begin
analysing it.

### 2.Histogram of the total number of steps taken each day

Our aim, is to collate the number of steps for each day. The code below
aggregates the steps by date, and creates a histogram of the steps.

    tot.steps.day<-aggregate(steps~date, data =act,sum, na.rm=T)
    hist(tot.steps.day$steps,main="Histogram of total number of steps taken each day",xlab="Total Steps By Day")

![](PA1_template_files/figure-markdown_strict/Histogram%20of%20Steps%20(by%20Day)-1.png)

### 3.Mean and median number of steps taken each day

We will now like to see the mean and median of steps taken each day.

    mean.steps<-mean(tot.steps.day$steps)
    median.steps<-median(tot.steps.day$steps)
    sumr<-data.frame(mean=mean.steps,median=median.steps)
    sumr

    ##       mean median
    ## 1 10766.19  10765

We now know that the mean and median number of steps taken per day are
1.076618910^{4} and 10765 respectively. \#\#\#4. Time series plot of the
average number of steps taken

    average.steps.inter<-aggregate(steps~interval, data=act,mean, na.rm=T)
    plot(average.steps.inter$interval,average.steps.inter$steps,type="l",xlab="Intervals",
         ylab="Average Steps",main="Time Series Plot of Average Steps Taken")

![](PA1_template_files/figure-markdown_strict/Time%20Series-1.png)

### 5. The 5-minute interval that, on average, contains the maximum number of steps

    max.average.steps.inter<-average.steps.inter[order(average.steps.inter$steps, decreasing=T),]
    max.num<-max.average.steps.inter[1,1]

We have identified the maximum number of steps to be 835.

### 6.Code to describe and show a strategy for imputing missing data

As we noticed during the preparation stage, there are a few NA values.

    na.perc<-mean(is.na(act$steps))*100
    na.perc

    ## [1] 13.11475

We can see the NA make up 13.1147541% of the **step** variable. We will
have to decide how to handle these values. I have decided to make use of
the mean step value for the day to replace all NA values.

    act.imp<- act %>%
            group_by(interval) %>%
            mutate(steps = ifelse(is.na(steps), mean(steps, na.rm=TRUE), steps))
    summary(act.imp)

    ##      steps             date               interval     
    ##  Min.   :  0.00   Min.   :2012-10-01   Min.   :   0.0  
    ##  1st Qu.:  0.00   1st Qu.:2012-10-16   1st Qu.: 588.8  
    ##  Median :  0.00   Median :2012-10-31   Median :1177.5  
    ##  Mean   : 37.38   Mean   :2012-10-31   Mean   :1177.5  
    ##  3rd Qu.: 27.00   3rd Qu.:2012-11-15   3rd Qu.:1766.2  
    ##  Max.   :806.00   Max.   :2012-11-30   Max.   :2355.0

We can see there are no longer NA values within the data set.

### 7.Histogram of the total number of steps taken each day after missing values are imputed

Now we can look at the data set with no NA values to compare if it has
made any significant changes.

    imp.tot.steps.day<-aggregate(steps~date, data =act.imp,sum, na.rm=T)

    hist(imp.tot.steps.day$steps,main="Histogram of total number of steps taken each day(Imputed Data)")

![](PA1_template_files/figure-markdown_strict/Histogram%20of%20Imputed%20Data-1.png)

We will now like to see the mean and median of steps taken each day.

    imp.mean.steps<-mean(imp.tot.steps.day$steps)
    imp.median.steps<-median(imp.tot.steps.day$steps)
    imp.sumr<-data.frame(mean=mean.steps,median=median.steps)
    imp.sumr

    ##       mean median
    ## 1 10766.19  10765

There has been no change to the mean, and median. Which is ideal as we
do not wish to alter the distribution of the data.

### 8.Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends

We have now processed the data, and replaced the NA values with values.
We can then proceed in comparing the steps taken per 5-minute interval
across weekdays and weekends.

    act.imp$day<-weekdays(act.imp$date)
    act.imp$day.type<-0
    act.imp$day.type<-ifelse(act.imp$day %in% c("Saturday","Sunday"),"Weekend","Weekday")
    act.imp$day.type<-factor(act.imp$day.type)

    par(mfrow = c(2, 1))
    for (type in c("Weekend", "Weekday")) {
            steps.type <- aggregate(steps ~ interval, data = act.imp, subset = act.imp$day.type == 
                                            type, FUN = mean)
            plot(steps.type, type = "l", main = type)
    }

![](PA1_template_files/figure-markdown_strict/Weekday%20vs%20Weekend-1.png)

    sessionInfo()

    ## R version 3.2.4 (2016-03-10)
    ## Platform: x86_64-w64-mingw32/x64 (64-bit)
    ## Running under: Windows 7 x64 (build 7601) Service Pack 1
    ## 
    ## locale:
    ## [1] LC_COLLATE=English_United Kingdom.1252 
    ## [2] LC_CTYPE=English_United Kingdom.1252   
    ## [3] LC_MONETARY=English_United Kingdom.1252
    ## [4] LC_NUMERIC=C                           
    ## [5] LC_TIME=English_United Kingdom.1252    
    ## 
    ## attached base packages:
    ## [1] stats     graphics  grDevices utils     datasets  methods   base     
    ## 
    ## other attached packages:
    ## [1] dplyr_0.5.0     lubridate_1.5.6
    ## 
    ## loaded via a namespace (and not attached):
    ##  [1] Rcpp_0.12.5      codetools_0.2-14 digest_0.6.9     assertthat_0.1  
    ##  [5] R6_2.1.2         DBI_0.4-1        formatR_1.4      magrittr_1.5    
    ##  [9] evaluate_0.9     stringi_1.1.1    lazyeval_0.2.0   rmarkdown_1.0   
    ## [13] tools_3.2.4      stringr_1.0.0    yaml_2.1.13      htmltools_0.3.5 
    ## [17] knitr_1.13       tibble_1.1
