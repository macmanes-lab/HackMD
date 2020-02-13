---
tags: macro, respirometry
---

# macro_extract_means_200_sec_per_cage
this is the 2nd macro that you have to run, after you've run the the 1st

```
[macro 1]
 ' Barbara Joos- Data Extraction macro for UNH system.
'This macro extracts mean VO2, VCO2, and VH2O for each animal for each mesurement period and writes these to the ExpeData internal spreadsheet. When the Macro is complete, Use View Spreadsheet and save the spreadsheet to a file.
'The maco uses marker selction tools to select each data section and find the most level 50% of the section.
'Units will be VO2 ml/min, VCO2 ml/min, VH2O ml/min becase flow rate was in ml/min.

for {n} = 0 to 6
if exists_marker {n} then
Select marker_anchor {n} 200 samples left search_all_samples
active channel Respchan
getstats mean
writestats FileName to spreadsheet
assign column_title Filename
writestats StartTime to spreadsheet
assign column_title StartTime
writestats StartDate to spreadsheet
Assign column_title StartDate
writestats Mean to spreadsheet
assign column_title Animal
active channel VO2
findwindow level selected width 100
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
active channel Vh2O
getstats mean
writestats Mean to spreadsheet
Assign column_title H2O
writestats StdDev to spreadsheet
Assign Column_title SD_H2O
writestats newline to spreadsheet
select change_right +102

For more marker {n}
Select marker_anchor {n} 200 samples left search_from_selected
active channel Respchan
getstats mean
writestats FileName to spreadsheet
assign column_title Filename
writestats StartTime to spreadsheet
assign column_title StartTime
writestats StartDate to spreadsheet
Assign column_title StartDate
writestats Mean to spreadsheet
assign column_title Animal
active channel VO2
findwindow level selected width 100
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
active channel Vh2O
getstats mean
writestats Mean to spreadsheet
Assign column_title H2O
writestats StdDev to spreadsheet
Assign Column_title SD_H2O
writestats newline to spreadsheet
select change_right +102
next Marker
end if
Next {n}

Prompt ' Save the spreadsheet'
[/macro 1]
```