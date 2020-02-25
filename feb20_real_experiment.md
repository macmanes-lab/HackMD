---
tags: real experiments
---

# Real experiment 20Feb20

0. 1500 start time.
1. Cage sampling every other baseline, 120 sec duration.
2. 14 ml water at start.
3. Animals in all cages
4. plan to run until Monday AM. 

Cage 0 - 20.1g
Cage 1 - 22.6g
Cage 2 - 21.1g
Cage 3 - 23.1g
Cage 4 - 21.8g
Cage 5 - **need weight at end of experiment**
Cage 6 - 15.4g

water refilled 24Feb20 at 1400. 

## Water

```
datafile <- "/Volumes/4TB_1/Dropbox/Cactus_Mouse_Physiology/data/20Feb20/feb20_4.csv"
feb20_4 <- read_csv(datafile, 
	col_types = cols(Animal = col_double(), 
        StartDate = col_date(format = "%m/%d/%Y"),
        deltaCO2 = col_double(), 
        deltaH2O = col_double(),
        H2Oml = col_double(),
        VCO2 = col_double(),
        StartTime = col_time(format = "%H:%M:%S")))

'%!in%' <- function(x,y)!('%in%'(x,y))

feb20_4 <- feb20_4 %>% 
	mutate(EE = 0.06*(3.941*VO2 + 1.106*VCO2)) %>% 
	mutate(RQ = VCO2/VO2) %>%
	mutate(H2Omg_edit8 = ifelse(hour(StartTime) == 8, H2Omg - 0.09536454, NA)) %>%
	mutate(H2Omg_edit7 = ifelse(hour(StartTime) == 7, H2Omg - 0.06536454, NA)) %>%
	mutate(H2Omg_edit9 = ifelse(hour(StartTime) == 9, H2Omg - 0.02219424, NA)) %>%
	mutate(H2Omg_edit10 = ifelse(hour(StartTime) == 10, H2Omg - 0.03273413, NA)) %>%
	mutate(H2Omg_edit19 = ifelse(hour(StartTime) == 19, H2Omg + 0.002130708, NA)) %>%	
	mutate(H2Omg_edit20 = ifelse(hour(StartTime) == 20, H2Omg + 0.02967473, NA)) %>%
	mutate(H2Omg_edit21 = ifelse(hour(StartTime) == 21, H2Omg + 0.02703453, NA)) %>%
	mutate(H2Omg_edit22 = ifelse(hour(StartTime) == 22, H2Omg + 0.01412046, NA)) %>%
	mutate(H2Omg_edits = ifelse(hour(StartTime) %!in% c(7,8,9,10,20,21,22,19), H2Omg, NA)) %>%
	replace_na(list(H2Omg_edit19 = "", H2Omg_edit20 = "", H2Omg_edit21 = "", H2Omg_edit22 = "", H2Omg_edit10 = "", H2Omg_edit7 = "", H2Omg_edit8 = "",H2Omg_edit9 = "",H2Omg_edits = "")) %>%
	mutate(animal = round(Animal, digits=0)) %>%
	unite("DateTime", StartDate:StartTime, remove = FALSE, sep =  " ") %>% 
	unite(H2Omg_edit, c(H2Omg_edit19, H2Omg_edit20, H2Omg_edit21, H2Omg_edit22, H2Omg_edits, H2Omg_edit10, H2Omg_edit7, H2Omg_edit8, H2Omg_edit9), sep = "", remove = TRUE) %>%
	mutate_at("H2Omg_edit", as.numeric) %>%
	mutate(Animal = NULL)

metric <- "H2Omg"

target <- c(0,1,2,3,4,5,6)
cages <- feb20_4 %>% filter(animal %in% target)
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

#### This is the plot of the H2O data without any correction for 8PM/AM bias.. 

![full water data](https://i.imgur.com/9RGc6M1.png)

#### Maybe we should exclude these times??
We still see the basic pattern

![](https://i.imgur.com/qmWKtFK.png)

#### Maybe we should correct for these biases mathmatically?
```
metric <- "H2Omg_edit"

target <- c(0,1,2,3,4,5,6)
cages <- feb20_4 %>% filter(animal %in% target)
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

![](https://i.imgur.com/ElQXuG3.png)

## VCO2

![](https://i.imgur.com/g9medLC.png)

## VO2

![](https://i.imgur.com/G2lsFEB.png)

## EE

![](https://i.imgur.com/rWkSPDl.png)

## RQ

![](https://i.imgur.com/A4Kvgsc.png)
