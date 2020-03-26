---
tags: allometry, Rscript
---

# exploring allometry relationships

this is the code for subsetting means for each individual a given hour

```
night <- with(all_animals ,all_animals[ hour( StartTime ) >= 0 & hour( StartTime ) < 6 , ] )

meanEE <- night %>% group_by(Animal_ID) %>% summarise(EE = mean(EE)) %>% select(EE)
```

next i need a vector of weights from the animals that is in the same order. 

```
animalweight <- night %>% group_by(Animal_ID) %>% summarise(weight = mean(weight)) %>% select(weight)
```

now plotting 

```
plot(meanEE[[1]] ~ animalweight[[1]], main = "EE 0000-0600", ylab="EE", xlab="weight")
abline(lm(meanEE[[1]] ~ animalweight[[1]]))
```

regression

```
summary(lm(meanEE[[1]] ~ animalweight[[1]]))
```

#### All together

```
night <- with(all_animals ,all_animals[ hour( StartTime ) >= 0 & hour( StartTime ) < 6 , ] )
day <- with(all_animals ,all_animals[ hour( StartTime ) >= 13 & hour( StartTime ) < 19 , ] )

animalweight <- night %>% group_by(Animal_ID) %>% summarise(weight = mean(weight)) %>% select(weight)

# this is the code to change - when changing the thing you want to plot. 

meanVCO2 <- day %>% group_by(Animal_ID) %>% summarise(VCO2 = mean(VO2)) %>% select(VCO2)

plot(meanVCO2[[1]] ~ animalweight[[1]], main = "meanVCO2 1300-1900", ylab="meanVCO2", xlab="weight")
abline(lm(meanVCO2[[1]] ~ animalweight[[1]]))
summary(lm(meanVCO2[[1]] ~ animalweight[[1]]))

```

night
![](https://i.imgur.com/9e9Xa2k.png)
![](https://i.imgur.com/PF9Sysi.png)
![](https://i.imgur.com/hLJqJcA.png)
![](https://i.imgur.com/XjHS0HA.png)

day
![](https://i.imgur.com/P20my8I.png)
![](https://i.imgur.com/8G8iq7t.png)
![](https://i.imgur.com/LLOxXJZ.png)
![](https://i.imgur.com/l5pyhuO.png)

