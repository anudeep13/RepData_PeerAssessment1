-   [R Markdown](#r-markdown)

R Markdown
----------

**This is an R Markdown document for the peer graded assignment in
reproducible research.**

Reading data from "activity.csv"

    df=read.csv("activity.csv")
    df$date=as.Date(df$date,"%Y-%m-%d")

Calculating total nuber of steps taken each day.

    library(dplyr)
    x=group_by(df,date)
    y=summarise(x,No.of.steps.taken.each.day=sum(steps,na.rm=TRUE))

Histogram of total number of steps taken each day.

    hist(y$No.of.steps.taken.each.day,breaks=30,xlab="Number of steps taken each day",main="Histogram of number of steps taken each day")

![](PA1_template_files/figure-markdown_strict/histogram1-1.png)

    dev.copy(png,"plot1.png")

    ## png 
    ##   5

    dev.off()

    ## RStudioGD 
    ##         2

Mean and Median of total number of steps taken each day.

    cat("The mean of total number of steps taken each day :",mean(y$No.of.steps.taken.each.day))

    ## The mean of total number of steps taken each day : 9354.23

    cat("The median of total number of steps taken each day :",median(y$No.of.steps.taken.each.day))

    ## The median of total number of steps taken each day : 10395

Average daily activity pattern

    tmp=group_by(df,interval)
    avgsteps=summarise(tmp,average.no.of.steps=mean(steps,na.rm=TRUE))
    plot(avgsteps$interval,avgsteps$average.no.of.steps,"l",main="5 min interval vs avg no. of steps",xlab="5 min interval",ylab ="avg number of steps")

![](PA1_template_files/figure-markdown_strict/avgpatt-1.png)

    dev.copy(png,"plot2.png")

    ## png 
    ##   5

    dev.off()

    ## RStudioGD 
    ##         2

The 5-minute interval, on average across all the days in the dataset,
containing the maximum number of steps

    cat("5 min interval with maximum number of steps:",avgsteps[[which.max(avgsteps$average.no.of.steps),"interval"]])

    ## 5 min interval with maximum number of steps: 835

    olddf=df

Number of missing values

    cat("Number of missing values",sum(is.na(df)))

    ## Number of missing values 2304

Imputing missing values based on 5 min interval

    for(i in seq(1,nrow(df),1)){
        if(is.na(df[i,"steps"])){
            df[i,"steps"]=mean(df[df$interval==df[i,"interval"],"steps"],na.rm=TRUE)
            
        }
        
    }

Histogram of the total number of steps taken each day

    grpday=group_by(df,date)
    steps=summarise(grpday,total.no.of.steps.taken=sum(steps))
    hist(steps$total.no.of.steps.taken,breaks=30,xlab="Total number of steps taken",main="Total number of steps taken each day")

![](PA1_template_files/figure-markdown_strict/histogram2-1.png)

    dev.copy(png,"plot3.png")

    ## png 
    ##   5

    dev.off()

    ## RStudioGD 
    ##         2

Mean and Median of total number of steps taken per day

    cat("Mean of total number of steps taken per day:",mean(steps$total.no.of.steps.taken))

    ## Mean of total number of steps taken per day: 10766.19

    cat("Median of total number of steps taken per day:",median(steps$total.no.of.steps.taken))

    ## Median of total number of steps taken per day: 10766.19

Creating a new factor variable "day" in the dataset with two levels -
"weekday" and "weekend" indicating whether a given date is a weekday or
weekend day.

    for(i in seq(1,nrow(df),1)){
        if(weekdays(df[i,"date"])=="Sunday"|weekdays(df[i,"date"])=="Saturday"){
            df[i,"day"]="weekend"
            
        }else{
            df[i,"day"]="weekday"
            
        }
    }

Making a panel plot containing a time series plot (i.e. type = "l") of
the 5-minute interval (x-axis) and the average number of steps taken,
averaged across all weekday days or weekend days (y-axis).

    df$day=as.factor(df$day)
    intervalgrp=group_by(df,interval,day)
    dayintsteps=summarise(intervalgrp,avg.no.of.steps=mean(steps,na.rm=TRUE))
    library(ggplot2)
    ggplot(dayintsteps,aes(x=interval,y=avg.no.of.steps))+ geom_line()+facet_grid(day~.)

![](PA1_template_files/figure-markdown_strict/panelplot-1.png)

    dev.copy(png,"plot4.png")

    ## png 
    ##   5

    dev.off()

    ## RStudioGD 
    ##         2
