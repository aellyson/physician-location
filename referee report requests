***** FAMILY MEDICINE PHYSICIAN RELOCATION *****
* Alice Ellyson, PhD * 
* Referee Requests *
* International Review of Law & Economics *

***** Constructing Premium Data *****
* Alternate Premium Construction Methods
* see prem_construct.do in "/Users/aliceellyson/Desktop/Dissertation/Data/Premiums"
* New premium dataset is prem_st_level_update 

***** Recreate full panel dataset with new constructed premiums *****
cd "/Users/aliceellyson/Desktop/Dissertation/Data/Chap2"

use Phy_Clean.dta
drop prem_tot-med_prem
merge m:1 fips_state year using prem_st_level_update.dta
tab year if _merge==1
tab fips_state if _merge==1

xtset id year
xtdescribe if _merge==1
drop _merge
* Lose 4393 physicians

******************** Data constructions *************************

drop pmsa_msa msa division region county

* Generate outcome binary variable (dependent variable) RELOCATION
gen lag_locat=l.fips_state
by id: gen locat_change=1 if fips_state!=lag_locat
by id: replace locat_change=0 if fips_state==lag_locat
replace locat_change=. if lag_locat==.
drop lag_locat
rename locat_chang relo
la var relo "=1 if physician relocated to a diff state between t-1 and t"

* Recent Move
gen recentmove=0
by id: replace recentmove=1 if l.relo==1 | l2.relo==1 | l3.relo==1
la var recentmove "=1 if physician relocated in last three years"

* Create time dummies
* Note: Going to lose 1992 because of generating lagged vars and relo 
*       Time dummies in regression should start with 1994 (leaving out 1993)
forvalues yr=1993/2007 {
gen d`yr'=1 if year==`yr'
replace d`yr'=0 if year!=`yr'
la var d`yr' "=1 if year==`yr'"
}

* Malpractice vars
la var m_pay "Malpractice Payments m_pay_{t}"
la var num_act_ref "Total Number of Active Reforms num_act_ref_{t}"
* Scale HMO enrollment 
gen hmo_scl=enrltot/1000000
la var hmo_scl "HMO Enrollment (in millions)"
* Scale Premiums
replace prem_tot0 = prem_tot0 / 1000
replace prem_tot_SURG = prem_tot_SURG / 1000
forvalues x=1/3 {
replace prem_tot_`x' = prem_tot_`x'/1000
la var prem_tot_`x' "`x' Malpractice Premiums (in thousands)"
}
la var prem_tot0 "Original Internal Med Premiums (in thousands)"
la var prem_tot_SURG "Surgical Premiums (in thousands)"

* Lagged variables for all vars included in the model
foreach var in prem_tot0 prem_tot_1 prem_tot_2 prem_tot_3 prem_tot_SURG ///
age exper fmres_prog total_phy hmo_scl lpop inc_pc civ_unemp urbanpct ///
recentmove sameloc r_cn r_cp r_pcf {
by id: gen `var'_1=l.`var'
la var `var'_1 "`var'_{t-1}"
}
 ** Time Demeaned vars for RE
foreach var in prem_tot0 prem_tot_1 prem_tot_2 prem_tot_3 prem_tot_SURG ///
age exper fmres_prog total_phy hmo_scl lpop inc_pc civ_unemp urbanpct ///
recentmove sameloc r_cn r_cp r_pcf {
egen `var'bar=mean(`var'), by(id)
}

***** Note: Have not generated absolute premium change and/or pct premium change
			* MUST Adjusted Code below if needed 
			* Generate Absolute Change in premiums
* forvalues y=2/4 {
* forvalues x=1/3 {
* by id: gen prem_chg`y'_`x' = l`x'.prem_tot - l`y'.prem_tot
* la var prem_chg`y'_`x' "Premium Change t-`y' to t-`x'"
* }
* }
* drop prem_chg3_1 prem_chg4_1 prem_chg2_2 prem_chg4_2 prem_chg2_3 prem_chg3_3

* Generate percentage change in premiums
* forvalues y=2/4 {
* forvalues x=1/3 {
* by id: gen prem_grw`y'_`x' = (prem_tot[_n-`x']-prem_tot[_n-`y']) / prem_tot[_n-`y']
* la var prem_grw`y'_`x' "Premium Percentage Change t-`y' to t-`x'"
* }
* }
* drop prem_grw3_1 prem_grw4_1 prem_grw2_2 prem_grw4_2 prem_grw2_3 prem_grw3_3

* Indicators for Premium Increases
* More than 25 percent
* gen prem_g25 = 1 if prem_grw2_1 > 0.25
* replace prem_g25 = 0 if prem_grw2_1 <= 0.25
* la var prem_g25 "Indicator for Premium Growth t-2 to t-1 greater than 25%"
* More than 15 percent
* gen prem_g15 = 1 if prem_grw2_1 > 0.15
* replace prem_g15 = 0 if prem_grw2_1 <= 0.15
* la var prem_g15 "Indicator for Premium Growth t-2 to t-1 greater than 15%"
* More than 35 percent
* gen prem_g35 = 1 if prem_grw2_1 > 0.35
* replace prem_g35 = 0 if prem_grw2_1 <= 0.35
* la var prem_g35 "Indicator for Premium Growth t-2 to t-1 greater than 35%"
* More than 50 percent
* gen prem_g50 = 1 if prem_grw2_1 > 0.5
* replace prem_g50 = 0 if prem_grw2_1 <= 0.5
* la var prem_g50 "Indicator for Premium Growth t-2 to t-1 greater than 50%"
* More than 75 percent
* gen prem_g75 = 1 if prem_grw2_1 > 0.75
* replace prem_g75 = 0 if prem_grw2_1 <= 0.75
* la var prem_g75 "Indicator for Premium Growth t-2 to t-1 greater than 75%"
* More than 100 percent
* gen prem_g100 = 1 if prem_grw2_1 > 1
* replace prem_g100 = 0 if prem_grw2_1 <= 1
* la var prem_g100 "Indicator for Premium Growth t-2 to t-1 greater than 100%"
			
***** GLOBALS *****
* Globals - DEP VARS
global y relo
* Globals - INDEP VARS
global time d1994-d2007
global mal0 prem_tot0_1 prem_tot0bar 
global mal1 prem_tot_1_1 prem_tot_1bar
global mal2 prem_tot_2_1 prem_tot_2bar
global mal3 prem_tot_3_1 prem_tot_3bar
global z prem_tot_SURG_1 prem_tot_SURGbar
global x female age_1 exper_1 do
global x_fe age_1 exper_1
global h1 fmres_prog_1 total_phy_1 hmo_scl_1
global s lpop_1 inc_pc_1 civ_unemp_1 urbanpct_1
global opp recentmove_1 sameloc_1
global ref r_cn_1 r_cp_1
global xbar agebar experbar fmres_progbar total_phybar ///
hmo_sclbar lpopbar inc_pcbar civ_unempbar urbanpctbar recentmovebar samelocbar


***** Test Four Premium Constructions *****
* Use Random Effects Probit for all Tests *

* 0: Original Premium Construction 
qui: probit ${y} ${mal0} ${x} ${h1} ${s} ${opp} ${time} ${xbar}, vce(cl id)
estimates store Prem0
qui: dprobit ${y} ${mal0} ${x} ${h1} ${s} ${opp} ${time} ${xbar}, vce(cl id)
estimates store Prem0_marg

* 1: Companies Reporting In Every Year
qui: probit ${y} ${mal1} ${x} ${h1} ${s} ${opp} ${time} ${xbar}, vce(cl id)
estimates store Prem1
qui: dprobit ${y} ${mal1} ${x} ${h1} ${s} ${opp} ${time} ${xbar}, vce(cl id)
estimates store Prem1_marg

* 2: Companies Reporting in More than One State
qui: probit ${y} ${mal2} ${x} ${h1} ${s} ${opp} ${time} ${xbar}, vce(cl id)
estimates store Prem2
qui: dprobit ${y} ${mal2} ${x} ${h1} ${s} ${opp} ${time} ${xbar}, vce(cl id)
estimates store Prem2_marg

* 3: Companies Reporting Only at the State Level 
qui: probit ${y} ${mal3} ${x} ${h1} ${s} ${opp} ${time} ${xbar}, vce(cl id)
estimates store Prem3
qui: dprobit ${y} ${mal3} ${x} ${h1} ${s} ${opp} ${time} ${xbar}, vce(cl id)
estimates store Prem3_marg

estimates table Prem0 Prem1 Prem2 Prem3, se keep(prem_tot0_1 prem_tot_1_1 ///
	prem_tot_2_1 prem_tot_3_1) 
estimates table Prem0 Prem1 Prem2 Prem3, star keep(prem_tot0_1 prem_tot_1_1 ///
	prem_tot_2_1 prem_tot_3_1) 
estimates table Prem0_marg Prem1_marg Prem2_marg Prem3_marg, se keep(prem_tot0_1 ///
	prem_tot_1_1 prem_tot_2_1 prem_tot_3_1)
estimates table Prem0_marg Prem1_marg Prem2_marg Prem3_marg, star keep(prem_tot0_1 ///
	prem_tot_1_1 prem_tot_2_1 prem_tot_3_1)

xtdescribe if prem_tot0_1 != . 
forvalues x=1/3 {
xtdescribe if prem_tot_`x'_1 != .  
}

* LPM
global mal0 prem_tot0_1 
global mal1 prem_tot_1_1 
global mal2 prem_tot_2_1
global mal3 prem_tot_3_1 
qui: xtreg ${y} ${mal0} ${x_fe} ${h1} ${s} ${opp} ${time} , fe vce(cl id)
estimates store lpm0
qui: xtreg ${y} ${mal1} ${x_fe} ${h1} ${s} ${opp} ${time} , fe vce(cl id)
estimates store lpm1
qui: xtreg ${y} ${mal2} ${x_fe} ${h1} ${s} ${opp} ${time} , fe vce(cl id)
estimates store lpm2
qui: xtreg ${y} ${mal3} ${x_fe} ${h1} ${s} ${opp} ${time} , fe vce(cl id)
estimates store lpm3
estimates table lpm0 lpm1 lpm2 lpm3, star keep(${mal0} ${mal1} ${mal2} ${mal3})
***** NOTE: 

***** Using Zscores instead 
foreach var in z_prem_tot0 {
by id: gen `var'_1=l.`var'
la var `var'_1 "`var'_{t-1}"
}

global zscores z_prem_tot0_1
* Z Score for Original Premium Construction 
qui: probit ${y} ${zscores} ${x} ${h1} ${s} ${opp} ${time} ${xbar}, vce(cl id)
estimates store Z_prem
qui: dprobit ${y} ${zscores} ${x} ${h1} ${s} ${opp} ${time} ${xbar}, vce(cl id)
estimates store Z_marg

estimates table Prem0 Z_prem, se keep(prem_tot0_1 z_prem_tot0_1)
estimates table Prem0 Z_prem, star keep(prem_tot0_1 z_prem_tot0_1)
estimates table Prem0_marg Z_marg, se keep(prem_tot0_1 z_prem_tot0_1)
estimates table Prem0_marg Z_marg, star keep(prem_tot0_1 z_prem_tot0_1)
	
	
qui: xtreg ${y} ${mal0} ${x_fe} ${h1} ${s} ${opp} ${time} , fe vce(cl id)
estimates store lpm0
qui: xtreg ${y} ${zscores} ${x_fe} ${h1} ${s} ${opp} ${time} , fe vce(cl id)
estimates store lpm0z

estimates table lpm0 lpm0z, se keep(prem_tot0_1 z_prem_tot0_1)
estimates table lpm0 lpm0z, star keep(prem_tot0_1 z_prem_tot0_1)

*More than 2 standard deviations above the mean z > 2.0 
gen prem_ge_mu_1 = 0
replace prem_ge_mu_1 = 1 if z_prem_tot0_1>=2.0
qui: probit ${y} prem_ge_mu_1 ${x} ${h1} ${s} ${opp} ${time} ${xbar}, vce(cl id)
estimates store Z_gemu
qui: xtreg ${y} prem_ge_mu_1 ${x_fe} ${h1} ${s} ${opp} ${time} , fe vce(cl id)
estimates store lpm_Z_gemu

estimates table Z_gemu lpm_Z_gemu, star keep (prem_ge_mu_1)


***** Instrument for Premiums using Surgery Premiums *****
preserve
egen prem = mean(prem_tot0), by(year)
egen prem_surg = mean(prem_tot_SURG), by(year)
la var prem "Internal Med Premiums (in thousands)"
la var prem_surg "Surgical Premiums (in thousands)"

twoway (scatter prem prem_surg year, graphregion(color(white) lwidth(large))) 
restore

* RUN IVREG2 and the following options which perform the following tests: 
* endog() where varlist in () is the suspected endogenous regressor(s)
	* null of endog is that regressors treated as endogenous are actually exogenous
	* if reject null then IV is preferred to OLS
* orthog() where varlist in () is instruments to test for validity
	* null of orthog is that suspected endog instrument is valid
	* if reject null then instrument validity is questionable and need new instrument
	
global prem prem_tot0_1
global z prem_tot_SURG_1

ivreg2 ${y} ${x_fe} ${h1} ${s} ${opp} ${time} (${prem}=${z}), cluster(id) endog(${prem})
* RUN IVREGRESS 2SLS
* option first will report first stage results
* Following post estimation commands which test:
* estat endogenous (same as option endog() with IVREG2)
* estat firststage, all gives summary F-stats (>10 is desirable threshold)
ivregress 2sls ${y} ${x_fe} ${h1} ${s} ${opp} ${time} (${prem}=${z}), ///
				vce(cluster id) first
estimates store IV2sls
estat endogenous
estat firststage, all
outreg2 [lpm0 IV2sls] using IV.tex, replace 

* Kitchen Sink on the Reform Vars * Use RELO_MAIN through line 153, then apply code below

global caps r_cn_1 r_cp_1 r_cnbar r_cpbar
qui: probit ${y} ${mal0} ${caps} ${x} ${h1} ${s} ${opp} ${time} ${xbar}, vce(cl id)
estimates store caps1
qui: dprobit ${y} ${mal0} ${caps} ${x} ${h1} ${s} ${opp} ${time} ${xbar}, vce(cl id)
estimates store caps_marg

estimates table REprob_1 caps1, star
