* Data Description for Physician Relocation

* If any changes made to dataset, make sure to first run datacleaning.do
* on Phy_Complete.dta

cd "/Users/aliceellyson/Desktop/Dissertation/Data/Chap2"
use Phy_Clean.dta

* Distribution of Sample over Alternatives
tab fipscode
latab fipscode, dec(2)
tab pe
latab pe, dec(2)
tab ob
latab ob, dec(2)
* Offering obstetrics
bysort year: tab ob
* By MSA
tabstat ob, by(msa) stat(mean n sd)
latabstat ob, by(msa) stat(mean n sd)

* Summary Statistics of Personal Characteristics and Choice Characteristics
sum
sutex

* Figures
****** BAR GRAPH: Prob(Relocating) by Reform Status
* Generate Variables to Graph the Probability of Relocating if Reforms are Active
* use clogit data - Phy_Clean.dta
egen prob1_r_cn=mean(locat_chang) if r_cn==1
egen prob0_r_cn=mean(locat_chang) if r_cn==0

egen prob1_r_cp=mean(locat_chang) if r_cp==1
egen prob0_r_cp=mean(locat_chang) if r_cp==0

egen prob1_r_cs=mean(locat_chang) if r_cs==1
egen prob0_r_cs=mean(locat_chang) if r_cs==0

egen prob1_r_js=mean(locat_chang) if r_js==1
egen prob0_r_js=mean(locat_chang) if r_js==0
la var prob1_r_cn "CNED Active"
la var prob1_r_cp "CPD Active"
la var prob1_r_cs "CSR Active"
la var prob1_r_js "JSLR Active"
la var prob0_r_cn "CNED Inactive"
la var prob0_r_cp "CPD Inactive"
la var prob0_r_cs "CSR Inactive"
la var prob0_r_js "JSLR Inactive"

graph bar prob1_r_cn - prob0_r_js
drop prob1_r_cn - prob0_r_js

**********************************************************************************

* Graphics Chapter 2
* Use state level dataset (NEWESTDATA.dta)
replace r_pp=1 if r_pp==2
replace pop=pop/10000

egen num_act_ref=rowtotal(r_cn r_cp r_cs r_js r_pp r_pe)
la var num_act_ref "Total Number of Active Reforms"
* Generate averages
egen avg_fm=mean(fm_phy), by(fipscode)
egen avg_m_pay=mean(m_pay), by(fipscode)
egen avg_pop=mean(pop), by(fipscode)
* Bar Chart
graph bar fm_phy, over(num_act_ref)  bargap(1) yti("Family Medicine Physicians")
* Quartiles for m_pay
sum m_pay, detail
gen fm_pc=fm_phy/pop
gen malq=1 if m_pay<62
replace malq=2 if m_pay>=62 & m_pay<134
replace malq=3 if m_pay>=134 & m_pay<264
replace malq=4 if m_pay>=264
la def qrt 1 "1st Quartile" 2 "2nd Quartile" 3 "3rd Quartile" 4 "4th Quartile"
la val malq qrt
graph bar fm_pc, over(malq) bargap(1) yti("Family Medicine Physicians per capita") blab(bar, position(inside) color(white))
* These graphics don't show much of a relationship at all
* Both are per capita figures
label var avg_fm "Family Medicine Physicians per capita"
label var avg_m_pay "Malpractice Payments per capita"
graph twoway scatter avg_fm avg_m_pay
graph twoway scatter avg_fm avg_m_pay if avg_m_pay<1400
egen avg_ref=mean(num_act_ref), by(fipscode)
graph twoway scatter avg_fm avg_ref

* Use physician level data (Phy_Clean.dta)

forvalues x=1992/2007 {
display `x'
tab mode if year==`x'
}

* Use Graphics.dta

twoway scatter solo partner group hospital gov other year, ///
ytitle("Percent Distribution of Physicians by Practice Mode") ///
note("Other includes medical students, and physicians in non-patient care positions, and those that did not provide a classification")


* Figure 2
cd "/Users/aliceellyson/Desktop/Dissertation/Data/Chap2"
use prem_final.dta
merge 1:1 fips_state year using STATE_DATA.dta, keepus(fm_phy pop)
drop if _merge==2

* Quartiles for m_pay
sum prem_tot, det
replace pop=pop/10000
gen fm_pc=fm_phy/pop
gen premq=1 if prem_tot < 5170
replace premq=2 if prem_tot >= 5170 & prem_tot < 7629
replace premq=3 if prem_tot >= 7629 & prem_tot < 12163
replace premq=4 if prem_tot >= 12163
la def qrt 1 "1st Quartile" 2 "2nd Quartile" 3 "3rd Quartile" 4 "4th Quartile"
la val premq qrt
graph bar fm_pc, over(premq) bargap(1) yti("Family Medicine Physicians per capita") blab(bar, position(inside) color(white))

