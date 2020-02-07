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

metric <- newdata$VO2
measurement <- newdata$VO2
measurement_SD <- newdata$SD_VO2


newdata %>% 
	ggplot(aes(as.POSIXct(with(newdata, StartDate + hms(StartTime))), y = metric, group=Animal, color=Animal)) + 
	geom_line() + 
	labs(x = "") + 
	scale_color_manual(values=c("#FF5733", "#FFFF33", "#61FF33", "#33E3FF", "#3383FF", "#FF33FF", "#050005")) +
	geom_pointrange(aes(ymax = c(measurement + measurement_SD), ymin = c(measurement - measurement_SD)))
```

![](https://i.imgur.com/vANoRbE.png)



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