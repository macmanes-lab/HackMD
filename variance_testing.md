# 120 sec versus 150 sec


#### 150 sec, calculate variance only midnight to 6AM
n=98 measurements in this range

```
cages <- with(cages012346_all ,cages012346_all[ hour( StartTime ) >= 0 & hour( StartTime ) < 6 , ] )

> var(cages$H2Omg, na.rm = TRUE)
[1] 0.001656899
> var(cages$VO2, na.rm = TRUE)
[1] 0.1785384
> var(cages$VCO2, na.rm = TRUE)
[1] 0.1279258
> var(cages$EE, na.rm = TRUE)
[1] 0.015276
```
#### 150 sec, calculate variance only midnight to 4pm to 10pm

```> var(cages$H2Omg, na.rm = TRUE)
[1] 0.005458669
> var(cages$VO2, na.rm = TRUE)
[1] 0.1220401
> var(cages$VCO2, na.rm = TRUE)
[1] 0.08778607
> var(cages$EE, na.rm = TRUE)
[1] 0.01044119
```

#### 120 sec, calculate variance only midnight to 6AM
n=89 measurements in this range

```
> cages <- with(cages012346_120sec ,cages012346_120sec[ hour( StartTime ) >= 0 & hour( StartTime ) < 6 , ] )
> 
> 
> var(cages$H2Omg, na.rm = TRUE)
[1] 0.005184012
> var(cages$VO2, na.rm = TRUE)
[1] 0.2078887
> var(cages$VCO2, na.rm = TRUE)
[1] 0.1571118
> var(cages$EE, na.rm = TRUE)
[1] 0.01792776
```
#### 120 sec, calculate variance only midnight to 4pm to 10pm

```
> var(cages$H2Omg, na.rm = TRUE)
[1] 0.005148466
> var(cages$VO2, na.rm = TRUE)
[1] 0.129211
> var(cages$VCO2, na.rm = TRUE)
[1] 0.1010742
> var(cages$EE, na.rm = TRUE)
[1] 0.0112287
```