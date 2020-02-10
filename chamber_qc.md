---
tags: analysis, macmanes, respiomatry
---

# Do some chambers have higher SD??
This is done on "ovenight.csv" which was 2 days recodring of empty cages, without water, baseline calc every 3rd cage. 


The code I used

```
waterrandomsampling %>% 
	group_by(Animal) %>%
	summarise(mean = mean(SD_VO2))
```

### These are the average SDs of the cages

##### Water: there does not seem to be any specific cages with higher variance
A0=0.01819597
A1=0.01754496
A2=0.01879907
A3=0.01838103
A4=0.01769739
A5=0.01773998
A6=0.01806375

##### CO2: there does not seem to be any specific cages with higher variance
A0=0.009228428
A1=0.0090518
A2=0.009335356
A3=0.010005
A4=0.009171164
A5=0.009080128
A6=0.009827624

##### O2: there does not seem to be any specific cages with higher variance
A0=0.02458157
A1=0.02286483
A2=0.02571785
A3=0.02597231
A4=0.02727174
A5=0.02393258
A6=0.02677925

### Temporal Patterns: these are plots of the SD over time. 

##### Water: none
![](https://i.imgur.com/WrHLRvm.png)

##### CO2: none

![](https://i.imgur.com/VZSzWwS.png)

##### O2: there does seem to be higher variability early on in recording??

![](https://i.imgur.com/CLDUGFj.png)
