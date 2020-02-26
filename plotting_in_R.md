---
tags: Rscript, macmanes
---

# Rscript for plotting data

updated...


```
library(tidyverse)
library(lubridate)
library(readr)	

cageweight0 <- 20.1
cageweight1 <- 22.6
cageweight2 <- 21.1
cageweight3 <- 23.1
cageweight4 <- 21.8
cageweight5 <- 15.6
cageweight6 <- 15.4

datafile <- "/Volumes/4TB_1/Dropbox/Cactus_Mouse_Physiology/data/20Feb20/feb20.csv"
feb20 <- read_csv(datafile, 
	col_types = cols(Animal = col_double(), 
        StartDate = col_date(format = "%m/%d/%Y"),
        deltaCO2 = col_double(), 
        deltaH2O = col_double(),
        H2Oml = col_double(),
        VCO2 = col_double(),
        StartTime = col_time(format = "%H:%M:%S")))

'%!in%' <- function(x,y)!('%in%'(x,y))

feb20a <- feb20 %>% 
	mutate(EE = 0.06*(3.941*VO2 + 1.106*VCO2)) %>% 
	mutate(RQ = VCO2/VO2) %>%
	mutate(animal = round(Animal, digits=0)) %>%
	mutate(Animal = NULL) %>%
	unite("DateTime", StartDate:StartTime, remove = FALSE, sep =  " ") %>%
	mutate(weight = 
		ifelse(animal == 0, cageweight0, 
		ifelse(animal == 1, cageweight1,
		ifelse(animal == 2, cageweight2,
		ifelse(animal == 3, cageweight3,
		ifelse(animal == 4, cageweight4,
		ifelse(animal == 5, cageweight5,
		ifelse(animal == 6, cageweight6, NA)))))))) %>% 
	mutate(H2Omg_edit = 
		ifelse(hour(StartTime) == 8, H2Omg - 0.09536454,
		ifelse(hour(StartTime) == 7, H2Omg - 0.06536454,
		ifelse(hour(StartTime) == 9, H2Omg - 0.02219424,
		ifelse(hour(StartTime) == 10, H2Omg - 0.03273413,
		ifelse(hour(StartTime) == 19, H2Omg + 0.002130708,
		ifelse(hour(StartTime) == 20, H2Omg + 0.02967473,
		ifelse(hour(StartTime) == 21, H2Omg + 0.02703453,
		ifelse(hour(StartTime) == 22, H2Omg + 0.01412046,
		ifelse(hour(StartTime) %!in% c(7,8,9,10,20,21,22,19), H2Omg, NA)))))))))) %>% 
	mutate_at("H2Omg_edit", as.numeric) %>%
	mutate(corEE = EE/weight)

metric <- "corEE"

target <- c(0,1,2,3,4,5,6)
cages <- feb20a %>% filter(animal %in% target)
#cages <- with( cages ,cages[ hour( StartTime ) >= 0 & hour( StartTime ) < 4 , ] )

measurement <- cages %>%  select(metric)
df<-as.data.frame(measurement[[metric]])
legend_title <- "Cage Number"

p <- ggplot(data = cages,aes(x=as.POSIXct(StartTime),y=measurement[[metric]]))
p <- p + geom_point(aes(group=as.factor(animal), color=as.factor(animal)), size = 3)
p <- p + theme_grey(base_size = 15)
p <- p + geom_smooth(data=df$V1, method='loess', span=.9)
p <- p + labs(x = "", y = metric)
p <- p + scale_color_brewer(legend_title, palette="Paired")
p <- p + scale_x_datetime(date_breaks = "2 hours", date_labels = "%H:%M")
#p <- p + geom_hline(yintercept = 0.8907387)
p


```

![](https://i.imgur.com/fuiIFEb.png)



```

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

#### a different way to plot

```
datafile <- "/Volumes/MACMANES/15Feb20/cages135.csv"
cages135 <- read_csv(datafile, 
	col_types = cols(Animal = col_character(), 
        StartDate = col_date(format = "%m/%d/%y"), 
        StartTime = col_time(format = "%H:%M:%S")))

cages135 <- cages135 %>% mutate(EE = 0.06*(3.941*VO2 + 1.106*VCO2))
cages135 <- cages135 %>% mutate(RQ = VCO2/VO2)
  
metric <- "EE"
SD <- "SD_H2Omg"

target <- c(1,3,5)
cages <- cages135 %>% filter(Animal %in% target)
measurement <- cages %>%  select(metric)
df<-as.data.frame(measurement[[metric]])

p <- ggplot(data = cages,aes(x=as.POSIXct(StartTime),y=measurement[[metric]]))
p <- p + geom_point(aes(group=Animal, color=Animal))
p <- p + geom_smooth(data=df$V1)
p <- p + labs(x = "", y = metric)
p <- p + scale_color_brewer(palette="Paired")
p <- p + scale_x_datetime(date_breaks = "2 hours", date_labels = "%H:%M")
p
```

![](https://i.imgur.com/JjgntrQ.png)
