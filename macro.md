---
tags: macro, respirometry
---

# macro_200_per_cage
This is the curret production macro, from USB drive 

```
[macro 1]
 ' Barbara Joos December 18, 2019 for University of New Hampshire
' Macro for FMS with multiple SS-4 and single multiplexed flow channel. Using equations for pull system.  Baseline is marker B, Muliplexer chamber 8
'This macro goes up to calculation of VO2, VCO2, VH2O and saves a modified file.

'First create a criterion channel to use for interpolations.  
transform general all_samples expression Respchan = 7
active channel Respchan
for {n} = 0 to 6
if exists_marker {n} then
Select marker_anchor {n} 300 samples left search_all_samples
select selectedwindow selected
transform general selected expression {n}
select change_right +10
	
For more marker {n}
Select marker_anchor {n} 300 samples left search_from_selected
transform general selected expression {n}
select change_right +10
Next marker 

end if
Next {n}

'do the smoothing and lag corrections on O2, CO2, WVP
active channel O2
correction smooth_nearest_neighbor all_samples width 5 repeat 1
correct lag all_samples shift 18
active channel CO2
correction smooth_nearest_neighbor all_samples width 5 repeat 1
correct lag all_samples shift 10
active channel H20_WVP
correct lag all_samples shift 12

'Do the response corrections on O2, CO2, WVP
active channel O2
transform z_transform all_samples z 7.859603E-02
make_temp_data_copy all_samples
correction smooth_nearest_neighbor all_samples width 9 repeat 2
active channel CO2
transform z_transform all_samples z .3
active channel H20_WVP	
transform z_transform all_samples z 7.745837E-02
correction smooth_nearest_neighbor all_samples width 9 repeat 2

'Smooth the BP channel and flow channel to use for the diltuion correction
active channel BP
correction smooth_nearest_neighbor all_samples width 9 repeat 3
active channel Chamber_flow
correction smooth_nearest_neighbor all_samples width 9 repeat 1

' Calculate dry O2 and CO2 and dry flow rate.
active channel O2
transform general all_samples expression O2dry= O2*BP/(BP-H20_WVP)
transform general all_samples expression CO2dry= CO2*BP/(BP-H20_WVP)
active channel Chamber_flow
transform general all_samples expression FRdry= Chamber_flow*(BP-H20_WVP)/BP

'span the O2 channel to 0.2095
active channel O2dry
make_temp_data_copy all_samples
correction baseline_single_markers all_samples marker B average_left samples 300 search_type level search_percent 25 catmull_rom copy_only extrapolate_none  span_value 0.2095 grr
overwrite_data_with_copy all_samples

' copy the spanned O2 channel to a new channel and calculate delta O2.
create channel
assign title c_O2dry = delta_O2
active channel delta_O2
make_temp_data_copy all_samples
correction baseline_single_markers all_samples marker B average_left samples 300 search_type level search_percent 25 catmull_rom copy_only extrapolate_none  grr
overwrite_data_with_copy all_samples
transform linear all_samples slope -1 intercept 0

'Convert CO2% to FCO2
active channel CO2dry
transform linear all_samples slope  .01 intercept 0

'Create an FiCO2 channel from the baseline sections with the interpolated channel tool.
create interpolated_channel source_channel CO2dry destination_channel FiCO2 value_channel Respchan = 7 selection Most_level percent  50 interpolation Catmull_Rom_spline

'Copy CO2dry to a new channel and create delta CO2
active channel CO2dry
create channel
assign title c_CO2dry = deltaCO2
active channel deltaCO2
make_temp_data_copy all_samples
correction baseline_single_markers all_samples marker B average_left samples 300 search_type level search_percent 25 catmull_rom copy_only extrapolate_none  grr
overwrite_data_with_copy all_samples

'Convert water vapor pressure to FH2O - divide by BP
active channel H20_WVP
transform general all_samples expression FH2O =H20_WVP/BP

'Create an FiH2O channel with the interpolated channel tool
active channel FH2O
create interpolated_channel source_channel FH2O destination_channel FiH2O value_channel Respchan = 7 selection Most_level percent  50 interpolation Catmull_Rom_spline

'Copy FH2O to a new channel and create deltaH2O
active channel FH2O
create channel
assign title c_FH2O = deltaH2O
active channel deltaH2O
make_temp_data_copy all_samples
correction baseline_criterion_channel all_samples copy_only criterion_channel Respchan criterion_value 7 search_type level search_percent 50 catmull_rom extrapolate_none
overwrite_data_with_copy all_samples

'Now calculate VO2, VCO2 and VH2O according to equations in Lighton 2008 11.7, 11.8, 11.9
transform general all_samples expression VO2 = FRdry*(delta_O2-deltaCO2*0.2095)/(1-0.2095)
transform general all_samples expression VCO2 = FRdry*(DeltaCO2 +delta_O2*FiCO2)/(1+FiCO2)
transform general all_samples expression Vh2O = FRdry*deltaH2O/(1-FiH2O)

'Save the calculations to a new file with prefix Mod-
save all_samples prefix mod-
[/macro 1]

```