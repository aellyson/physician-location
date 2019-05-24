***** Migration Patterns *****

***** PRELIMINARIES *****
cd "/Users/aliceellyson/Desktop/Dissertation/Data/Chap2"
set mem 8g
use Phy_Clean.dta
xtset id year

***** ADD/EDIT/MANAGE VARIABLES *****
drop pmsa_msa msa division pra region county

* Generate outcome binary variable (dependent variable) RELOCATION
rename fips_state locat
gen lag_locat=l.locat
gen locat_change=1 if locat~=lag_locat
replace locat_change=0 if locat==lag_locat
replace locat_change=. if lag_locat==.
drop lag_locat
rename locat_chang relo
la var relo "=1 if physician relocated to a diff state"
rename locat fips_state

* Recent Move
gen recentmove=0
by id: replace recentmove=1 if fips_state~=l.fips_state 
by id: replace recentmove=1 if fips_state~=l2.fips_state 
by id: replace recentmove=1 if fips_state~=l3.fips_state 
by id: replace recentmove=0 if l.relo==.
by id: replace recentmove=0 if l2.relo==.
by id: replace recentmove=. if relo==.

* REFORM VAR - binary variables equal to one if a particular reform was effective
* in the state where the physician was located in year t, zero if not
la var r_cn "NEDC_t"
la var r_cp "PDC_t"
la var r_cs "CSR_t"
la var r_pp "MPP_t"
la var r_pe "PER_t"
la var r_js "JSLR_t"

* Create time dummies
forvalues yr=1992/2007 {
gen d`yr'=1 if year==`yr'
replace d`yr'=0 if year!=`yr'
la var d`yr' "=1 if year==`yr'"
}

* Malpractice vars
la var m_pay "Malpractice Payments m_pay_{t}"
la var num_act_ref "Total Number of Active Reforms num_act_ref_{t}"

* Lagged variables for all vars included in the model
foreach var in age exper m_pay num_act_ref diff_clim sameloc recentmove ///
lpop inc_pc civ_unemp urbanpct fmres_prog hmo_pc r_cn r_cp r_cs r_js r_pe r_pp ///
low mid_cn mid_cp high {
gen `var'_1=l.`var'
la var `var'_1 "`var'_{t-1}"
}

* Identify previous state (t-1) for each observation
gen prev_st=l.fips_state
la var prev_st "State Physician lived in last year"
la val prev_st fips_st

* New Low
gen low_jr=1 if r_cn==1 | r_cp==1 | (r_cn==1 & r_cp==1)
replace low_jr=0 if low_jr==.
gen low_jr_1=l.low_jr

***** GLOBALS *****
* Globals - DEP VARS
global y relo
* Globals - INDEP VARS
global time d1993-d2006
global mal m_pay_1 num_act_ref_1
global cat mid_cn_1 mid_cp_1 high_1
global x1 female age_1 exper_1 do
global x age_1 exper_1
global s lpop_1 inc_pc_1 civ_unemp_1 urbanpct_1 fmres_prog_1 hmo_pc_1
global oppcost recentmove_1 diff_clim_1 sameloc_1

***** Restrict Sample to Physician Years of Relocation
* Identify restricted sample of physicians who move at some point during the panel
clogit ${y} ${cat} ${x} ${s} ${oppcost} ${time} , group(id)
* res_samp is =1 if used in clogit estimations
gen res_samp=e(sample)
la var res_samp "=1 if physician moves at some point in the sample"
keep if res_samp==1


***** Migrating Binaries for Premiums*****
* Run RELO_MAIN.do from beginning to globals  
gen prem_tot_th = prem_tot * 1000
sum prem_tot_th, detail
		* Full Sample 
			* High - Premium > 75% distribution -  14161.38
			* MED_High - Premium > 50% distribution - 8451.5
			* Med_Low - Premium > 25% distribution - 5577
			* Low - Premium < 25% distribution - 5577 
* Restrict Sample to Physician Years of Relocation
* Identify restricted sample of physicians who move at some point during the panel
clogit ${y} ${cat} ${x} ${s} ${oppcost} ${time} , group(id)
* res_samp is =1 if used in clogit estimations
gen res_samp=e(sample)
la var res_samp "=1 if physician moves at some point in the sample"
keep if res_samp==1
* DEFINE PREMIUM GROUPS
sum prem_tot_th, detail
	* Movers Sample 
		* High - Premium > 75% distribution -  14161.38
		* MED_High - Premium > 50% distribution - 8497.625
		* Med_Low - Premium > 25% distribution - 5592.125
		* Low - Premium < 25% distribution - 5592.125
		
* NOTE: Premium groups are very similar in both full sample and movers sample
 
* NOTE: prem_tot_1 is prem_tot_{t-1}
gen lag_prem = prem_tot_1 * 1000

* Generate variable indicating physician movement based on state malpractice category
* Identify previous state (t-1) for each observation
gen prev_st=l.fips_state
la var prev_st "State Physician lived in last year"
la val prev_st fips_st

			* High - Premium > 75% distribution -  14161.38
			* MED_High - Premium > 50% distribution - 8451.5
			* Med_Low - Premium > 25% distribution - 5577
			* Low - Premium < 25% distribution - 5577 

foreach var in moveH_L moveH_ML moveH_MH moveH_H ///
				moveMH_L moveMH_ML moveMH_MH moveMH_H ///
				moveML_L moveML_ML moveML_MH moveML_H ///
				moveL_L moveL_ML moveL_MH moveL_H {
	gen `var'= . 
	}
						
* From High_t-1 to....
* Low_t
replace moveH_L=1 if lag_prem >= 14161.38 & prem_tot_th < 5577 & relo == 1 
la var moveH_L "Physician moved from High_t-1 to Low_t state"
* MEDLow_t
replace moveH_ML=1 if lag_prem >= 14161.38 & (prem_tot_th >= 5577 & prem_tot_th < 8451.5) & relo == 1 
la var moveH_ML "Physician moved from High_t-1 to MedLow_t state"
* MEDHigh_t
replace moveH_MH=1 if lag_prem >= 14161.38 & (prem_tot_th >= 8451.5 & prem_tot_th < 14161.38) & relo == 1 
la var moveH_MH "Physician moved from High_t-1 to MedHigh_t state"
* High_t
replace moveH_H=1 if lag_prem >= 14161.38 & prem_tot_th >= 14161.38 & relo == 1 
la var moveH_H "Physician moved from High_t-1 to High_t state"

			* High - Premium > 75% distribution -  14161.38
			* MED_High - Premium > 50% distribution - 8451.5
			* Med_Low - Premium > 25% distribution - 5577
			* Low - Premium < 25% distribution - 5577 
	
* From MED_High_t-1 to....
* Low_t
replace moveMH_L=1 if (lag_prem >= 8451.5 & lag_prem < 14161.38) & prem_tot_th < 5577 & relo == 1 
la var moveMH_L "Physician moved from MedHigh_t-1 to Low_t state"
* MEDLow_t
replace moveMH_ML=1 if (lag_prem >= 8451.5 & lag_prem < 14161.38) & (prem_tot_th >= 5577 & prem_tot_th < 8451.5) & relo == 1 
la var moveMH_ML "Physician moved from MedHigh_t-1 to MedLow_t state"
* MEDHigh_t
replace moveMH_MH=1 if (lag_prem >= 8451.5 & lag_prem < 14161.38) & (prem_tot_th >= 8451.5 & prem_tot_th < 14161.38) & relo == 1 
la var moveMH_MH "Physician moved from MedHigh_t-1 to MedHigh_t state"
* High_t
replace moveMH_H=1 if (lag_prem >= 8451.5 & lag_prem < 14161.38) & prem_tot_th >= 14161.38 & relo == 1 
la var moveMH_H "Physician moved from MedHigh_t-1 to High_t state"

			* High - Premium > 75% distribution -  14161.38
			* Med_High - Premium > 50% distribution - 8451.5
			* Med_Low - Premium > 25% distribution - 5577
			* Low - Premium < 25% distribution - 5577 

* From MED_Low_t-1 to....
* Low_t
replace moveML_L=1 if (lag_prem >= 5577 & lag_prem < 8451.5) & prem_tot_th < 5577 & relo == 1 
la var moveML_L "Physician moved from MedLow_t-1 to Low_t state"
* MEDLow_t
replace moveML_ML=1 if (lag_prem >= 5577 & lag_prem < 8451.5) & (prem_tot_th >= 5577 & prem_tot_th < 8451.5) & relo == 1 
la var moveML_ML "Physician moved from MedLow_t-1 to MedLow_t state"
* MEDHigh_t
replace moveML_MH=1 if (lag_prem >= 5577 & lag_prem < 8451.5) & (prem_tot_th >= 8451.5 & prem_tot_th < 14161.38) & relo == 1 
la var moveML_MH "Physician moved from MedLow_t-1 to MedHigh_t state"
* High_t
replace moveML_H=1 if (lag_prem >= 5577 & lag_prem < 8451.5) & prem_tot_th >= 14161.38 & relo == 1 
la var moveML_H "Physician moved from MedLow_t-1 to High_t state"

			* High - Premium > 75% distribution -  14161.38
			* Med_High - Premium > 50% distribution - 8451.5
			* Med_Low - Premium > 25% distribution - 5577
			* Low - Premium < 25% distribution - 5577 
			
* From Low_t-1 to....
* Low_t
replace moveL_L=1 if lag_prem < 5577 & prem_tot_th < 5577 & relo == 1 
la var moveL_L "Physician moved from Low_t-1 to Low_t state"
* MEDLow_t
replace moveL_ML=1 if lag_prem < 5577 & (prem_tot_th >= 5577 & prem_tot_th < 8451.5) & relo == 1 
la var moveL_ML "Physician moved from Low_t-1 to MedLow_t state"
* MEDHigh_t
replace moveL_MH=1 if lag_prem < 5577 & (prem_tot_th >= 8451.5 & prem_tot_th < 14161.38) & relo == 1 
la var moveL_MH "Physician moved from Low_t-1 to MedHigh_t state"
* High_t
replace moveL_H=1 if lag_prem < 5577 & prem_tot_th >= 14161.38 & relo == 1 
la var moveL_H "Physician moved from Low_t-1 to High_t state"


edit if relo==1 & moveH_L== moveH_ML== moveH_MH== moveH_H== moveMH_L== moveMH_ML== moveMH_MH== moveMH_H== moveML_L== moveML_ML== moveML_MH== moveML_H== moveL_L== moveL_ML== moveL_MH== moveL_H==.


foreach var in moveH_L moveH_ML moveH_MH moveH_H ///
				moveMH_L moveMH_ML moveMH_MH moveMH_H ///
				moveML_L moveML_ML moveML_MH moveML_H ///
				moveL_L moveL_ML moveL_MH moveL_H {
 di "`:var l `var''"
 count if `var'==1
 }
 count if relo==1
 
foreach var in moveH_L moveH_ML moveH_MH moveH_H ///
				moveMH_L moveMH_ML moveMH_MH moveMH_H ///
				moveML_L moveML_ML moveML_MH moveML_H ///
				moveL_L moveL_ML moveL_MH moveL_H {
 di "`:var l `var''"
 count if `var'==1 & exper<10
 }
 count if relo==1 & exper<10
 
 foreach var in moveH_L moveH_ML moveH_MH moveH_H ///
				moveMH_L moveMH_ML moveMH_MH moveMH_H ///
				moveML_L moveML_ML moveML_MH moveML_H ///
				moveL_L moveL_ML moveL_MH moveL_H {
 di "`:var l `var''"
 count if `var'==1 & exper>=10
 }
 count if relo==1 & exper>=10
 
* Group
 foreach var in moveH_L moveH_ML moveH_MH moveH_H ///
				moveMH_L moveMH_ML moveMH_MH moveMH_H ///
				moveML_L moveML_ML moveML_MH moveML_H ///
				moveL_L moveL_ML moveL_MH moveL_H {
 di "`:var l `var''"
 count if `var'==1 & mode==2
 }
 
 * Solo
 foreach var in moveH_L moveH_ML moveH_MH moveH_H ///
				moveMH_L moveMH_ML moveMH_MH moveMH_H ///
				moveML_L moveML_ML moveML_MH moveML_H ///
				moveL_L moveL_ML moveL_MH moveL_H {
 di "`:var l `var''"
 count if `var'==1 & mode==0
 }
 
 
 
 *********************************************
***** Migrating Binaries for REFORMS *****
* Generate variable indicating physician movement based on state malpractice category
* From High_t-1 to....
* Low_t
gen moveHL=1 if high_1==1 & low==1
la var moveHL "Physician moved from High_t-1 to Low_t state"
* Mid_cn_t
gen moveHCN=1 if high_1==1 & mid_cn==1
la var moveHCN "Physician moved from High_t-1 to mid_cn_t state"
* Mid_cp_t
gen moveHCP=1 if high_1==1 & mid_cp==1
la var moveHCP "Physician moved from High_t-1 to mid_cp_t state"
* High_t
gen moveHH=1 if high_1==1 & high==1
la var moveHH "Physician moved from High_t-1 to High_t state"

* From Mid_cn_t-1 to....
* Low_t
gen moveCNL=1 if mid_cn_1==1 & low==1
la var moveCNL "Physician moved from Mid_cn_t-1 to Low_t state"
* Mid_cn_t
gen moveCNCN=1 if mid_cn_1==1 & mid_cn==1
la var moveCNCN "Physician moved from Mid_cn_t-1 to Mid_cn_t state"
* Mid_cp_t
gen moveCNCP=1 if mid_cn_1==1 & mid_cp==1
la var moveCNCP "Physician moved from Mid_cn_t-1 to Mid_cp_t state"
* High_t
gen moveCNH=1 if mid_cn_1==1 & high==1
la var moveCNH "Physician moved from Mid_cn_t-1 to High_t state"

* From Mid_cp_t-1 to....
* Low_t
gen moveCPL=1 if mid_cp_1==1 & low==1
la var moveCPL "Physician moved from Mid_cp_t-1 to Low_t state"
* Mid_cn_t
gen moveCPCN=1 if mid_cp_1==1 & mid_cn==1
la var moveCPCN "Physician moved from Mid_cp_t-1 to Mid_cn_t state"
* Mid_cp_t
gen moveCPCP=1 if mid_cp_1==1 & mid_cp==1
la var moveCPCP "Physician moved from Mid_cp_t-1 to Mid_cp_t state"
* High_t
gen moveCPH=1 if mid_cp_1==1 & high==1
la var moveCPH "Physician moved from Mid_cp_t-1 to High_t state"

* From Low_t-1 to....
* Low_t
gen moveLL=1 if low_1==1 & low==1
la var moveLL "Physician moved from Low-1 to Low_t state"
* Mid_cn_t
gen moveLCN=1 if low_1==1 & mid_cn==1
la var moveLCN "Physician moved from Low_t-1 to Mid_cn_t state"
* Mid_cp_t
gen moveLCP=1 if low_1==1 & mid_cp==1
la var moveLCP "Physician moved from Low_t-1 to Mid_cp_t state"
* High_t
gen moveLH=1 if low_1==1 & high==1
la var moveLH "Physician moved from Low_t-1 to High_t state"

foreach var in moveHH moveHCN moveHCP moveHL moveCNH moveCNCN moveCNCP moveCNL ///
moveCPH moveCPCN moveCPCP moveCPL moveLH moveLCN moveLCP moveLL {
 count if `var'==1
 }
 
 foreach var in moveHH moveHCN moveHCP moveHL moveCNH moveCNCN moveCNCP moveCNL ///
moveCPH moveCPCN moveCPCP moveCPL moveLH moveLCN moveLCP moveLL {
 count if `var'==1 & exper<10
 }

***** Two categories ****
* Move from High_t-1 to Low_t
gen moveHL=1 if high_1==1 & low_jr==1
* Move from High_t-1 to High_t
gen moveHH=1 if high_1==1 & high==1
* Move from Low_t-1 to Low_t
gen moveLL=1 if low_jr_1==1 & low_jr==1
* Move from Low_t-1 to High_t
gen moveLH=1 if low_jr_1==1 & high==1

foreach var in moveHH moveHL moveLH moveLL {
 count if `var'==1
 }


 
 
 *** OLD? 

***** Two categories ****
* Move from High_t-1 to Low_t
gen moveHL=1 if high_1==1 & low_jr==1
* Move from High_t-1 to High_t
gen moveHH=1 if high_1==1 & high==1
* Move from Low_t-1 to Low_t
gen moveLL=1 if low_jr_1==1 & low_jr==1
* Move from Low_t-1 to High_t
gen moveLH=1 if low_jr_1==1 & high==1

foreach var in moveHH moveHL moveLH moveLL {
 count if `var'==1
 }
