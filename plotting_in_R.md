---
tags: Rscript, macmanes
---

# Rscript for plotting data

```
#load packages

library(tidyverse)
library(lubridate)
library(readr)	


# import data

datafile <- "/Users/macmanes/Downloads/traveling_mouse_all.csv"

# should not have to change below except how you name the dataset e.g., travelingmouse

travelingmouse <- read_csv(datafile, 
	col_types = cols(Animal = col_character(), 
        StartDate = col_date(format = "%m/%d/%y"), 
        StartTime = col_time(format = "%H:%M:%S")))
View(travelingmouse)

# plot VO2 in this case. 

travelingmouse %>% 
	ggplot(aes(as.POSIXct(with(male_data, StartDate + hms(StartTime))), VO2, group=Animal, color=Animal)) + 
	geom_line() + 
	labs(x = "") + 
	scale_color_manual(values=c("#FF5733", "#FFFF33", "#61FF33", "#33E3FF", "#3383FF", "#FF33FF", "#050005"))
```