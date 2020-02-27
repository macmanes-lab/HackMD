---
tags: Rscripts
---

# merging datasets

```
all_animals <- full_join(cages20feb,cages26feb)
library(devtools)
library(patchwork)
library(tidyverse)
library(lubridate)
library(readr)
library(viridis)


all_animals <- full_join(cages20feb,cages26feb)

metric0 <- "Deg_C"

target <- 4
cagetemp <- all_animals %>% filter(animal %in% target)

measurement_zero <- cagetemp %>%  select(metric0)
df<-as.data.frame(measurement_zero[[metric0]])

p0 <- ggplot(data = cagetemp,aes(x=as.POSIXct(StartTime),y=measurement_zero[[metric0]]))
p0 <- p0 + geom_line(aes(), size = 2)
p0 <- p0 + theme(axis.text.x = element_blank(), axis.text.y=element_text(size=12), legend.position = "none")
p0 <- p0 + labs(x = "", y = metric0)
p0 <- p0 + scale_colour_viridis_d(legend_title, option = "C")
p0 <- p0 + scale_x_datetime(date_breaks = "2 hours", date_labels = "%H:%M")



metric1 <- "RQ"

measurement_one <- all_animals %>%  select(metric1)
df<-as.data.frame(measurement_one[[metric1]])
legend_title <- "Animal ID"

p1 <- ggplot(data = all_animals,aes(x=as.POSIXct(StartTime),y=measurement_one[[metric1]]))
p1 <- p1 + geom_point(aes(group=as.factor(Animal_ID), color=as.factor(Animal_ID)), size = 3)
p1 <- p1 + theme(axis.text.x = element_blank(), axis.text.y=element_text(size=12), legend.position = "none")
p1 <- p1 + geom_smooth(data=df$V1, method='loess', span=.4, level=0.99)
p1 <- p1 + labs(x = "", y = metric1)
p1 <- p1 + scale_colour_viridis_d(legend_title, option = "C")
p1 <- p1 + scale_x_datetime(date_breaks = "2 hours", date_labels = "%H:%M")
p1 <- p1 + geom_hline(yintercept = 0.8907387)
p1 <- p1 + ylim(0.5, 2)



metric2 <- "EE"

measurement_two <- all_animals %>%  select(metric2)
df<-as.data.frame(measurement_two[[metric2]])
legend_title <- "Animal ID"

p2 <- ggplot(data = all_animals,aes(x=as.POSIXct(StartTime),y=measurement_two[[metric2]]))
p2 <- p2 + geom_point(aes(group=as.factor(Animal_ID), color=as.factor(Animal_ID)), size = 3,)
p2 <- p2 + theme(axis.text.x = element_blank(), axis.text.y=element_text(size=12))
p2 <- p2 + geom_smooth(data=df$V1, method='loess', span=.4, level=0.99)
p2 <- p2 + labs(x = "", y = metric2)
p2 <- p2 + scale_colour_viridis_d(legend_title, option = "C")
p2 <- p2 + scale_x_datetime(date_breaks = "2 hours", date_labels = "%H:%M")

metric3 <- "H2Omg"

measurement_three <- all_animals %>%  select(metric3)
df<-as.data.frame(measurement_three[[metric3]])
legend_title <- "Animal ID"

p3 <- ggplot(data = all_animals,aes(x=as.POSIXct(StartTime),y=measurement_three[[metric3]]))
p3 <- p3 + geom_point(aes(group=as.factor(Animal_ID), color=as.factor(Animal_ID)), size = 3)
p3 <- p3 + theme(axis.text.y=element_text(size=12), legend.position = "none", axis.text.x=element_text(size=12))
p3 <- p3 + geom_smooth(data=df$V1, method='loess', span=.1, level=0.99)
p3 <- p3 + labs(x = "", y = metric3)
p3 <- p3 + scale_colour_viridis_d(legend_title, option = "C")
p3 <- p3 + scale_x_datetime(date_breaks = "2 hours", date_labels = "%H:%M")

p0/p1/p2/p3



```

![](https://i.imgur.com/O83d7pI.png)
