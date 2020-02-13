---
tags: macro, respirometry
---

# macro_extract_means_300_sec_per_cage with additional raw data
this is the 2nd macro that you have to run, after you've run the the 1st

```
[macro 1]
 ' Barbara Joos- Data Extraction macro for UNH system.
'This macro extracts mean VO2, VCO2, and VH2O for each animal for each mesurement period and writes these to the ExpeData internal spreadsheet. Whne the Macro is complete, Use View Spreadsheet and save the spreadsheet to a file.
'The maco uses marker selction tools to select each data section and find the most level 50% of the section.
'Units will be VO2 ml/min, VCO2 ml/min, VH2O ml/min becase flow rate was in ml/min.

for {n} = 0 to 6
if exists_marker {n} then
Select marker_anchor {n} 300 samples left search_all_samples
active channel Respchan
getstats mean
writestats StartTime to spreadsheet
assign column_title StartTime
writestats StartDate to spreadsheet
Assign column_title StartDate
writestats Mean to spreadsheet
assign column_title Animal
active channel VO2
findwindow level selected width 150
select selectedwindow selected
getstats mean
writestats Mean to spreadsheet
Assign Column_title VO2
writestats StdDev to spreadsheet
Assign column_title SD_VO2
active channel VCO2
getstats mean
writestats Mean to spreadsheet
Assign column_title VCO2
writestats StdDev to spreadsheet
Assign column_title SD_VCO2
active channel Deg_C
getstats mean
writestats Mean to spreadsheet
Assign column_title Deg_C
active channel BP
getstats mean
writestats Mean to spreadsheet
Assign column_title BP
active channel CO2
getstats mean
writestats Mean to spreadsheet
Assign column_title CO2
active channel SS4_FR
getstats mean
writestats Mean to spreadsheet
Assign column_title SS4_FR
active channel Vh2O
getstats mean
writestats Mean to spreadsheet
Assign column_title H2O
writestats StdDev to spreadsheet
Assign Column_title SD_H2O
writestats newline to spreadsheet
select change_right +152

For more marker {n}
Select marker_anchor {n} 300 samples left search_from_selected
active channel Respchan
getstats mean
writestats StartTime to spreadsheet
assign column_title StartTime
writestats StartDate to spreadsheet
Assign column_title StartDate
writestats Mean to spreadsheet
assign column_title Animal
active channel VO2
findwindow level selected width 150
select selectedwindow selected
getstats mean
writestats Mean to spreadsheet
Assign Column_title VO2
writestats StdDev to spreadsheet
Assign column_title SD_VO2
active channel VCO2
getstats mean
writestats Mean to spreadsheet
Assign column_title VCO2
writestats StdDev to spreadsheet
Assign column_title SD_VCO2
active channel Deg_C
getstats mean
writestats Mean to spreadsheet
Assign column_title Deg_C
active channel BP
getstats mean
writestats Mean to spreadsheet
Assign column_title BP
active channel CO2
getstats mean
writestats Mean to spreadsheet
Assign column_title CO2
active channel SS4_FR
getstats mean
writestats Mean to spreadsheet
Assign column_title SS4_FR
active channel Vh2O
getstats mean
writestats Mean to spreadsheet
Assign column_title H2O
writestats StdDev to spreadsheet
Assign Column_title SD_H2O
writestats newline to spreadsheet
select change_right +152
next Marker
end if
Next {n}

Prompt ' Save the spreadsheet'
[/macro 1]
```