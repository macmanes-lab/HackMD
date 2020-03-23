---
tags: real experiments
---

# Male control experiment 26Feb20

0. 1500 start time.
1. Cage sampling every other baseline, 120 sec duration.
2. 14 ml water at start.
3. Animals in all cages
4. plan to run until Monday AM. 

Cage 0 - 22.8g - 10M

Cage 1 - 21.2g - 52M

Cage 2 - 24.2g - 60M

Cage 3 - 21.8g - 34M

Cage 4 - 26.6g - 1046M

Cage 5 - 20.1g - 1100M

Cage 6 - 23.8g - 1099M

```
library(tidyverse)
library(lubridate)
library(readr)	

cageweight0 <- 22.8
cageweight1 <- 21.2
cageweight2 <- 24.2
cageweight3 <- 21.8
cageweight4 <- 26.6
cageweight5 <- 20.1
cageweight6 <- 23.8

animalID0 <- "10M"
animalID1 <- "52M"
animalID2 <- "61M"
animalID3 <- "34M"
animalID4 <- "1046M"
animalID5 <- "1100M"
animalID6 <- "1099M"

datafile <- "~/Dropbox/Cactus_Mouse_Physiology/data/26Feb20/feb26.csv"
feb26 <- read_csv(datafile, 
	col_types = cols(Animal = col_double(), 
        StartDate = col_date(format = "%m/%d/%Y"),
        deltaCO2 = col_double(), 
        deltaH2O = col_double(),
        H2Oml = col_double(),
        VCO2 = col_double(),
        StartTime = col_time(format = "%H:%M:%S")))

'%!in%' <- function(x,y)!('%in%'(x,y))

feb26 <- feb26 %>% 
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
	mutate(Animal_ID =
        ifelse(animal == 7, 'baseline',
		ifelse(animal == 0, animalID0, 
		ifelse(animal == 1, animalID1,
		ifelse(animal == 2, animalID2,
		ifelse(animal == 3, animalID3,
		ifelse(animal == 4, animalID4,
		ifelse(animal == 5, animalID5,
		ifelse(animal == 6, animalID6, NA))))))))) %>% 
	mutate(H2Omg_edit = 
		ifelse(hour(StartTime) == 8, H2Omg - 0.001,
		ifelse(hour(StartTime) == 7, H2Omg - 0.001,
		ifelse(hour(StartTime) == 9, H2Omg - 0.002,
		ifelse(hour(StartTime) == 10, H2Omg - 0.002,
		ifelse(hour(StartTime) == 19, H2Omg + 0.001,
		ifelse(hour(StartTime) == 20, H2Omg + 0.001,
		ifelse(hour(StartTime) == 21, H2Omg + 0.001,
		ifelse(hour(StartTime) == 22, H2Omg + 0.001,
		ifelse(hour(StartTime) %!in% c(7,8,9,10,20,21,22,19), H2Omg, NA)))))))))) %>% 
	mutate_at("H2Omg_edit", as.numeric) %>%
	mutate(corEE = EE/weight)  %>%
    mutate(sex = "Male")

metric <- "corEE"

target <- c(0,1,2,3,4,5,6)
cages26feb <- feb26 %>% filter(animal %in% target)
#cages26feb <- with( cages ,cages[ hour( StartTime ) >= 0 & hour( StartTime ) < 4 , ] )

measurement <- cages26feb %>%  select(metric)
df<-as.data.frame(measurement[[metric]])
legend_title <- "Animal ID"

p <- ggplot(data = cages26feb,aes(x=as.POSIXct(StartTime),y=measurement[[metric]]))
p <- p + geom_point(aes(group=as.factor(Animal_ID), color=as.factor(Animal_ID)), size = 3)
p <- p + theme_grey(base_size = 15)
p <- p + geom_smooth(data=df$V1, method='loess', span=.3)
p <- p + labs(x = "", y = metric)
p <- p + scale_color_brewer(legend_title, palette="Paired")
p <- p + scale_x_datetime(date_breaks = "2 hours", date_labels = "%H:%M")
#p <- p + geom_hline(yintercept = 0.8907387)
p
```