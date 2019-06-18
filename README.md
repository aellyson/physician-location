# physician-location
Can Malpractice Pressure Compel a Physician to Relocate? Ellyson and Robertson (2017)

Existing literature considers the effect of changes in malpractice pressure by focusing on physician supply, and concludes that changes in tort laws have limited but some impact on physician movement. Using a panel dataset which follows a random sample of 28,227 family medicine physicians in the United States from 1992–2007, this paper evaluates whether changes in malpractice premiums impact a physician's decision to relocate their practice. Our findings suggest that even large premium growth has no impact on the physician relocation decision. Generally, these results suggest that family medicine physicians do not use relocation as a strategy to avoid malpractice pressure. However, some physicians are more inclined to relocate than others. Results indicate that group and hospital practice physicians are more likely to move to another state when premiums are high compared to solo and partnership practice physicians.

List of Data Sets

-fips_state.dta			Specifies state, postal code, and fipscode

-Phy_Clean.dta		Family Medicine Physician data set cleaned including state vars

-Phy_Complete.dta		Combined Physician data set, not cleaned

-STATE_DATA.dta		Most up to date state characteristic data

-prem_final.dta		Malpractice premium data from Medical Liability Monitor

Do Files

-data_describe		Code for data description and state level graphics

-data_cleaning		Code to clean Phy_Complete and save as Phy_Clean

-dynamicprobit		Code that implements Dynamic Probit (Wooldridge)

-expand_loc			Code to expand dataset, 51 obsv for each id year combo

-migration			Code that generates migration patterns for four categories of malpractice pressure

-nested			Code to set up nested legit

-Relo_extras			Code removed from RELO_MAIN but may need later

-RELO_MAIN			MAIN CODE FOR PHYSICIAN RELOCATION PAPER

-STATE_Edits			Code that edits STATE_DATA.dta as needed and replaces 

-Test_bordermoves		Count physicians moving to border states
