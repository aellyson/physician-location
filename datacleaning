***** Data Cleaning: Physician-Level Data *****
cd "/Users/aliceellyson/Desktop/Dissertation/Data/Chap2"
use Phy_Complete.dta

* Drop variables (dead=0 for all obs)
drop _merge dead undel nocontact

* Change variable names *
rename researchid id
rename sex female
rename ama_stcode postal
rename med_trfrom train_date
rename stschgrad medschool
rename hospitalid hospital
rename hospitalhours hours
rename praflag pra
rename ustrained foreign
rename residencytrainingstate ST_restrain
rename medicalschoolstate ST_medschool

* Sort Data before merges
xtset id year
* Merge with state fipscode
merge m:1 postal using fips_state
* Those not matching are territories outside US (Puerto Rico=PR,
* Guam=GU, and Virgin Islands=VI) Drop these (_merge==1)
drop if _merge==1
drop _merge

* Label variables *
la var id "Physician ID"
la var mpa "Major Professional Activity"
la var do "=1 if D.O."
la var female "=1 if female"
la var region "Census Region"
la var division "Census Division"
la var addrtype "Address Type"
la var postal "State Abbreviation"
la var zip "Zip Code"
la var fips_count "Fips Code County"
la var fips_city "Fips Code City"
la var county "County"
la var b_date "Birth Date"
la var b_place "Birth Place City, State"
la var ecfmg "ECFMG Certification"
la var lic_yr "Year Physician was licensed in state of Current Practice"
la var top "Primary Type of Practice"
la var pe "Type of Employment"
la var spec1 "Primary Specialty"
la var spec2 "Secondary Specialty"
la var train_date "Date of beginning of this segment of graduate training"
la var medtrinst "Institution Code of of graduate training"
la var medschool "Med School Institution Code"
la var grad_yr "Year of Med School Graduation"
la var year "Year"
la var hospital "Hospital code where physician practices"
la var hours "Percent of hours physician practices at hospital"
la var pra "=1 if received Physician Recognition Award Certificate"
la var foreign "=1 if trained outside US"
la var ST_restrain "state of residency training"
la var ST_medschool "state of medical school"
la var state "State"
la var fipscode "Fips Code State"

* Label values *
la de reglab 0 "Outside US" 1 "Northeast" 2 "Midwest" 3 "South" 4 "West"
la val region reglab
la de divlab 0 "Outside US" 1 "New England" 2 "Mid Atlantic" 3 "East_Nort Cent" ///
4 "West_Nort Cent" 5 "South Atlantic" 6 "East_sout Cent" 7 "West_sout Cent" ///
8 "Mountain" 9"Pacific"
la val division divlab 

replace addrtype=2 if addrtype==10
replace addrtype=3 if addrtype==11
la de address 0 "Unknown" 1 "Professional" 2 "Home" 3"Both"
la val addrtype address
*Unknown
replace top=0 if top==11
replace top=0 if top==100
*Direct Paitent Care (residents, patient care)
replace top=1 if top==12
replace top=1 if top==20
*Teaching and Research
replace top=2 if top==40
replace top=2 if top==50
*Administrative and Non-Patient Care
replace top=3 if top==30
replace top=3 if top==62
replace top=3 if top==14
replace top=3 if top==15

la de toplab 0 "No Classification" 1 "Direct Patient Care" ///
2 "Teaching and Research" 3 "Other"
la val top toplab

* Present Employment: See Dictionary PRESEMP.txt
gen mode=.
*Solo
replace mode=0 if pe==11
*Partner
foreach num of numlist 13 14 {
replace mode=1 if pe==`num'
}
*Group
foreach num of numlist 30 35 {
replace mode=2 if pe==`num'
}
*Hospital
replace mode=3 if pe==50
*Government (loc and fed)
foreach num of numlist 60 63 64 80 81 82 83 84 85 86 90 91 92 93 94 95 96 {
replace mode=4 if pe==`num'
} 
*Other
* 21,22 - other patientcare (40 - Medical school, 101 - other non-patient care
* 									110 - no classification)
foreach num of numlist 21 22 40 101 110 {
replace mode=5 if pe==`num'
}

la de modelab 0 "Solo" 1 "Partnership" 2 "Group" 3 "Hospital" ///
4 "Government" 5 "Other"
la val mode modelab
la var mode "Practice Mode"

* Alternative
la de fips_st 1 "AL" 2 "AK" 4 "AZ" 5 "AR" 6 "CA" 8 "CO" 9 "CT" 10 "DE" ///
11 "DC" 12 "FL" 13 "GA" 15 "HI" 16 "ID" 17 "IL" 18 "IN" 19 "IA" 20 "KS" ///
21 "KY" 22 "LA" 23 "ME" 24 "MD" 25 "MA" 26 "MI" 27 "MN" 28 "MS" 29 "MO" ///
30 "MT" 31 "NE" 32 "NV" 33 "NH" 34 "NJ" 35 "NM" 36 "NY" 37 "NC" 38 "ND" ///
39 "OH" 40 "OK" 41 "OR" 42 "PA" 44 "RI" 45 "SC" 46 "SD" 47 "TN" 48 "TX" ///
49 "UT" 50 "VT" 51 "VA" 53 "WA" 54 "WV" 55 "WI" 56 "WY" 
la val fipscode fips_st
rename fipscode fips_state

* MAJOR PROFESSIONAL ACTIVITY
rename mpa activity
gen mpa=.
* Resident
replace mpa=0 if activity=="HPR"
* Full-time Clinical/Patient Care
foreach code in HPP OFF {
replace mpa=1 if activity=="`code'"
} 
* Research
replace mpa=2 if activity=="RES"
* Teaching
replace mpa=3 if activity=="MTC"
* Locum Tenens
replace mpa=4 if activity=="LOC"
* Administrative
replace mpa=5 if activity=="ADM"
* Other
foreach code in OTH NCL UNA {
replace mpa=6 if activity=="`code'"
} 
la var mpa "Major Professional Activity"
la de mpalabel 0 "Resident" 1 "Patient Care" 2 "Research" 3 "Teaching" 4 "Locum Tenens" 5 "Administrative" 6 "Other"
la val mpa mpalabel
drop activity
* Convert birthdate variable to read as date in Stata
nsplit b_date, digits(2 2 4) gen(b_month b_day b_year)
drop if b_year==1900
* THese physicians would already be over 90 in the first year of practice

* Fix invalid values of lic_yr and grad_yr
replace lic_yr=. if lic_yr>2014
replace lic_yr=. if lic_yr==19
replace lic_yr=. if lic_yr==1900
replace lic_yr=. if lic_yr==1901
replace lic_yr=. if lic_yr==2008
replace grad_yr=. if grad_yr==1901

* Create Obstetrics dummy
gen ob=1 if spec2=="OBG"
replace ob=1 if spec2=="OBS"
replace ob=1 if spec2=="OCC"
replace ob=0 if ob==.
label var ob "Secondary specialty in Obstetrics"
la de oblabel 0 "NO OB" 1 "OB Specialty"
la val ob oblabel

* Fix ST_restrain
encode ST_restrain, gen(state_res1) label(fips_st)
egen state_res2=max(state_res1), by(id)
la val state_res2 fips_st
rename state_res2 st_res
la var st_res "State of Residency Training"
drop ST_restrain state_res1 postal 

* Generate new id that doesn't have gaps
rename id oldid
egen newid = group(oldid)
rename newid id
drop oldid 

* Errors in age
replace b_year=1962 if id==17092
replace b_year=. if id==11925
replace b_year=. if id==24047

* Calculate age using birthdate
gen age=year-b_year
la var age "Age (in years)"
replace age=. if b_year==.

* State of Practice Same as State of Residency
gen sameloc=1 if st_res==fips_state
replace sameloc=0 if sameloc==.
replace sameloc=. if st_res==.
la var sameloc "=1 if same state as residency training"

* Years of Experience
gen exper=year-grad_yr
replace exper=. if grad_yr==.
la var exper "Years of Experience"

* Attended Top 20 Medical School
* gen top20=.
* foreach num of numlist  {
* replace top20=1 if medschool==`num'
* }

* Merge State Characteristics 
merge m:1 fips_state year using STATE_DATA.dta
drop _merge

merge m:1 fips_state year using prem_final.dta, keepus(prem_tot surchg prem med_prem_tot med_surchg med_prem)
drop _merge

* Edit state characteristics variables
gen lpop=ln(pop)
la var lpop "ln(population)"
gen hmo_pc=enrltot/pop
la var hmo_pc "HMO enrollment per capita"
replace inc_pc=inc_pc/1000
la var inc_pc "Income per capita (in thousands)"
la var r_pe "Punitive Evidence Reform"
*gen lossratio=prem/loss
*la var lossratio "Ratio Premiums to Losses"
*gen profit=(prem-loss)/1000000
*la var profit "Average Insurer Profitability (in millions of dollars)"
* Create additional var to account for offsetting effects of JSLR
*gen act_ref_JS=num_act_ref-r_js
*la var act_ref_JS "Number of Active Reforms discounted for JSL Reform"
* Labels
*la var elect "=1 if gubenatorial election in this state in this year"
*la de maj 0 "Majority Democrat" 1 "Majority Republican"
*la val rep_hr maj
*la var r_pe "Punitive Evidence Reform"
* DROP VARS NOT NEEDED - MAY NEED TO RERUN IF WE DO NEED THESE
drop fed_code b_date b_place ecfmg spec1 spec2 train_date foreign cmsa smsa ///
hospital ST_medschool caps_cn caps_cp lawyer elect tortcost l_lobby ///
i_lobby c_lobby rep_gov rep_sen r_sr top medschool medtrinst cnnochg-cp_treat ///
low mid_cn mid_cp loss prem pra postal

* Sort data
global person id year state fips_state do female age
global prof exper mode mpa ob lic_yr pe
global educ sameloc grad_yr st_res
global health fm_phy-fmresidents enrltot hmo_pc
global st_char pop lpop inc_pc civ_unemp rep_hr urbanpct
global mal prem_tot surchg med_prem_tot med_surchg med_prem m_pay
global ref num_act_ref diff_clim r_cn-r_pcf  high 
global locat addrtype zip fips_count county fips_city region division pmsa_msa msa
global extra b_month b_day b_year 

order ${person} ${prof} ${educ} ${mal} ${ref} ${health} ${st_char} ${locat} ${extra}
xtset id year

* Exclusionary Criteria
* Remove physicians not primarily engaged in Patient Care
* Administrative or Other
drop if mpa==5
drop if mpa==6

save Phy_Clean, replace

*MSA
*rename msa msa0
*gen msa=0 if msa0=="A"
*replace msa=1 if msa0=="B"
*replace msa=2 if msa0=="C"
*replace msa=3 if msa0=="D"
*la var msa "MSA (population in thousands)"
*la de msalabel 0 "1,000 or more" 1 "250 to 999" 2 "100 to 249" 3 "Less than 100"
*la val msa msalabel
*drop msa0
