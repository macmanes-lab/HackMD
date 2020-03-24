---
tags: Rscripts
---

# merging datasets

```
library(patchwork)
library(tidyverse)
library(lubridate)
library(readr)
library(viridis)

start_time <- ymd_hms(cages20feb[[1]][1])
begin_experiment <- start_time + dhours(2)
end_time <- begin_experiment + dhours(72)
filter_cages20feb <- cages20feb %>% filter(DateTime >= begin_experiment & DateTime <= end_time)

start_time <- ymd_hms(cages26feb[[1]][1])
begin_experiment <- start_time + dhours(2)
end_time <- begin_experiment + dhours(72)
filter_cages26feb<- cages26feb %>% filter(DateTime >= begin_experiment & DateTime <= end_time)


start_time <- ymd_hms(cages5mar[[1]][1])
begin_experiment <- start_time + dhours(2)
end_time <- begin_experiment + dhours(72)
filter_cages5mar<- cages5mar %>% filter(DateTime >= begin_experiment & DateTime <= end_time)

start_time <- ymd_hms(cages10mar[[1]][1])
begin_experiment <- start_time + dhours(2)
end_time <- begin_experiment + dhours(72)
filter_cages10mar<- cages10mar %>% filter(DateTime >= begin_experiment & DateTime <= end_time)

start_time <- ymd_hms(cages14mar[[1]][1])
begin_experiment <- start_time + dhours(2)
end_time <- begin_experiment + dhours(72)
filter_cages14mar<- cages14mar %>% filter(DateTime >= begin_experiment & DateTime <= end_time)

all_animals <- full_join(filter_cages20feb,filter_cages26feb)
all_animals <- full_join(all_animals,filter_cages5mar)
all_animals <- full_join(all_animals,filter_cages10mar)
all_animals <- full_join(all_animals,filter_cages14mar)

metric0 <- "Deg_C"

target <- 7
cagetemp <- cages14mar %>% filter(animal %in% target)
cagetemp <- tail(cagetemp, n=320)
measurement_zero <- cagetemp %>%  select(metric0)
df<-as.data.frame(measurement_zero[[metric0]])

p0 <- ggplot(data = cagetemp,aes(x=as.POSIXct(StartTime),y=measurement_zero[[metric0]]))
p0 <- p0 + geom_line(aes(), size = 2)
p0 <- p0 + theme(axis.text.x = element_blank(), axis.text.y=element_text(size=12), legend.position = "none")
p0 <- p0 + labs(x = "", y = metric0)
p0 <- p0 + scale_colour_viridis_d(legend_title, option = "C")
p0 <- p0 + scale_x_datetime(date_breaks = "2 hours", date_labels = "%H:%M")

target <- c(0,1,2,3,4,5,6)
all_animals <- all_animals %>% filter(animal %in% target)

metric1 <- "RQ"

measurement_one <- all_animals %>%  select(metric1)
df<-as.data.frame(measurement_one[[metric1]])
legend_title <- "Animal ID"

p1 <- ggplot(data = all_animals,aes(colour=factor(sex),x=as.POSIXct(StartTime),y=measurement_one[[metric1]]))
p1 <- p1 + geom_point(aes(group=as.factor(sex), color=as.factor(sex)), size = 1)
p1 <- p1 + theme(axis.text.x = element_blank(), axis.text.y=element_text(size=12))
p1 <- p1 + geom_smooth(data=df$V1, method='loess', span=.4, level=0.99)
p1 <- p1 + labs(x = "", y = metric1)
p1 <- p1 + scale_color_manual(legend_title, values=c("#050505", "#FF3338"))
p1 <- p1 + scale_x_datetime(date_breaks = "2 hours", date_labels = "%H:%M")
p1 <- p1 + geom_hline(yintercept = 0.8907387, color='coral', size=2)
p1 <- p1 + ylim(0.5, 2)



metric2 <- "EE"

measurement_two <- all_animals %>%  select(metric2)
df<-as.data.frame(measurement_two[[metric2]])
legend_title <- "Animal ID"

p2 <- ggplot(data = all_animals,aes(colour=factor(sex),x=as.POSIXct(StartTime),y=measurement_two[[metric2]]))
p2 <- p2 + geom_point(aes(group=as.factor(sex), color=as.factor(sex)), size = 1,)
p2 <- p2 + theme(axis.text.x = element_blank(), axis.text.y=element_text(size=12), legend.position = "none")
p2 <- p2 + geom_smooth(data=df$V1, method='loess', span=.4, level=0.99)
p2 <- p2 + labs(x = "", y = metric2)
p2 <- p2 + scale_color_manual(values=c("#050505", "#FF3338"))
p2 <- p2 + scale_x_datetime(date_breaks = "2 hours", date_labels = "%H:%M")

metric3 <- "H2Omg"

measurement_three <- all_animals %>%  select(metric3)
df<-as.data.frame(measurement_three[[metric3]])
legend_title <- "Animal ID"

p3 <- ggplot(data = all_animals,aes(colour=factor(sex),x=as.POSIXct(StartTime),y=measurement_three[[metric3]]))
p3 <- p3 + geom_point(aes(group=as.factor(sex), color=as.factor(sex)), size = 1)
p3 <- p3 + theme(axis.text.y=element_text(size=12), legend.position = "none", axis.text.x=element_text(size=12))
p3 <- p3 + geom_smooth(data=df$V1, method='loess', span=.1, level=0.99)
p3 <- p3 + labs(x = "", y = metric3)
p3 <- p3 + scale_color_manual(values=c("#050505", "#FF3338"))
p3 <- p3 + scale_x_datetime(date_breaks = "2 hours", date_labels = "%H:%M")

p0/p1/p2/p3



```

![](https://i.imgur.com/vc3P4xj.png)

