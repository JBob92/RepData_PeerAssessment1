---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---



```r
knit2html("PA1_template.Rmd")
```

```
## knitr::opts_chunk$set(echo = TRUE)
```

```r
browseURL("PA1_template.html")
```

**#Rep_Data_Assignment1**

# Set the working directory

```r
setwd("/home/rstudio/Reproducible Research/week2")
```

# Read the CSV file

```r
data <- read.csv("activity.csv", stringsAsFactors = FALSE)
```
# Convert date column to Date format

```r
data$date <- as.Date(data$date)
```
# 1 mean no. steps per day
# Sum steps per day, ignoring NAs

```r
daily_steps <- aggregate(steps ~ date, data, sum, na.rm = TRUE)
```
# Histogram of total daily steps

```r
hist(daily_steps$steps, main = "Total Steps per Day", xlab = "Steps", col = "lightblue", breaks = 20)

```
![](plot1.png)

# Mean and Median of daily steps

```r
mean_steps <- mean(daily_steps$steps)
median_steps <- median(daily_steps$steps)
cat("Mean Steps per Day:", mean_steps, "\n")
```

```
## Mean Steps per Day: 10766.19
```

```r
cat("Median Steps per Day:", median_steps, "\n")
```

```
## Median Steps per Day: 10765
```

# 2 Avg daily activity pattern
# Average steps per interval across all days

```r
interval_steps <- aggregate(steps ~ interval, data, mean, na.rm = TRUE)
```
# Time series plot

```r
plot(interval_steps$interval, interval_steps$steps, type = "l",
     main = "Average Daily Activity Pattern", xlab = "5-min Interval", ylab = "Average Steps")

```

![](plot2.png)
# Find the interval with the maximum average steps

```r
max_interval <- interval_steps$interval[which.max(interval_steps$steps)]
cat("5-minute interval with maximum steps:", max_interval, "\n")
```

```
## 5-minute interval with maximum steps: 835
```

# 3 Impute missing values
# Count total missing values

```r
missing_count <- sum(is.na(data$steps))
cat("Total Missing Values:", missing_count, "\n")
```

```
## Total Missing Values: 2304
```

# Replace NAs with mean for that interval

```r
for (i in 1:nrow(data)) {
  if (is.na(data$steps[i])) {
    data$steps[i] <- interval_steps$steps[interval_steps$interval == data$interval[i]]
  }
}
```
# Verify no missing values remain

```r
sum(is.na(data$steps))
```

```
## [1] 0
```
# Sum steps per day after filling NAs

```r
daily_steps_filled <- aggregate(steps ~ date, data, sum)
```
# Histogram with imputed data

```r
hist(daily_steps_filled$steps, main = "Total Steps per Day (Imputed Data)", xlab = "Steps", col = "lightgreen", breaks = 20)
```
![](plot3.png)

# New mean and median

```r
mean_steps_filled <- mean(daily_steps_filled$steps)
median_steps_filled <- median(daily_steps_filled$steps)

cat("Mean Steps per Day (Imputed):", mean_steps_filled, "\n")
```

```
## Mean Steps per Day (Imputed): 10766.19
```

```r
cat("Median Steps per Day (Imputed):", median_steps_filled, "\n")
```

```
## Median Steps per Day (Imputed): 10766.19
```


# 4 Weekdays vs weekends
# Create weekday/weekend factor

```r
data$day_type <- ifelse(weekdays(data$date) %in% c("Saturday", "Sunday"), "weekend", "weekday")
```
# Aggregate by interval and day type

```r
weekday_steps <- aggregate(steps ~ interval + day_type, data, mean)
```
# Split data

```r
weekday_data <- subset(weekday_steps, day_type == "weekday")
weekend_data <- subset(weekday_steps, day_type == "weekend")
```
# Panel plot

```r
par(mfrow = c(2, 1))

plot(weekday_data$interval, weekday_data$steps, type = "l", col = "blue",
     main = "Weekday Activity", xlab = "5-min Interval", ylab = "Avg Steps")

plot(weekend_data$interval, weekend_data$steps, type = "l", col = "red",
     main = "Weekend Activity", xlab = "5-min Interval", ylab = "Avg Steps")
```

![](plot4.png)

```r
par(mfrow = c(1, 1))
```


