***** FAMILY MEDICINE PHYSICIAN RELOCATION *****

***** PRELIMINARIES *****
cd "/Users/aliceellyson/Desktop/Dissertation/Data/Chap2"
set mem 8g
use Phy_Clean.dta
xtset id year

xtdescribe
sum id-exper
***** ADD/EDIT/MANAGE VARIABLES *****
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
* Scale Premiums
replace prem_tot = prem_tot/1000
la var prem_tot "Total Internal Med Premiums (in thousands)"
la var hmo_scl "HMO Enrollment (in millions)"
* Lagged variables for all vars included in the model
foreach var in prem_tot age exper fmres_prog total_phy hmo_scl lpop inc_pc civ_unemp ///
urbanpct recentmove sameloc r_cn r_cp r_pcf {
by id: gen `var'_1=l.`var'
la var `var'_1 "`var'_{t-1}"
}
 ** Time Demeaned vars for RE
foreach var in prem_tot age exper fmres_prog total_phy hmo_scl lpop inc_pc civ_unemp ///
urbanpct recentmove sameloc r_cn r_cp r_pcf {
egen `var'bar=mean(`var'), by(id)
}
* Generate Absolute Change in premiums
forvalues y=2/4 {
forvalues x=1/3 {
by id: gen prem_chg`y'_`x' = l`x'.prem_tot - l`y'.prem_tot
la var prem_chg`y'_`x' "Premium Change t-`y' to t-`x'"
}
}
drop prem_chg3_1 prem_chg4_1 prem_chg2_2 prem_chg4_2 prem_chg2_3 prem_chg3_3

* Generate percentage change in premiums
forvalues y=2/4 {
forvalues x=1/3 {
by id: gen prem_grw`y'_`x' = (prem_tot[_n-`x']-prem_tot[_n-`y']) / prem_tot[_n-`y']
la var prem_grw`y'_`x' "Premium Percentage Change t-`y' to t-`x'"
}
}
drop prem_grw3_1 prem_grw4_1 prem_grw2_2 prem_grw4_2 prem_grw2_3 prem_grw3_3

* Indicators for Premium Increases
* More than 25 percent
gen prem_g25 = 1 if prem_grw2_1 > 0.25
replace prem_g25 = 0 if prem_grw2_1 <= 0.25
la var prem_g25 "Indicator for Premium Growth t-2 to t-1 greater than 25%"
* More than 15 percent
gen prem_g15 = 1 if prem_grw2_1 > 0.15
replace prem_g15 = 0 if prem_grw2_1 <= 0.15
la var prem_g15 "Indicator for Premium Growth t-2 to t-1 greater than 15%"
* More than 35 percent
gen prem_g35 = 1 if prem_grw2_1 > 0.35
replace prem_g35 = 0 if prem_grw2_1 <= 0.35
la var prem_g35 "Indicator for Premium Growth t-2 to t-1 greater than 35%"
* More than 50 percent
gen prem_g50 = 1 if prem_grw2_1 > 0.5
replace prem_g50 = 0 if prem_grw2_1 <= 0.5
la var prem_g50 "Indicator for Premium Growth t-2 to t-1 greater than 50%"
* More than 75 percent
gen prem_g75 = 1 if prem_grw2_1 > 0.75
replace prem_g75 = 0 if prem_grw2_1 <= 0.75
la var prem_g75 "Indicator for Premium Growth t-2 to t-1 greater than 75%"
* More than 100 percent
gen prem_g100 = 1 if prem_grw2_1 > 1
replace prem_g100 = 0 if prem_grw2_1 <= 1
la var prem_g100 "Indicator for Premium Growth t-2 to t-1 greater than 100%"
***** GLOBALS *****
* Globals - DEP VARS
global y relo
* Globals - INDEP VARS
global time d1994-d2007
global mal0 prem_tot_1
global mal25 prem_g25
global mal35 prem_g35
global mal15 prem_g15
global mal50 prem_g50
global mal75 prem_g75
global mal100 prem_g100
global mal1 prem_chg2_1
global mal2 prem_chg2_1 prem_chg3_2
global mal3 prem_chg2_1 prem_chg3_2 prem_chg4_3
global mal4 prem_grw2_1
global mal5 prem_grw2_1 prem_grw3_2
global mal6 prem_grw2_1 prem_grw3_2 prem_grw4_3
global x female age_1 exper_1 do
global x_fe age_1 exper_1
global h1 fmres_prog_1 total_phy_1 hmo_scl_1
global s lpop_1 inc_pc_1 civ_unemp_1 urbanpct_1
global opp recentmove_1 sameloc_1
global ref r_cn_1 r_cp_1

global xbar prem_totbar agebar experbar fmres_progbar total_phybar ///
hmo_sclbar lpopbar inc_pcbar civ_unempbar urbanpctbar recentmovebar samelocbar

***** REGRESSIONS *****

***** 1 *****
* Does state-by-state variation in premium affect the likelihood a physician would move?

* Base specification - Full Panel with last years premium (mal0) on RHS
* LPM
qui: xtreg ${y} ${mal0} ${x_fe} ${h1} ${s} ${opp} ${time} , fe vce(cl id)
estimates store LPM_FE_1
* Probit
*qui: xtprobit ${y} ${mal0} ${x} ${h1} ${s} ${opp} ${time} , re 
*estimates store Prob_1
*qui: margins, dydx(*) atmeans
*estimates store Prob_1_marg
*qui: dprobit ${y} ${mal0} ${x} ${h1} ${s} ${opp} ${time} , vce(cl id)
*estimates store Prob_1_marg
* Conditional Logit
* Should be the same as xtlogit vars, fe
qui: clogit ${y} ${mal0} ${x_fe} ${h1} ${s} ${opp} ${time} , group(id) 
estimates store CLOG1
* Random Effects Probit
* Chamberlain's RE: Possibility 4 Page 73 Notes Pooled MLE Estimation
qui: probit ${y} ${mal0} ${x} ${h1} ${s} ${opp} ${time} ${xbar}, vce(cl id)
estimates store REprob_1
qui: dprobit ${y} ${mal0} ${x} ${h1} ${s} ${opp} ${time} ${xbar}, vce(cl id)
estimates store REprob_1_marg

* Table
cd "/Users/aliceellyson/Desktop/Dissertation/Data/Chap2/Results_Tables"
outreg2 [REprob_1 REprob_1_marg LPM_FE_1 CLOG1] ///
using initial.tex, replace keep(${mal0} ${x} ${x_fe} ${h1} ${s} ${opp})

* Test that pooled and full MLE result in the same conclusion
* Chamberlain's RE: Possibility 3 Page 73 Notes
* Full MLE Note: This one takes a while
xtprobit ${y} ${mal0} ${x} ${h1} ${s} ${opp} ${time} ${xbar}, re
estimates store PROBIT_RE_FULL
margins, dydx(*) atmeans
estimates store PROBIT_RE_FULL_MARG
*
predict xdhat, xb
gen xdhata = xdhat / sqrt(1 + .00067^2)
display (1 / sqrt(1 + .00067^2))
display (1 / sqrt(1 + .00067^2))*_b[prem_tot_1]
display (1 / sqrt(1 + .00067^2))*_b[recentmove_1]
display (1 / sqrt(1 + .00067^2))*_b[lpop_1]

* NOTE: the last display should give you a similar coefficient from pooled
* Also note, APEs should be similar
drop _est_PROB-scale xdhat xdhata

***** 2 *****
* Subsample analysis: < 10 years exper, solo and partner, group and hospital
* Less experienced physicians may consider this more before they settle
* Physicians working in Solo and Partner compared to Hospital and Group practice 

* 1 - Full Panel 
* estimates REprob_1_marg
* 2 - Less than 10 years experience 
probit ${y} ${mal0} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if exper<10, vce(cl id)
dprobit ${y} ${mal0} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if exper<10, vce(cl id)
estimates store REprob_exper
* 3a - Solo and Partner Physicians
probit ${y} ${mal0} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if mode==0 | mode==1, vce(cl id)
dprobit ${y} ${mal0} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if mode==0 | mode==1, vce(cl id)
estimates store REprob_solo
* 3b - Group and Hospital Physicians
probit ${y} ${mal0} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if mode==2 | mode==3, vce(cl id)
dprobit ${y} ${mal0} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if mode==2 | mode==3, vce(cl id)
estimates store REprob_group
* 4 - OB: Subsample by physicians with OB board certification
probit ${y} ${mal0} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if ob==1, vce(cl id)
dprobit ${y} ${mal0} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if ob==1, vce(cl id)
estimates store REprob_OB
* Table 
outreg2 [REprob_1_marg REprob_exper REprob_solo REprob_group] ///
using subsample.tex, replace keep(${mal0} ${x} ${h1} ${s} ${opp})

***** 3 *****
* Do large changes in malpractice premiums have a differential effect on relocation?
* Given a large increase in malpractice pressure, do physicians migrate out of that state
* First move only - censor more moves - absolute change (all three lags)
by id: gen z = sum(relo)
by id: gen z1=z[_n-1]
drop z
* Exclude all observations where z1>=1 
* Sensitivity - (Base) 25% (Lower) 15% and (Higher) 35%
* All Random Effects
* 15
qui: probit ${y} ${mal15} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if z1 < 1, vce(cl id)
estimates store GR_15
qui: dprobit ${y} ${mal15} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if z1 < 1, vce(cl id)
estimates store GR_15marg
* 25
qui: probit ${y} ${mal25} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if z1 < 1, vce(cl id)
estimates store GR_25
qui: dprobit ${y} ${mal25} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if z1 < 1, vce(cl id)
estimates store GR_25marg
* 35
qui: probit ${y} ${mal35} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if z1 < 1, vce(cl id)
estimates store GR_35
qui: dprobit ${y} ${mal35} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if z1 < 1, vce(cl id)
estimates store GR_35marg
* 50
qui: probit ${y} ${mal50} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if z1 < 1, vce(cl id)
estimates store GR_50
qui: dprobit ${y} ${mal50} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if z1 < 1, vce(cl id)
estimates store GR_50marg
* 75
qui: probit ${y} ${mal75} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if z1 < 1, vce(cl id)
estimates store GR_75
qui: dprobit ${y} ${mal75} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if z1 < 1, vce(cl id)
estimates store GR_75marg
* 100
qui: probit ${y} ${mal100} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if z1 < 1, vce(cl id)
estimates store GR_100
qui: dprobit ${y} ${mal100} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if z1 < 1, vce(cl id)
estimates store GR_100marg
* 1 - Baseline 25, 2 - 15, 3 - 35
outreg2 [GR_25marg GR_50marg GR_75marg GR_100marg] ///
using FM1.tex, replace keep(${mal25} ${mal50} ${mal75} ${mal100}  ${x} ${x_fe} ${h1} ${s} ${opp})

***** OTHER *****

***** 3 *****
* What about reforms?
global ref r_cn_1 
* 1 - Full Panel 
probit ${y} ${mal0} ${ref} ${x} ${h1} ${s} ${opp} ${time} ${xbar}, vce(cl id)
dprobit ${y} ${mal0} ${ref} ${x} ${h1} ${s} ${opp} ${time} ${xbar}, vce(cl id)
estimates store REprob
* 2 - Less than 10 years experience 
probit ${y} ${mal0} ${ref} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if exper<10, vce(cl id)
dprobit ${y} ${mal0} ${ref} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if exper<10, vce(cl id)
estimates store REprob_exper
* 3a - Solo and Partner Physicians
probit ${y} ${mal0} ${ref} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if mode==0 | mode==1, vce(cl id)
dprobit ${y} ${mal0} ${ref} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if mode==0 | mode==1, vce(cl id)
estimates store REprob_solo
* 3b - Group and Hospital Physicians
probit ${y} ${mal0} ${ref} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if mode==2 | mode==3, vce(cl id)
dprobit ${y} ${mal0} ${ref} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if mode==2 | mode==3, vce(cl id)
estimates store REprob_group
* 4 - OB: Subsample by physicians with OB board certification
probit ${y} ${mal0} ${ref} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if ob==1, vce(cl id)
dprobit ${y} ${mal0} ${ref} ${x} ${h1} ${s} ${opp} ${time} ${xbar} if ob==1, vce(cl id)
estimates store REprob_OB
* Table 
outreg2 [REprob REprob_exper REprob_solo REprob_group] ///
using reforms.tex, replace keep(${mal0} ${ref} ${x} ${h1} ${s} ${opp})

* Specification Check Plot!
coefplot RES_PROBRE_ME PROBIT_RE_MARG, keep(mid_cn_1 mid_cp_1 high_1) ///
 xtitle() xline(0) 
 
 ****** Other just checks *****
* Test for strict exogeneity using LPM
* Generate leads, if leads are significant, strict exog likely violated
gen ldprem_tot=f.prem_tot
la var ldprem_tot "prem_tot_{t+1}"
global lead ldprem_tot
xtreg ${y} ${lead} ${mal} ${x_fe} ${h1} ${s} ${opp} ${time}, fe vce(cl id)
drop ldprem_tot
* Not significant!! :) WOOOO

***** Migration Patterns *****
* See migration.do

***** Migration Patterns *****

******************** MAY NEED FOR RE PROBIT **************************************** 
 * Compare (1) - (4) 
* est tab LPM_FE PROBIT PROBIT_APE PROBIT_RE PROBIT_RE_MARG CLOGIT_FE, ///
* b(%9.3f) se(%9.3f) p stats (N ll chi2)  
outreg2 [LPM_FE PROBIT PROBIT_APE PROBIT_RE PROBIT_RE_MARG CLOGIT_FE] ///
using BinResponse.tex, stats(coef se N) par(se) bd(4) sd(4) replace ///
keep (${cat} ${x1} ${x} ${s} ${oppcost} ${xbar}) tex
*twoway scatter Yhat_LPM ${y} m_pay, connect(l .) symbol(i O) sort ylabel(0 1) 
*twoway scatter Yhat_Logit ${y} m_pay, connect(l i) msymbol(i O) sort ylabel(0 1)




