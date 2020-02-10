---
tags: Rscript, macmanes
---

# Rscript for plotting data

```
#Before plotting (Should add this to R script)
# 1. open macro'd csv file on your own computer and re-save to fix the date issue (otherwise, manually edit years to be only 2 digits)
# 2. Manually edit animal cage numbers to be numbers with no decimals

#load packages

library(tidyverse)
library(lubridate)
library(readr)	


# import data

datafile <- "/Users/macmanes/Desktop/overnight.csv"

# should not have to change below except how you name the dataset e.g., travelingmouse

newdata <- read_csv(datafile, 
	col_types = cols(Animal = col_character(), 
        StartDate = col_date(format = "%m/%d/%y"), 
        StartTime = col_time(format = "%H:%M:%S")))
View(newdata)

# Change variables to plot VO2, in this case.

metric <- "VO2"
SD <- "SD_VO2"

measurement <- waterrandomsampling %>% select(metric)
measurement_SD <- waterrandomsampling %>% select(SD)
waterrandomsampling %>% 
	ggplot(aes(as.POSIXct(with(waterrandomsampling, StartDate + hms(StartTime))), 
		y = measurement[[metric]], group=Animal, color=Animal)) + 
	geom_line() + 
	labs(x = "", y = metric) + 
	scale_color_brewer(palette="Paired") +
	geom_pointrange(aes(ymax = c(measurement[[metric]] + measurement_SD[[SD]]), ymin = c(measurement[[metric]] - measurement_SD[[SD]]))) +
	theme(axis.text.x = element_text(angle = 45, hjust = 1)) +
	scale_x_datetime(date_breaks = "2 hours", date_labels = "%d%b %H:%M")
```

![](https://i.imgur.com/D41eYbd.png)



## If you want to subsample out a single animal

change the metric and SD, and number of animal you want to subsample

```
library(tidyverse)
library(lubridate)
library(readr)

datafile <- "/Users/macmanes/Desktop/waterrandomsampling.csv"
waterrandomsampling <- read_csv(datafile, 
	col_types = cols(Animal = col_character(), 
        StartDate = col_date(format = "%m/%d/%y"), 
        StartTime = col_time(format = "%H:%M:%S")))
View(waterrandomsampling)

metric <- "VO2"
SD <- "SD_VO2"
animal <- 6

measurement <- waterrandomsampling %>% filter(Animal != animal) %>% select(metric)
measurement_SD <- waterrandomsampling %>% filter(Animal != animal) %>% select(SD)
waterrandomsampling %>% filter(Animal != animal) %>% 
	ggplot(aes(as.POSIXct(with(waterrandomsampling %>% filter(Animal != animal), StartDate + hms(StartTime))), 
		y = measurement[[metric]], group=Animal, color=Animal)) + 
	geom_line() + 
	labs(x = "", y = metric) + 
	scale_color_brewer(palette="Paired") +
	geom_pointrange(aes(ymax = c(measurement[[metric]] + measurement_SD[[SD]]), ymin = c(measurement[[metric]] - measurement_SD[[SD]]))) +
	theme(axis.text.x = element_text(angle = 45, hjust = 1)) +
	scale_x_datetime(date_breaks = "2 hours", date_labels = "%d%b %H:%M")

```

![](https://i.imgur.com/zedGPkf.png)



## This is for plotting data by time, for travelling mouse and similar. 

```
attach(travelingmouse)
newdata <- travelingmouse[order(StartTime),]
plot(newdata$VO2, type="l", ylab="VO2", xaxt="n", xlab="Cage Numbers", frame.plot = F, lwd=3, col='red')
abline(h=0)
axis(1, at=1:27, labels=c(0,1,2,3,4,5,6,0,1,2,3,4,5,6,0,1,2,3,4,5,6,0,1,2,3,4,5))
```
#### H2O

![](https://i.imgur.com/YRjYLfX.png)

#### VO2

![](https://i.imgur.com/0keXXim.png)


- [ ] Need to work with Dani/Jocie to make sure at least VO2 makes sense, cause it did in the raw data.. Otherwise this is a macro issue
- [ ] Also note that O2 != VO2, but still..