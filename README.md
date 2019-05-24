# physician-location
Can Malpractice Pressure Compel a Physician to Relocate? 

Data Sets
fips_state.dta			Specifies state, postal code, and fipscode
Phy_Clean.dta		Family Medicine Physician data set cleaned including state vars
Phy_Complete.dta		Combined Physician data set, not cleaned
STATE_DATA.dta		Most up to date state characteristic data
prem_final.dta		Malpractice premium data from Medical Liability Monitor

Do Files
data_describe		Code for data description and state level graphics
data_cleaning		Code to clean Phy_Complete and save as Phy_Clean
dynamicprobit		Code that implements Dynamic Probit (Wooldridge)
expand_loc			Code to expand dataset, 51 obsv for each id year combo
migration			Code that generates migration patterns for four categories of malpractice pressure
nested			Code to set up nested legit
Relo_extras			Code removed from RELO_MAIN but may need later
RELO_MAIN			MAIN CODE FOR PHYSICIAN RELOCATION PAPER
STATE_Edits			Code that edits STATE_DATA.dta as needed and replaces 
Test_bordermoves		Count physicians moving to border states
