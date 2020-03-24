---
tags: real experiments
---

# Male Control 10Mar2020

##### this is the same experiment as 5Mar, but trying to make sure if the cage tubig was tight, particularly in cage 0

Cage 0 - 19.1g - 22M

Cage 1 - 18.2g - 25M

Cage 2 - 23.2g - 53M

Cage 3 - 22.9g - 80M

Cage 4 - 22.3g - 1047M

Cage 5 - 22.1g - 23M

Cage 6 - 23.0g - 1048M

```
library(tidyverse)
library(lubridate)
library(readr)	

cageweight0 <- 19.1
cageweight1 <- 18.2
cageweight3 <- 22.9
cageweight2 <- 23.1
cageweight4 <- 22.3
cageweight5 <- 22.1
cageweight6 <- 23.0

animalID0 <- "22M"
animalID1 <- "25M"
animalID3 <- "80M"
animalID2 <- "53M"
animalID4 <- "1047M"
animalID5 <- "23M"
animalID6 <- "1048M"

datafile <- "~/Dropbox/Cactus_Mouse_Physiology/data/10Mar20/mar10.csv"
mar10 <- read_csv(datafile, 
col_types = cols(Animal = col_double(), 
    StartDate = col_date(format = "%m/%d/%Y"),
    deltaCO2 = col_double(), 
    deltaH2O = col_double(),
    H2Oml = col_double(),
    VCO2 = col_double(),
    StartTime = col_time(format = "%H:%M:%S")))

'%!in%' <- function(x,y)!('%in%'(x,y))

mar10 <- mar10 %>% 
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
mutate(corEE = EE/weight) %>%
mutate(sex = "Male")

metric <- "corEE"

target <- c(0,1,2,4,5,6)
cages10mar <- mar10 %>% filter(animal %in% target)

measurement <- cages10mar %>%  select(metric)
df<-as.data.frame(measurement[[metric]])
legend_title <- "Animal ID"

p <- ggplot(data = cages10mar,aes(x=as.POSIXct(StartTime),y=measurement[[metric]]))
p <- p + geom_point(aes(group=as.factor(Animal_ID), color=as.factor(Animal_ID)), size = 3)
p <- p + theme_grey(base_size = 15)
p <- p + geom_smooth(data=df$V1, method='loess', span=.3)
p <- p + labs(x = "", y = metric)
p <- p + scale_color_brewer(legend_title, palette="Paired")
p <- p + scale_x_datetime(date_breaks = "2 hours", date_labels = "%H:%M")
#p <- p + geom_hline(yintercept = 0.8907387, color='coral', size=2)
p
```
