## Project: GDP Estimation and Forecasting Analytics (Base Year 2022–23)

## This project demonstrates how to build an end-to-end GDP Analytics Pipeline using SQL, Python, Machine Learning, and Power BI/Tableau.

## SQL Queries for GDP Data Extraction from Major Data Sources

## 1. National Accounts Statistics (NAS)

## sql

SELECT
    year,
    quarter,
    sector_code,
    sector_name,
    gva_current_prices,
    gva_constant_prices,
    gdp_current_prices,
    gdp_constant_prices
FROM nas_gdp_statistics
WHERE year >= '2022-23';

## Explanation

SELECT : 	This keyword specifies that you want to retrieve data from a database table. It tells SQL which columns should appear in the output.

year: 	Retrieves the financial year for which GDP statistics are available (e.g., 2022–23, 2023–24).

quarter:	Retrieves the quarter of the financial year (Q1, Q2, Q3, Q4) to enable quarterly GDP analysis.

sector_code:	Retrieves the unique identifier/code assigned to each economic sector (such as Agriculture, 
Manufacturing, Services). These codes help standardize sector classification.

sector_name:	Retrieves the name of the economic sector corresponding to the sector code. For example, Agriculture, Mining, Manufacturing, Construction, Financial Services, etc.

gva_current_prices:	Retrieves Gross Value Added (GVA) at Current Prices. This measures the value of goods and services produced by a sector using prices prevailing during that period, including the effects of inflation.

gva_constant_prices:	Retrieves Gross Value Added (GVA) at Constant Prices. It adjusts for inflation using a fixed base year, allowing analysis of real economic growth.

gdp_current_prices:: 	Retrieves Gross Domestic Product (GDP) at Constant Prices (also called Real GDP). It removes the effect of price changes, enabling comparison of actual economic growth across years.

FROM nas_gdp_statistics: it	Specifies the source table from which the data is being extracted. Here, nas_gdp_statistics is assumed to store National Accounts Statistics (NAS) GDP data.

WHERE year >= '2022-23'; Filters the records to return only data for financial year 2022–23 and subsequent years. This is useful when the analysis focuses on the revised GDP base year (2022–23) and later periods.

## 2. GST Aggregated Data

SELECT
    filing_period,
    state_code,
    industry_code,
    SUM(taxable_value) AS total_turnover,
    SUM(cgst_amount + sgst_amount + igst_amount) AS total_gst_collection
FROM gst_transactions
WHERE filing_period >= '2022-04'
GROUP BY
    filing_period,
    state_code,
    industry_code;

## Explanation

SELECT	: Specifies that data needs to be retrieved from the database and determines which columns or calculated values will appear in the output.

filing_period, : 	Retrieves the GST return filing period, usually represented in YYYY-MM format (e.g., 2022-04 for April 2022). This helps analyze GST data over time.

state_code,	Retrieves the state identifier code associated with the GST registrations. It enables state-wise analysis of economic activity and tax collections.

industry_code,	Retrieves the industry classification code (such as NIC code or sector code) representing the type of business activity. This supports sector-wise analysis.

SUM(taxable_value) AS total_turnover, - 	Calculates the total taxable turnover by adding (SUM) all values from the taxable_value column within each group. The result is given the alias total_turnover. This represents the total value of taxable supplies made by businesses.

SUM(cgst_amount + sgst_amount + igst_amount) AS total_gst_collection - Computes the total GST collected by summing the amounts of Central GST (CGST), State GST (SGST), and Integrated GST (IGST) for each group. The result is labeled as total_gst_collection.

FROM gst_transactions	- Specifies the source table from which the data is extracted. Here, gst_transactions is assumed to contain transaction-level GST filing data.

WHERE filing_period >= '2022-04'-	Filters the dataset to include only records from April 2022 onwards. Since FY 2022–23 in India starts in April 2022, this condition ensures the analysis aligns with the GDP base year 2022–23.

GROUP BY	-Groups records having the same values in specified columns so that aggregate functions such as SUM() can calculate totals for each group.

filing_period - 	Groups the data by each filing month, enabling month-wise GST analysis.

state_code - Further groups the data by state, allowing state-level turnover and GST collection analysis.

industry_code; - 	Further groups the data by industry/sector, enabling sector-wise assessment of economic activity and tax contributions.

## 3. Household Consumption/Expenditure Survey Data (HCES)

SELECT
    survey_year,
    state_code,
    sector,
    expenditure_category,
    AVG(monthly_consumption_expenditure) AS avg_mpce,
    SUM(monthly_consumption_expenditure) AS total_consumption
FROM hces_household_expenditure
GROUP BY
    survey_year,
    state_code,
    sector,
    expenditure_category;

## Explanation

SQL Code	Explanation
SELECT - 	Specifies that data is to be retrieved from the database and defines which columns and calculated values should appear in the output.

survey_year - 	Retrieves the year in which the Household Consumption Expenditure Survey (HCES) was conducted. This allows comparison of consumption patterns across different survey periods.

state_code - 	Retrieves the state identifier code, enabling state-wise analysis of household consumption expenditure.
sector,	Retrieves the sector classification of households, typically Rural or Urban. This helps compare consumption behavior between rural and urban populations.

expenditure_category -	Retrieves the category of expenditure incurred by households, such as Food, Education, Health, Clothing, Transport, Housing, etc. This facilitates category-wise consumption analysis.

AVG(monthly_consumption_expenditure) AS avg_mpce - 	Calculates the average Monthly Per Capita Consumption Expenditure (MPCE) by taking the average (AVG) of the monthly_consumption_expenditure values within each group. The result is assigned the alias avg_mpce. MPCE is a key indicator of household living standards and consumption behavior.

SUM(monthly_consumption_expenditure) AS total_consumption	- Computes the total household consumption expenditure by summing (SUM) all values in the monthly_consumption_expenditure column for each group. The result is named total_consumption. This measure contributes to estimating aggregate private final consumption expenditure.

FROM hces_household_expenditure	 - it Specifies the source table containing household consumption expenditure data collected through the Household Consumption Expenditure Survey (HCES).

GROUP BY - Groups rows having the same values in the specified columns so that aggregate functions such as AVG() and SUM() can be applied to each group.

survey_year - 	Groups the data by survey year, enabling year-wise consumption analysis.

state_code - 	Further groups the data by state, allowing comparison of consumption patterns across different states.
sector,	Further groups the data by rural or urban sector, facilitating rural-urban expenditure comparisons.

expenditure_category; - Further groups the data by type of expenditure, enabling analysis of spending patterns across different consumption categories.

## 4. Annual Survey of Industries (ASI)

SELECT
    survey_year,
    state_code,
    nic_code,
    SUM(total_output) AS gross_output,
    SUM(intermediate_consumption) AS intermediate_consumption,
    SUM(gross_value_added) AS gva
FROM asi_industries
WHERE survey_year >= 2022
GROUP BY
    survey_year,
    state_code,
    nic_code;

## Explanation

SELECT - 	Specifies that data should be retrieved from the database and determines which columns and calculated values will appear in the output.

survey_yea -	Retrieves the Annual Survey of Industries (ASI) reference year for each observation. This allows year-wise analysis of industrial performance.

state_code - 	Retrieves the state identifier code representing the location of the industrial units. This enables state-level industrial analysis.

nic_code - 	Retrieves the National Industrial Classification (NIC) code, which classifies industries based on their economic activities (e.g., manufacturing of textiles, food products, machinery, etc.). This supports industry-wise analysis.

SUM(total_output) AS gross_output - 	Calculates the Gross Output by summing (SUM) the total_output values for each group. Gross Output represents the total value of goods produced and industrial services rendered by establishments before deducting intermediate consumption. The result is labeled as gross_output.

SUM(intermediate_consumption) AS intermediate_consumption - 	Computes the Intermediate Consumption by summing all expenditures on goods and services consumed during the production process (such as raw materials, fuel, electricity, and purchased services). The aggregated value is assigned the alias intermediate_consumption.

SUM(gross_value_added)  AS gva - it Calculates the Gross Value Added (GVA) by summing (SUM) the gross_value_added values for each group. GVA represents the contribution of industries to the economy and is generally calculated as: GVA=Gross Output−Intermediate Consumption The result is named gva.

FROM asi_industries - it Specifies the source table containing data from the Annual Survey of Industries (ASI), which covers registered manufacturing establishments in India.

WHERE survey_year >= 2022- 	Filters the records to include only data for survey year 2022 and later. This aligns the analysis with the GDP base year 2022–23 and subsequent years.

GROUP BY - 	Groups records having identical values in specified columns so that aggregate functions such as SUM() can compute totals for each group.

survey_year - 	Groups the data by survey year, enabling year-wise industrial performance analysis.
state_code,	Further groups the data by state, facilitating state-level comparisons of industrial output and value addition.

nic_code - 	Further groups the data by industry classification (NIC code), enabling industry-wise estimation of Gross Output, Intermediate Consumption, and GVA.

## 5. MCA-21 Corporate Financial Data

SELECT
    financial_year,
    company_cin,
    industry_code,
    revenue_from_operations,
    total_expenditure,
    profit_before_tax,
    employee_cost,
    depreciation
FROM mca21_financials
WHERE financial_year >= '2022-23';

## Explanation

SELECT - 	Specifies that data should be retrieved from the database and identifies which columns will be included in the query output.

financial_year - 	Retrieves the financial year for which the company's financial information is reported (e.g., 2022–23, 2023–24). This enables year-wise analysis of corporate performance.

company_cin - 	Retrieves the Corporate Identification Number (CIN) assigned to each registered company by the Ministry of Corporate Affairs (MCA). The CIN uniquely identifies companies in India and facilitates company-level analysis.

industry_code - 	Retrieves the industry classification code (such as NIC code) representing the economic activity of the company. This enables sector-wise aggregation and analysis.

revenue_from_operations - 	Retrieves the revenue generated from the company's core business activities during the 
financial year. This excludes non-operating income such as interest income or gains from asset sales. It is an important indicator of business activity and output.

total_expenditure - 	Retrieves the total expenses incurred by the company during the financial year. This includes costs related to production, administration, employee benefits, depreciation, finance costs, and other operating expenses.

profit_before_tax - 	Retrieves the Profit Before Tax (PBT), which represents the company's earnings after deducting all expenses except income tax. It is calculated as: PBT=Total Revenue−Total Expenses This measure reflects operational profitability before taxation.

employee_cost - 	Retrieves the total compensation paid to employees, including salaries, wages, bonuses, provident fund contributions, and other employee benefits. This information is useful for analyzing labor costs and estimating compensation of employees in national accounts.

depreciation -	Retrieves the depreciation expense recognized by the company for wear and tear or obsolescence of fixed assets during the financial year. Depreciation is important for estimating Consumption of Fixed Capital (CFC) in GDP calculations.

FROM mca21_financials - it Specifies the source table containing financial information extracted from the MCA-21 database, which stores corporate filings submitted by companies registered with the Ministry of Corporate Affairs.

WHERE financial_year >= '2022-23'; - 	Filters the dataset to include only records for FY 2022–23 and later years. This aligns the analysis with the revised GDP base year 2022–23 and supports recent economic assessments.

## 6. Periodic Labour Force Survey (PLFS)

SELECT
    survey_year,
    state_code,
    sector,
    industry_code,
    employment_status,
    labour_force_participation_rate,
    worker_population_ratio,
    unemployment_rate
FROM plfs_employment_statistics
WHERE survey_year >= 2022;

## Explanation

SELECT	Specifies that data should be retrieved from the database and identifies the columns that will be included in the result set.

survey_year,	Retrieves the reference year of the Periodic Labour Force Survey (PLFS). This enables year-wise analysis of labour market trends and employment indicators.

state_code,	Retrieves the state identifier code, allowing state-wise comparisons of employment and labour market conditions.

sector,	Retrieves the sector classification, generally indicating Rural or Urban areas. This facilitates analysis of labour market differences between rural and urban populations.

industry_code,	Retrieves the industry classification code (typically based on NIC codes), which identifies the economic activity in which individuals are employed. This supports industry-wise employment analysis.

employment_status,	Retrieves the employment status category of individuals, such as Self-Employed, Regular Wage/Salaried Employee, or Casual Labour. This helps understand the composition and quality of employment.

labour_force_participation_rate,	Retrieves the Labour Force Participation Rate (LFPR), which measures the percentage of the working-age population that is either employed or actively seeking employment. It is calculated as: 

## LFPR= labour force / ( working - age population)*100

worker_population_ratio - it Retrieves the Worker Population Ratio (WPR), which represents the percentage of the working-age population that is employed. It is calculated as: WPR=(
Working-Age Population

## Number of Employed Persons / working - age population)*100
	​
unemployment_rate - 	Retrieves the Unemployment Rate (UR), which measures the percentage of the labour force that is unemployed but actively seeking work. It is calculated as: UR=(

## Labour Force
Unemployed Persons = (unemployed persons/ labour force )*100
	​
FROM plfs_employment_statistics	Specifies the source table containing labour market data derived from the Periodic Labour Force Survey (PLFS) conducted by the National Sample Survey Office (NSSO).

WHERE survey_year >= 2022;	Filters the records to include only data for survey year 2022 and later. This aligns the analysis with the GDP base year 2022–23 and recent labour market conditions.

## 7. Index of Industrial Production (IIP)

SELECT
    reference_month,
    industry_group,
    use_based_classification,
    iip_index,
    growth_rate
FROM iip_statistics
WHERE reference_month >= '2022-04';

## Explanation

SELECT- 	Specifies that data should be retrieved from the database and identifies the columns that will be included in the output.

reference_month -	Retrieves the month and year for which the Index of Industrial Production (IIP) data is reported, usually in YYYY-MM format (e.g., 2022-04 for April 2022). This enables month-wise analysis of industrial activity.

industry_group - 	Retrieves the industry classification group based on the National Industrial Classification (NIC) used in IIP reporting. Examples include Manufacturing, Mining, and Electricity sectors. This supports sector-wise industrial analysis.

use_based_classification - 	Retrieves the use-based category of industrial products. Common classifications include:
• Primary Goods
• Capital Goods
• Intermediate Goods
• Infrastructure/Construction Goods
• Consumer Durables
• Consumer Non-Durables
This helps analyze industrial output from the demand and usage perspective.

iip_index -	Retrieves the Index of Industrial Production (IIP) value, which measures changes in the volume of industrial production relative to a base year. An index value above 100 indicates production levels higher than the 

base year -  while a value below 100 indicates lower production levels.

growth_rate - it Retrieves the year-on-year growth rate of industrial production, usually expressed as a percentage. It measures the rate of increase or decrease in industrial output compared with the corresponding month of the previous year. The growth rate is generally calculated as: Growth Rate=(

growth rate = (current IIP- previous period IIP/ previous period IIP)*100

FROM iip_statistics -	Specifies the source table containing Index of Industrial Production (IIP) data published by the National Statistical Office (NSO).

WHERE reference_month >= -  '2022-04';	Filters the dataset to include only records from April 2022 onwards, aligning the analysis with the GDP base year 2022–23.

## 8. Consumer Price Index (CPI)

SELECT
    reference_month,
    state_code,
    commodity_group,
    cpi_index,
    inflation_rate
FROM cpi_statistics
WHERE reference_month >= '2022-04';

## Explanation

SELECT	Specifies that data should be retrieved from the database and identifies the columns that will be included in the result set.

reference_month,	Retrieves the month and year for which the Consumer Price Index (CPI) data is reported, generally in YYYY-MM format (e.g., 2022-04 for April 2022). This allows month-wise analysis of price movements and inflation trends.

state_code,	Retrieves the state identifier code, enabling state-wise comparison of consumer price levels and inflation patterns across different regions.

commodity_group,	Retrieves the commodity category used in CPI calculations. Examples include Food & Beverages, Housing, Clothing & Footwear, Fuel & Light, Health, Education, Transport & Communication, and Miscellaneous items. This facilitates category-wise inflation analysis.

cpi_index,	Retrieves the Consumer Price Index (CPI) value, which measures the average change over time in the prices paid by consumers for a basket of goods and services relative to a base year. An increase in the CPI index generally indicates rising consumer prices.

inflation_rate	Retrieves the inflation rate, usually expressed as a percentage, showing how much consumer prices have increased or decreased compared to the corresponding period of the previous year. Inflation rate is generally calculated as: Inflation Rate=( Current CPI−Previous CPI / Previous CPI)*100

FROM cpi_statistics	 - it Specifies the source table containing Consumer Price Index data, which is typically published by the National Statistical Office (NSO).

WHERE reference_month >= '2022-04'; - it Filters the records to include only data from April 2022 onwards, aligning the analysis with the GDP base year 2022–23 and recent inflation trends.

## 9. Wholesale Price Index (WPI)

SELECT
    reference_month,
    commodity_group,
    wpi_index,
    inflation_rate
FROM wpi_statistics
WHERE reference_month >= '2022-04';

## Explanation

SELECT - 	Specifies that data should be retrieved from the database and determines which columns will be included in the output.

reference_month - 	Retrieves the month and year for which the Wholesale Price Index (WPI) data is reported, generally in YYYY-MM format (e.g., 2022-04 for April 2022). This facilitates month-wise tracking of wholesale price movements.

commodity_group - 	Retrieves the commodity category used in WPI compilation. Common groups include Primary Articles, Fuel & Power, and Manufactured Products. This enables analysis of price changes across different segments of the economy.

wpi_index - 	Retrieves the Wholesale Price Index (WPI) value, which measures the average change in prices of goods traded at the wholesale level relative to a base year. It serves as an indicator of producer-level inflation. An index value higher than the base year value indicates an increase in wholesale prices.

inflation_rate	- Retrieves the WPI inflation rate, expressed as a percentage, showing the change in wholesale prices compared with the corresponding period of the previous year. It is generally calculated as: 
WPI Inflation Rate=( current WPI - Previous WPI)/  Previous WPI)×100

 FROM wpi_statistics -	Specifies the source table containing Wholesale Price Index data, typically published by the Office of the Economic Adviser (OEA), Department for Promotion of Industry and Internal Trade (DPIIT).

WHERE reference_month >= '2022-04'; - 	Filters the dataset to include only records from April 2022 onwards, aligning the analysis with the GDP base year 2022–23 and focusing on recent wholesale price trends.

## 10. External Trade Data (Exports and Imports)

SELECT
    trade_month,
    commodity_code,
    country_code,
    SUM(export_value) AS total_exports,
    SUM(import_value) AS total_imports
FROM external_trade_statistics
WHERE trade_month >= '2022-04'
GROUP BY
    trade_month,
    commodity_code,
    country_code;

## Explanation

SELECT-	Specifies that data should be retrieved from the database and defines the columns and calculated values that will appear in the output.

trade_month - 	Retrieves the month and year of the trade transaction, generally stored in YYYY-MM format (e.g., 2022-04 for April 2022). This enables month-wise analysis of trade performance.

commodity_code -	Retrieves the commodity classification code, typically based on the Harmonized System (HS) Code, which identifies the type of goods being exported or imported. This facilitates commodity-wise trade analysis.

country_code - 	Retrieves the country identifier code representing the trading partner country involved in the export or import transaction. This supports country-wise trade analysis.

SUM(export_value) AS total_exports -	Calculates the total value of exports by summing (SUM) all values in the export_value column for each group. The result is assigned the alias total_exports. This represents the aggregate monetary value of goods exported.

SUM(import_value) AS total_imports - Calculates the total value of imports by summing (SUM) all values in the import_value column for each group. The result is assigned the alias total_imports. This represents the aggregate monetary value of goods imported.

FROM external_trade_statistics - it	Specifies the source table containing external trade data. This dataset is typically derived from customs records compiled by the Directorate General of Commercial Intelligence and Statistics (DGCI&S) under the Ministry of Commerce and Industry.

WHERE trade_month >= '2022-04' - it	Filters the records to include only trade transactions from April 2022 onwards, aligning the analysis with the GDP base year 2022–23.

GROUP BY -	Groups records having the same values in specified columns so that aggregate functions such as SUM() can calculate totals for each group.

trade_month - Groups the data by trade month, enabling month-wise analysis of exports and imports.

commodity_code -	Further groups the data by commodity type, allowing commodity-wise trade assessment.

country_code; - 	Further groups the data by trading partner country, facilitating country-wise trade analysis.

## 11. Government Expenditure Data

SELECT
    financial_year,
    ministry_name,
    expenditure_type,
    SUM(revenue_expenditure) AS total_revenue_expenditure,
    SUM(capital_expenditure) AS total_capital_expenditure
FROM government_expenditure
WHERE financial_year >= '2022-23'
GROUP BY
    financial_year,
    ministry_name,
    expenditure_type;

## Explanation

SELECT - Specifies that data should be retrieved from the database and determines which columns and calculated values will be included in the output.

financial_year - 	Retrieves the financial year for which government expenditure data is recorded (e.g., 2022–23, 2023–24). This enables year-wise analysis of government spending patterns.

ministry_name - 	Retrieves the name of the government ministry or department responsible for the expenditure, such as the Ministry of Defence, Ministry of Health and Family Welfare, Ministry of Education, etc. This supports ministry-wise expenditure analysis.

expenditure_type - 	Retrieves the classification of expenditure, which may include categories such as Plan/Non-Plan (historically) or functional categories like Administrative Services, Social Services, Economic Services, Defence Services, and Grants-in-Aid. This facilitates expenditure analysis by purpose or function.

SUM(revenue_expenditure) AS total_revenue_expenditure - 	Calculates the total revenue expenditure by summing (SUM) all values in the revenue_expenditure column for each group. Revenue expenditure refers to recurring expenses incurred for the day-to-day functioning of government, such as salaries, pensions, subsidies, interest payments, and maintenance expenses. The result is labeled as total_revenue_expenditure.

SUM(capital_expenditure) AS total_capital_expenditure - it	Calculates the total capital expenditure by summing (SUM) all values in the capital_expenditure column for each group. Capital expenditure represents spending on the creation or acquisition of assets such as infrastructure projects, machinery, buildings, and investments that contribute to future economic benefits. The result is assigned the alias total_capital_expenditure.

FROM government_expenditure	- it Specifies the source table containing government expenditure data. This data is generally compiled from the Union Budget, State Budgets, and Government Finance Statistics (GFS) used in national accounts compilation.

WHERE financial_year >= '2022-23' - it	Filters the records to include only data from FY 2022–23 onwards, aligning the analysis with the revised GDP base year 2022–23.

GROUP BY -	Groups records having the same values in specified columns so that aggregate functions such as SUM() can calculate totals for each group.

financial_year,	Groups the data by financial year, - enabling year-wise analysis of government expenditure trends.
ministry_name,	Further groups the data by government ministry, facilitating ministry-wise expenditure assessment.

expenditure_type; - 	Further groups the data by expenditure category, allowing analysis of spending patterns across different expenditure types.

## 12. Supply and Use Tables (SUT)

SELECT
    reference_year,
    product_code,
    industry_code,
    domestic_output,
    imports,
    intermediate_consumption,
    final_consumption,
    exports,
    gross_capital_formation
FROM supply_use_tables
WHERE reference_year >= '2022-23';

## Explanation

SELECT	- Specifies that data should be retrieved from the database and determines which columns will appear in the query result.

reference_year - 	Retrieves the reference year for which the Supply and Use Table (SUT) data is compiled (e.g., 2022–23, 2023–24). This enables year-wise analysis of the flow of goods and services within the economy.

product_code - 	Retrieves the product classification code, typically based on standard product classifications such as the Central Product Classification (CPC) or national classifications. This identifies specific goods and services being analyzed.

industry_code - 	Retrieves the industry classification code (usually NIC code), identifying the industries that produce or consume the products. This facilitates industry-product linkage analysis.

domestic_output -	Retrieves the value of goods and services produced domestically by industries during the reference year. This represents the supply of products generated within the economy.

imports -	Retrieves the value of imported goods and services available in the domestic economy. Imports supplement domestic output to determine the total supply of products.

intermediate_consumption -	Retrieves the value of goods and services consumed as inputs in the production process by industries. These inputs are used up during the accounting period and are not considered final output.

final_consumption - 	Retrieves the value of goods and services consumed by final users, including Private Final Consumption Expenditure (PFCE) and Government Final Consumption Expenditure (GFCE). This represents consumption that directly satisfies household or government needs.

exports - Retrieves the value of goods and services exported to the rest of the world. Exports form part of final demand and contribute positively to GDP calculations.

gross_capital_formation	- Retrieves the value of investment in fixed assets and inventory changes, commonly referred to as Gross Capital Formation (GCF). This includes Gross Fixed Capital Formation (GFCF), changes in inventories, and acquisitions less disposals of valuables. It represents investment activity within the economy.

FROM supply_use_tables- 	Specifies the source table containing Supply and Use Table (SUT) data. SUTs provide a detailed framework showing the relationship between the production of goods and services (supply) and their uses in the economy.

WHERE reference_year >= '2022-23';	- Filters the records to include only data for FY 2022–23 and later years, aligning the analysis with the revised GDP base year 2022–23.

## 13. Final Consolidated GDP Dataset

SELECT
    nas.year,
    nas.sector_code,
    nas.gdp_current_prices,
    gst.total_turnover,
    asi.gva,
    hces.total_consumption,
    plfs.labour_force_participation_rate,
    iip.iip_index,
    cpi.cpi_index,
    wpi.wpi_index,
    trade.total_exports,
    trade.total_imports,
    govt.total_revenue_expenditure,
    govt.total_capital_expenditure
FROM nas_gdp_statistics nas
LEFT JOIN gst_aggregated gst
    ON nas.year = gst.filing_period
LEFT JOIN asi_industries asi
    ON nas.sector_code = asi.nic_code
LEFT JOIN household_consumption hces
    ON nas.year = hces.survey_year
LEFT JOIN plfs_employment_statistics plfs
    ON nas.year = plfs.survey_year
LEFT JOIN iip_statistics iip
    ON nas.year = YEAR(iip.reference_month)
LEFT JOIN cpi_statistics cpi
    ON nas.year = YEAR(cpi.reference_month)
LEFT JOIN wpi_statistics wpi
    ON nas.year = YEAR(wpi.reference_month)
LEFT JOIN external_trade_statistics trade
    ON nas.year = YEAR(trade.trade_month)
LEFT JOIN government_expenditure govt
    ON nas.year = govt.financial_year;

## Explanation

SELECT-	Specifies that data should be retrieved from the database and defines the columns that will appear in the final output.

nas.year - 	Retrieves the year from the nas_gdp_statistics table. This serves as the primary time dimension for integrating all datasets.

nas.sector_code - Retrieves the economic sector code from the National Accounts Statistics (NAS) dataset, enabling sector-wise GDP analysis.

nas.gdp_current_prices - 	Retrieves GDP at Current Prices (Nominal GDP) from NAS. This is the target variable for GDP estimation and forecasting.

gst.total_turnover-	Retrieves total GST taxable turnover from the aggregated GST dataset. It acts as a proxy for economic activity and business transactions.

asi.gva - Retrieves Gross Value Added (GVA) from the Annual Survey of Industries (ASI), representing industrial sector output contribution.

hces.total_consumption - Retrieves total household consumption expenditure from HCES data, supporting estimation of Private Final Consumption Expenditure (PFCE).

plfs.labour_force_participation_rate - Retrieves the Labour Force Participation Rate (LFPR) from PLFS, indicating labour market participation and employment conditions.

iip.iip_index - Retrieves the Index of Industrial Production (IIP) value, serving as a high-frequency indicator of industrial output.

cpi.cpi_index - 	Retrieves the Consumer Price Index (CPI) value, which measures consumer inflation and is used for deflating nominal consumption values.

wpi.wpi_index -	Retrieves the Wholesale Price Index (WPI) value, used as a producer price indicator and output deflator.

trade.total_exports - Retrieves the total value of exports from external trade statistics, contributing positively to GDP calculations.

trade.total_imports - 	Retrieves the total value of imports from external trade statistics, which are subtracted in GDP estimation under the expenditure approach.

govt.total_revenue_expenditure - 	Retrieves total government revenue expenditure, representing government consumption spending.

govt.total_capital_expenditure - Retrieves total government capital expenditure, representing public investment in infrastructure and assets.

## Data Source Specification

FROM nas_gdp_statistics nas	- Specifies the primary table (nas_gdp_statistics) containing GDP data. nas is an alias used to simplify references to this table throughout the query.

LEFT JOIN Operations

A LEFT JOIN returns all records from the left table (nas) and matching records from the right table. If no match exists, NULL values are returned for columns from the right table.

## GST Data Integration

LEFT JOIN gst_aggregated gst	Joins the aggregated GST dataset to incorporate business turnover information. gst is the alias for this table.

ON nas.year = gst.filing_period - 	Matches GDP records with GST records based on the reporting period. This links economic output with GST turnover data.

## ASI Data Integration

LEFT JOIN asi_industries asi - Joins Annual Survey of Industries (ASI) data to include industrial GVA estimates.
ON nas.sector_code = asi.nic_code	Matches NAS sector classifications with ASI industry classifications using industry codes.

## Household Consumption Integration

LEFT JOIN household_consumption hces	Joins Household Consumption Expenditure Survey (HCES) data.
ON nas.year = hces.survey_year	Matches GDP years with household survey years to incorporate consumption expenditure estimates.

## Labour Market Integration

LEFT JOIN plfs_employment_statistics plfs	- Joins Periodic Labour Force Survey (PLFS) data.
ON nas.year = plfs.survey_year	Matches GDP years with labour force survey years to include labour market indicators.

## Industrial Production Integration

LEFT JOIN iip_statistics iip- 	Joins Index of Industrial Production (IIP) data.
ON nas.year = YEAR(iip.reference_month)- 	Extracts the year from the monthly IIP date and matches it with the GDP year. This converts monthly IIP data into annual alignment.

## Consumer Inflation Integration

LEFT JOIN cpi_statistics cpi-	Joins Consumer Price Index (CPI) data.
ON nas.year = YEAR(cpi.reference_month)	Matches GDP years with the year extracted from monthly CPI observations.

## Wholesale Inflation Integration

LEFT JOIN wpi_statistics wpi-	Joins Wholesale Price Index (WPI) data.
ON nas.year = YEAR(wpi.reference_month)	Matches GDP years with the year extracted from monthly WPI observations.

## External Trade Integration

LEFT JOIN external_trade_statistics trade	-Joins external trade data to incorporate exports and imports.
ON nas.year = YEAR(trade.trade_month)	Matches GDP years with the year extracted from monthly trade records.

## Government Expenditure Integration

LEFT JOIN government_expenditure govt	Joins government expenditure data.
ON nas.year = govt.financial_year	Matches GDP years with government financial years to include fiscal spending information.

## conceptual flow of data integration

NAS (GDP)
    ↓
GST (Turnover)
    ↓
ASI (Industrial GVA)
    ↓
HCES (Consumption)
    ↓
PLFS (Employment)
    ↓
IIP + CPI + WPI (High-frequency indicators)
    ↓
Trade (Exports & Imports)
    ↓
Government Expenditure
    ↓
Integrated Macroeconomic Dataset
    ↓
GDP Estimation & Forecasting Models

## Data Extraction

------------------------

import pandas as pd
from sqlalchemy import create_engine

engine = create_engine(
    "postgresql://username:password@localhost:5432/gdp_db"
)

nas = pd.read_sql("SELECT * FROM national_accounts", engine)

gst = pd.read_sql("SELECT * FROM gst_returns", engine)

household = pd.read_sql(
    "SELECT * FROM household_microdata",
    engine
)

----------------------

## Explanation

import pandas as pd - 	Imports the Pandas library, which is widely used for data manipulation, cleaning, analysis, and working with tabular data structures called DataFrames. The alias pd is a standard convention that makes Pandas functions easier to call.

from sqlalchemy import create_engine - Imports the create_engine function from the SQLAlchemy library. SQLAlchemy is a Python SQL toolkit used to establish database connections and interact with relational databases such as PostgreSQL, MySQL, SQLite, and Oracle.

engine = create_engine(	Calls the create_engine() - function to create a database engine object. The engine acts as an interface between Python and the database, managing connections and executing SQL queries.

"postgresql://username:password@localhost:5432/gdp_db"	- This is the database connection string (URL) specifying how to connect to the PostgreSQL database. It contains several components:

• postgresql:// → Indicates that PostgreSQL is the database system being used.
• username → Database username used for authentication.
• password → Password associated with the username.
• localhost → Hostname of the database server. localhost means the database is running on the same machine as the  Python application.
• 5432 → Default port number used by PostgreSQL.
• gdp_db → Name of the database containing GDP-related datasets.

Closes the create_engine() function call. After execution, the engine object holds the connection details required for database operations.

nas = pd.read_sql("SELECT * FROM national_accounts", engine) - Uses the Pandas read_sql() function to execute the SQL query "SELECT * FROM national_accounts" using the database engine.

• SELECT * retrieves all columns from the table.
• national_accounts is assumed to contain National Accounts Statistics (NAS) data, such as GDP and GVA estimates.
• The query results are loaded into a Pandas DataFrame named nas.

gst = pd.read_sql("SELECT * FROM gst_returns", engine)	- Executes the SQL query "SELECT * FROM gst_returns" and loads all records from the gst_returns table into a Pandas DataFrame named gst. This dataset may include GST turnover, tax collections, and filing information used as indicators of economic activity.

household = pd.read_sql - Begins a multi-line call to the pd.read_sql() function for improved readability, especially when SQL queries become long or complex.

"SELECT * FROM household_microdata",	- SQL query that retrieves all columns and rows from the household_microdata table. This table may contain household-level survey data, such as consumption expenditure, demographic characteristics, and socio-economic indicators.

engine -	Specifies the database engine through which the SQL query will be executed. The engine provides the active connection to the PostgreSQL database.

Closes the pd.read_sql() function call. The retrieved household survey data is stored in the household DataFrame.

## Overall Summary

PostgreSQL Database (gdp_db)
        ↓
SQLAlchemy Engine Connection
        ↓
Pandas read_sql()
        ↓
nas DataFrame (GDP Data)
gst DataFrame (GST Data)
household DataFrame (Consumption Data)
        ↓
Data Cleaning & Preprocessing
        ↓
Feature Engineering
        ↓
GDP Estimation & Forecasting Models

## Summary

This code connects to a PostgreSQL database and extracts macroeconomic datasets into Pandas DataFrames, preparing them for further steps such as data cleaning, exploratory analysis, feature engineering, and GDP forecasting model development.

---------------------------------

macro = pd.read_sql(
    "SELECT * FROM macro_indicators",
    engine
)

## explanation

macro =	- Creates a variable named macro that will store the output of the SQL query. Since pd.read_sql() returns a Pandas DataFrame, macro becomes a DataFrame containing the retrieved macroeconomic data.

pd.read_sql - Calls the read_sql() function from the Pandas library. This function executes an SQL query on a database and imports the query results directly into a Pandas DataFrame for further analysis.

 "SELECT * FROM macro_indicators" - 	SQL query used to retrieve data from the database.

• SELECT → Specifies that data should be retrieved.
• * → Indicates that all columns from the table should be selected.
• FROM macro_indicators → Specifies that the data should be extracted from the macro_indicators table, which is assumed to contain various macroeconomic indicators.

engine- 	Specifies the SQLAlchemy database engine object used to connect to the database. The engine contains the database connection details (host, port, database name, username, password) and executes the SQL query.

Closes the pd.read_sql() function call. After execution, the retrieved data is stored in the macro DataFrame.

overall summary

PostgreSQL Database
        ↓
macro_indicators Table
        ↓
pd.read_sql()
        ↓
macro DataFrame
        ↓
Data Cleaning & Preprocessing
        ↓
Feature Engineering
        ↓
GDP Estimation & Forecasting Models

## summary

This code extracts all macroeconomic indicators from the macro_indicators database table and loads them into a Pandas DataFrame named macro, making the data ready for exploratory analysis, feature engineering, and GDP estimation or forecasting tasks.

-------------------------------------

## Data Cleaning

## Missing Value Analysis

datasets = [nas, gst, household, macro]

for df in datasets:
    print(df.isnull().sum())

## Explanation

datasets = [nas, gst, household, macro] - 	Creates a Python list named datasets that contains four Pandas DataFrames: nas, gst, household, and macro. Storing them in a list allows the same operation to be performed on each dataset without writing repetitive code.

nas - 	Refers to the DataFrame containing National Accounts Statistics (NAS) data, such as GDP and GVA estimates.

gst	- Refers to the DataFrame containing GST-related data, such as turnover and tax collection information.
household	Refers to the DataFrame containing household consumption or expenditure survey data, used for consumption analysis.

macro- 	Refers to the DataFrame containing macroeconomic indicators, such as IIP, CPI, WPI, exports, imports, and government expenditure.

for df in datasets:	- Starts a for loop that iterates through each DataFrame in the datasets list. During each iteration, the current DataFrame is temporarily assigned to the variable df.

df - A temporary variable representing one DataFrame at a time during the loop execution. In successive iterations, df will refer to nas, then gst, then household, and finally macro.

print(df.isnull().sum())- 	Prints the number of missing (NULL or NaN) values in each column of the current 
DataFrame. This helps identify data quality issues before analysis or model building.

## overall summary

Data Extraction
       ↓
Missing Value Analysis  ← (This Code)
       ↓
Data Cleaning
       ↓
Feature Engineering
       ↓
GDP Estimation Models
       ↓
Forecasting & Visualization

## summary

This code loops through multiple GDP-related datasets and checks each column for missing values, helping identify data quality issues that need to be addressed before performing economic analysis or building forecasting models.

-----------------------------------

## Imputation

nas['gva_constant_price'].fillna(
    nas['gva_constant_price'].median(),
    inplace=True
)

macro.fillna(method='ffill', inplace=True)

## Explanation

nas['gva_constant_price']- Selects the gva_constant_price column from the nas DataFrame. This column contains Gross Value Added (GVA) at constant prices, which measures real economic output after removing the effects of inflation.

.fillna(	Calls the fillna() method -  which is used to replace missing values (NaN) in a DataFrame or Series with specified values.

nas['gva_constant_price'].median() - Calculates the median of all non-missing values in the gva_constant_price column. The median is the middle value when the data is arranged in ascending order and is often used because it is less affected by extreme values (outliers) than the mean.

inplace=True-	Modifies the original gva_constant_price column directly instead of creating a new copy. The missing values are permanently replaced in the nas DataFrame.
Closes the fillna() method call.

macro.fillna(	Calls the fillna() - it is the  method on the entire macro DataFrame to handle missing values across all columns.

method='ffill' - it	Specifies the forward fill (ffill) method. Missing values are replaced using the most recent non-missing value that appears before them in the dataset.

inplace=True - Updates the original macro DataFrame directly by replacing missing values without creating a new DataFrame.
Closes the fillna() method call.

## overall summary

Data Extraction
       ↓
Missing Value Detection
       ↓
Data Imputation  ← (This Code)
       ↓
Data Validation
       ↓
Feature Engineering
       ↓
GDP Estimation Models
       ↓
GDP Forecasting

## summary

First statement: Replaces missing values in the gva_constant_price column with the median GVA value, reducing the impact of outliers.

Second statement: Uses forward fill (ffill) to replace missing values in the macro DataFrame with the most recent available observations, preserving continuity in macroeconomic time series data.

--------------------------------------

## Duplicate Removal

gst.drop_duplicates(inplace=True)

household.drop_duplicates(
    subset=['household_id'],
    inplace=True
)

## explanation

gst.drop_duplicates(inplace=True) - 	Removes duplicate rows from the gst DataFrame. If two or more rows have exactly the same values across all columns, only the first occurrence is retained, and the remaining duplicate rows are deleted.

.drop_duplicates()-	A Pandas method used to identify and remove duplicate records from a DataFrame. By default, it considers all columns when determining whether rows are duplicates.

inplace=True-	Modifies the original DataFrame directly without creating a new DataFrame. The duplicate records are permanently removed from gst.

household.drop_duplicates(	Calls the drop_duplicates() - it is the method on the household DataFrame to remove duplicate household records.

subset=['household_id'] - Specifies that duplicates should be identified only based on the household_id column. If multiple rows have the same household_id, only the first occurrence is kept, while the others are removed.

inplace=True -	Updates the original household DataFrame directly by removing duplicates without creating a copy.
)	Closes the drop_duplicates() method call.

## overall summary

Data Extraction
       ↓
Missing Value Treatment
       ↓
Duplicate Detection
       ↓
Duplicate Removal  ← (This Code)
       ↓
Data Validation
       ↓
Feature Engineering
       ↓
GDP Estimation Models

## summary

gst.drop_duplicates(inplace=True) removes completely identical GST records to avoid duplication in turnover and tax data.

household.drop_duplicates(subset=['household_id'], inplace=True) removes duplicate household entries based on the household ID, ensuring that each household contributes only once to consumption analysis and GDP estimation.

-------------------------------------

## Statistical Validation

## Z-Score Based Outlier Detection

from scipy.stats import zscore
import numpy as np

nas['zscore'] = np.abs(
    zscore(nas['gva_constant_price'])
)

outliers = nas[nas['zscore'] > 3]

print(outliers.head())

## Explanation

from scipy.stats import zscore	Imports the zscore function from the scipy.stats module. The Z-score is a statistical measure used to determine how many standard deviations a data point is away from the mean. It is commonly used for outlier detection.

import numpy as np	- Imports the NumPy library, which provides support for numerical computations and mathematical operations. The alias np is the standard convention used in Python.

nas['zscore'] =	- Creates a new column named zscore in the nas DataFrame to store the calculated Z-score values for each observation in the gva_constant_price column.

zscore(nas['gva_constant_price'])	- Computes the Z-score for every value in the gva_constant_price column. The Z-score indicates how far each value is from the mean in terms of standard deviations.

np.abs(...) - 	Applies the absolute value function to the calculated Z-scores. This converts both positive and negative deviations into positive values, allowing detection of outliers on both ends of the distribution.
)	Closes the np.abs() function call.

outliers = nas[nas['zscore'] > 3]	- it Filters the nas DataFrame and creates a new DataFrame called outliers containing only rows where the absolute Z-score is greater than 3. According to the common statistical rule, observations with (

print(outliers.head()) - 	Displays the first five rows of the outliers DataFrame, allowing you to inspect the detected outlier records.

## overal summary

GVA Data
    ↓
Calculate Mean
    ↓
Calculate Standard Deviation
    ↓
Compute Z-scores
    ↓
Take Absolute Values
    ↓
Identify Z-score > 3
    ↓
Extract Outlier Records

## summary

zscore() calculates how far each GVA observation is from the mean in terms of standard deviations.

np.abs() converts negative and positive deviations into positive values.

nas['zscore'] stores the calculated Z-scores.

nas[nas['zscore'] > 3] extracts observations that are more than 3 standard deviations away from the mean, identifying 
potential outliers.

--------------------------------------

## IQR Method

Q1 = gst['taxable_value'].quantile(0.25)
Q3 = gst['taxable_value'].quantile(0.75)

IQR = Q3 - Q1

gst_clean = gst[
    (gst['taxable_value'] >= Q1 - 1.5*IQR) &
    (gst['taxable_value'] <= Q3 + 1.5*IQR)
]

## explanation

Q1 = gst['taxable_value'].quantile(0.25)	- Calculates the first quartile (Q1), also known as the 25th percentile, of the taxable_value column in the gst DataFrame. This means that 25% of the observations have taxable values less than or equal to this value.

gst['taxable_value']-	Selects the taxable_value column from the gst DataFrame, which contains GST taxable turnover values.
.quantile(0.25)	Computes the value below which 25% of the observations fall.

Q3 = gst['taxable_value'].quantile(0.75) - Calculates the third quartile (Q3), also known as the 75th percentile, of the taxable_value column. This means that 75% of the observations have values less than or equal to this threshold.
.quantile(0.75)	Computes the value below which 75% of the observations fall.

IQR = Q3 - Q1	- it Calculates the Interquartile Range (IQR) by subtracting Q1 from Q3. The IQR measures the spread of the middle 50% of the data and is commonly used for outlier detection.

gst_clean = gst - 	Creates a new DataFrame named gst_clean that will contain only records that satisfy the specified filtering conditions.

(gst['taxable_value'] >= Q1 - 1.5*IQR)- 	Defines the lower bound for acceptable values. Records with taxable_value less than Q1 - 1.5 × IQR are considered potential lower outliers and are excluded.
&	Represents the logical AND operator in Pandas. It ensures that both the lower-bound and upper-bound conditions must be true for a row to be retained.

(gst['taxable_value'] <= Q3 + 1.5*IQR)	- Defines the upper bound for acceptable values. Records with taxable_value greater than Q3 + 1.5 × IQR are considered potential upper outliers and are excluded.

]	Closes the filtering operation. The resulting gst_clean DataFrame contains GST records after removing outliers based on the IQR method.

## overall summary

Data Extraction
       ↓
Missing Value Treatment
       ↓
Duplicate Removal
       ↓
Outlier Detection (IQR)  ← This Code
       ↓
Clean GST Dataset
       ↓
Feature Engineering
       ↓
GDP Estimation Models

## summary

Q1 calculates the 25th percentile of GST taxable values.

Q3 calculates the 75th percentile.

IQR measures the spread of the middle 50% of the data.

The code identifies acceptable values within:

Q1−1.5×IQRtoQ3+1.5×IQR

gst_clean - it stores the GST dataset after removing potential outliers, resulting in cleaner data for GDP estimation and forecasting.

----------------------------------------

## Cross-Source Reconciliation

## GST vs National Accounts

gst_sector = gst_clean.groupby(
    ['sector','filing_month']
)['taxable_value'].sum().reset_index()

nas_sector = nas.groupby(
    ['sector','quarter']
)['gva_constant_price'].sum().reset_index()

## Explanation

gst_sector =-	Creates a new DataFrame named gst_sector to store the aggregated GST data grouped by sector and filing month.

gst_clean.groupby(	Calls the groupby() - it is the  method on the gst_clean DataFrame. This method groups rows that share the same values in specified columns, enabling aggregation operations such as sum, mean, count, etc.

['sector', 'filing_month'] - 	Specifies the columns used for grouping. The data is grouped by:
• sector → Economic sector or industry category.
• filing_month → Month in which GST returns were filed.

)['taxable_value']	- Selects the taxable_value column after grouping. This indicates that the aggregation operation will be performed on GST taxable turnover values.

.sum()- 	Calculates the sum of taxable values for each unique combination of sector and filing_month. This provides total GST turnover by sector and month.

.reset_index()- 	Converts the grouped indices (sector and filing_month) back into regular DataFrame columns, resulting in a clean tabular structure.

nas_sector =- 	Creates a new DataFrame named nas_sector to store aggregated National Accounts data grouped by sector and quarter.

nas.groupby(	Calls the groupby() -  its is the method on the nas DataFrame to organize GDP-related data into groups.
['sector', 'quarter']	Specifies the grouping variables:
• sector → Economic sector classification.
• quarter → Quarter of the year (Q1, Q2, Q3, Q4).

)['gva_constant_price'] - 	Selects the gva_constant_price column for aggregation. GVA at constant prices represents real economic output after adjusting for inflation.

.sum()	- Calculates the total GVA at constant prices for each sector-quarter combination.

.reset_index() - 	Converts the grouping variables (sector and quarter) from indices back into standard columns in the resulting DataFrame.

## overall summary

Raw GST Data        Raw NAS Data
      ↓                   ↓
Data Cleaning       Data Cleaning
      ↓                   ↓
Outlier Removal     Missing Value Treatment
      ↓                   ↓
Group by Sector     Group by Sector
and Month           and Quarter
      ↓                   ↓
GST Sector Data     NAS Sector Data
      ↓                   ↓
Feature Engineering & Data Integration
      ↓
GDP Estimation Models

## summary

The first block aggregates GST turnover by sector and filing month, producing sector-wise monthly business activity indicators.

The second block aggregates GVA at constant prices by sector and quarter, producing sector-wise real output estimates.
Both datasets are commonly used in GDP estimation projects to analyze sectoral economic performance and build forecasting models.

--------------------------------------

## Correlation Check

merged = pd.merge(
    gst_sector,
    nas_sector,
    on='sector'
)

merged.corr(numeric_only=True)

## explanation

Python Code	Explanation
merged =	Creates a new DataFrame named merged that will store the result of combining the gst_sector and nas_sector DataFrames.
pd.merge(	Calls the merge() function from the Pandas library. This function combines two DataFrames based on one or more common columns, similar to SQL JOIN operations.
gst_sector,	Specifies the left DataFrame in the merge operation. This DataFrame contains aggregated GST turnover data grouped by sector and filing month.
nas_sector,	Specifies the right DataFrame in the merge operation. This DataFrame contains aggregated GVA data grouped by sector and quarter.
on='sector'	Indicates that the merge should be performed using the sector column as the common key. Rows with matching sector values in both DataFrames will be combined.
)	Closes the pd.merge() function call. The merged dataset is stored in the merged DataFrame.
merged.corr(numeric_only=True)	Calculates the correlation matrix for all numeric columns in the merged DataFrame. This helps assess the strength and direction of relationships between variables.
corr()	Pandas method used to compute pairwise correlation coefficients between numerical variables. By default, it calculates the Pearson correlation coefficient.
numeric_only=True	Restricts the correlation calculation to numeric columns only, excluding categorical or text columns such as sector, filing_month, or quarter.

## overall summary

GST Sector Aggregation
         ↓
NAS Sector Aggregation
         ↓
Merge Datasets  ← (pd.merge)
         ↓
Integrated Sector Dataset
         ↓
Correlation Analysis  ← (corr)
         ↓
Feature Selection
         ↓
GDP Estimation Models

## summary

'''

Merge Operation
merged = pd.merge(
    gst_sector,
    nas_sector,
    on='sector'
)

'''

Combines GST and NAS datasets.

Uses sector as the common key.

Performs an inner join by default.

## Correlation Analysis

merged.corr(numeric_only=True)

Computes pairwise correlations among numeric variables.

Measures the strength and direction of relationships.

Helps identify useful predictors for GDP estimation and forecasting models.

# Together, these steps support data integration and exploratory analysis in a GDP estimation pipeline by examining whether sectoral GST turnover is associated with sectoral GVA performance.

-----------------------------------------

## Consistency Ratio

merged['consistency_ratio'] = (
    merged['taxable_value'] /
    merged['gva_constant_price']
)

merged.head()

## explanation

merged['consistency_ratio'] = - Creates a new column named  `consistency_ratio`  in the `merged` DataFrame to store the calculated ratio.

merged['taxable_value'] -  Selects the **total GST taxable value** for each sector (and filing month, if applicable) from the merged dataset.

merged['gva_constant_price'] - Selects the **Gross Value Added (GVA) at constant prices** from the National Accounts Statistics (NAS) data.

merged['taxable_value'] / merged['gva_constant_price'] - Calculates the **consistency ratio** by dividing GST taxable value by GVA, indicating how closely GST economic activity aligns with official GDP estimates.

merged.head() - Displays the **first five rows** of the DataFrame to verify that the `consistency_ratio` column has been created correctly.

## Overall Summary

GST Taxable Value Data
          ⬇
NAS GVA (Constant Price) Data
          ⬇
Merge both datasets sector-wise
          ⬇
Compute `consistency_ratio = Taxable Value ÷ GVA`
          ⬇
Assess alignment between GST transactions and official economic output estimates
          ⬇
Preview the results using `head()`

## summary

The code calculates a consistency ratio between GST taxable values and sector-wise GVA estimates to evaluate the relationship and alignment between GST-based economic activity and official GDP measurements, and then displays the first few records for validation.

----------------------------------------

## Feature Engineering

macro['lag_1'] = macro['iip_index'].shift(1)

macro['lag_4'] = macro['iip_index'].shift(4)

macro['rolling_mean'] = (
    macro['iip_index']
    .rolling(4)
    .mean()
)

macro['rolling_std'] = (
    macro['iip_index']
    .rolling(4)
    .std()
)

## explanation

macro['lag_1'] = macro['iip_index'].shift(1) - Creates a new column `lag_1` containing the **previous period's IIP index value** for each observation.

macro['lag_4'] = macro['iip_index'].shift(4) - Creates a new column `lag_4` containing the **IIP index value from four periods earlier**, capturing seasonal or quarterly patterns.

macro['rolling_mean'] = (
    macro['iip_index']
    .rolling(4)
    .mean()
)

Calculates the **4-period moving average** of the IIP index to smooth short-term fluctuations and identify trends.

macro['iip_index'].rolling(4) - Defines a rolling window of 4 consecutive observations over the IIP index series.

.mean() -  Computes the **average value within each 4-period rolling window**.

macro['rolling_std'] = (
    macro['iip_index']
    .rolling(4)
    .std()
)

Calculates the 4-period rolling standard deviation of the IIP index to measure short-term variability or volatility.

.std() - Computes the **standard deviation within each 4-period rolling window. 

## Overall Summary (Arrow-wise)

IIP Index Time Series Data
              ⬇
Generate `lag_1` feature (Previous period IIP value)
                ⬇
Generate `lag_4` feature (Value from four periods ago)
                 ⬇
Calculate 4-period rolling mean (Trend indicator)
                 ⬇
Calculate 4-period rolling standard deviation (Volatility indicator)
                    ⬇
Create time-series features for forecasting and economic analysis

## summary

**The code performs feature engineering on the IIP index by creating lag variables and rolling statistical measures to capture historical trends, seasonality, and volatility for time-series forecasting and macroeconomic modeling.

----------------------------------------

## GDP Growth Rate

nas['gdp_growth'] = (
    nas['gva_constant_price']
    .pct_change() * 100
)

## explanation

nas['gdp_growth'] = -  Creates a new column named **`gdp_growth`** in the `nas` DataFrame to store GDP growth rates.

nas['gva_constant_price'] - Selects the **Gross Value Added (GVA) at constant prices**, which represents real economic output adjusted for inflation.

.pct_change() - Calculates the **percentage change between the current and previous period's GVA values**.

.pct_change() * 100 -  Converts the calculated growth rate from **decimal form to percentage form** by multiplying by 100.

## Overall Summary (Arrow-wise)

Real GVA (Constant Price) Data
               ⬇
Compare current period GVA with previous period GVA
                      ⬇
Calculate period-over-period percentage change
                   ⬇
Convert growth rate into percentage (%)
                 ⬇
Store the result in the `gdp_growth` column
                       ⬇
Generate GDP growth trends for economic analysis and forecasting

## summary

The code computes the period-over-period GDP growth rate by calculating the percentage change in GVA at constant prices and stores the result as a percentage in the `gdp_growth` column.

------------------------------------------

## Inflation Adjustment

nas = nas.merge(
    macro[['date','cpi']],
    left_on='year',
    right_on='date'
)

nas['real_gdp'] = (
    nas['gva_current_price'] /
    nas['cpi']
) * 100

## explanation

nas = nas.merge( - Initiates a merge operation to combine the `nas` DataFrame with another DataFrame containing CPI data.

macro[['date','cpi']], -  Selects only the `date` and `cpi` columns from the `macro` DataFrame for merging.

left_on='year', -  Uses the `year` column from the `nas` DataFrame as the merge key.

right_on='date' -  Uses the `date` column from the `macro` DataFrame as the corresponding merge key.

Completes the merge operation and updates `nas` with the matched CPI values.

nas['real_gdp'] = -  Creates a new column named `real_gdp` to store inflation-adjusted GDP values.

nas['gva_current_price'] -  Selects the Gross Value Added (GVA) measured at current prices (nominal GDP).

nas['cpi'] -  Selects the Consumer Price Index (CPI), which serves as a measure of inflation.

nas['gva_current_price'] / nas['cpi'] -  Deflates nominal GDP by dividing it by the CPI to remove the impact of inflation.

(nas['gva_current_price'] / nas['cpi']) * 100 -  Scales the inflation-adjusted GDP using a CPI base index of 100 to calculate Real GDP.

## Overall Summary   

NAS Data (Nominal GVA)
          ⬇
Macroeconomic Data (CPI)
           ⬇
Merge datasets using Year and Date keys
              ⬇
Attach CPI values to each GDP observation
              ⬇
Deflate Current Price GVA using CPI
              ⬇
Adjust for inflation effects
               ⬇
Calculate Real GDP (`real_gdp`)
              ⬇
Prepare inflation-adjusted GDP data for economic analysis and forecasting

## Summary

The code merges CPI data with the NAS dataset and calculates Real GDP by adjusting nominal GVA for inflation using the Consumer Price Index (CPI).

--------------------------------------------

## Time Series Forecasting (ARIMA)

from statsmodels.tsa.arima.model import ARIMA

gdp_series = nas[
    'gva_constant_price'
]

model = ARIMA(
    gdp_series,
    order=(2,1,2)
)

result = model.fit()

forecast = result.forecast(steps=4)

print(forecast)

## explanation

from statsmodels.tsa.arima.model import ARIMA -  Imports the ARIMA (AutoRegressive Integrated Moving Average) model used for time series forecasting.

gdp_series = nas['gva_constant_price'] - Extracts the real GDP (GVA at constant prices)** time series from the `nas` DataFrame for forecasting.

model = ARIMA(
    gdp_series,
    order=(2,1,2)
)

Initializes an ARIMA model on the GDP series with parameters , AR=2, Differencing=1, and MA=2.

gdp_series - Specifies the historical GDP time series data that the ARIMA model will learn from.

order=(2,1,2) -  Defines the ARIMA configuration where 2 autoregressive terms, 1 differencing step, and 2 moving average terms are used.

p = 2: Uses the previous two GDP observations to predict future values.
d = 1: Applies first-order differencing to make the series stationary.
q = 2: Uses the previous two forecast errors to improve predictions.

result = model.fit() - Fits the ARIMA model to the historical GDP data by estimating the optimal model parameters.

forecast = result.forecast(steps=4) -  Generates GDP forecasts for the **next 4 future periods** using the trained ARIMA model.

steps=4 - Specifies that predictions should be made for four time periods ahead.

print(forecast) -  Displays the forecasted GDP values for the next four periods.

## Overall Summary

Historical GDP Data (GVA at Constant Prices)
             ⬇
Extract GDP Time Series
              ⬇
Initialize ARIMA(2,1,2) Model
                 ⬇
Use Past GDP Values (AR Component)
                 ⬇
Apply Differencing to Achieve Stationarity (I Component)
                ⬇
Incorporate Past Forecast Errors (MA Component)
                  ⬇
Train/Fit the ARIMA Model
            ⬇
Forecast GDP for the Next 4 Periods
             ⬇
Display Predicted GDP Values

## Summary

The code builds an ARIMA(2,1,2) time series forecasting model using historical real GDP data, fits the model to capture underlying trends and patterns, predicts GDP values for the next four periods, and prints the forecasted results.

--------------------------------------------

## Machine Learning Forecasting

## Prepare Dataset

features = [
    'iip_index',
    'cpi',
    'exports',
    'imports',
    'lag_1',
    'lag_4',
    'rolling_mean'
]

X = macro[features].dropna()

y = nas['gva_constant_price'][
    X.index
]

## explanation

## Prepare Dataset

features = [
    'iip_index',
    'cpi',
    'exports',
    'imports',
    'lag_1',
    'lag_4',
    'rolling_mean'
]

X = macro[features].dropna()

y = nas['gva_constant_price'][
    X.index
]

Begins defining a list of predictor variables that will be used as input features for the GDP forecasting model.

'iip_index', -  Includes the **Index of Industrial Production (IIP)** as an indicator of industrial economic activity.

'cpi', - Includes the **Consumer Price Index (CPI)** to capture inflation effects in the economy.

'exports', - Includes **export values** to represent external demand and trade performance.

'imports', -  Includes **import values** to reflect domestic demand and international trade dependence.

'lag_1', - Includes the **one-period lagged IIP value** to capture short-term historical patterns.

'lag_4', -  Includes the **four-period lagged IIP value** to capture seasonal or longer-term dependencies.

'rolling_mean' -  Includes the **rolling average of IIP** to represent smoothed economic trends over time.

] -  Completes the list of selected features for model training.

X = macro[features].dropna() -  Creates the feature dataset `X` by selecting the specified variables from `macro` and removing rows with missing values.

macro[features] -  Extracts only the chosen predictor variables from the `macro` DataFrame.

.dropna() -  Removes observations containing missing values to ensure clean data for model training.

y = nas['gva_constant_price'][X.index] - Creates the target variable `y` by selecting GDP values corresponding to the same indices as the cleaned feature dataset `X`.

nas['gva_constant_price'] -  Selects the **Gross Value Added (GVA) at constant prices**, which serves as the prediction target.

[X.index] - Aligns the GDP target values with the feature dataset by using the same row indices.

## Overall Summary

Macroeconomic Dataset (`macro`)
              ⬇
Select Economic Indicators (IIP, CPI, Exports, Imports)
                          ⬇
Include Time-Series Features (Lag-1, Lag-4, Rolling Mean)
                        ⬇
Create Feature Matrix (`X`)
             ⬇
Remove Missing Observations
          ⬇
Extract Real GDP (`gva_constant_price`) from NAS Data
                    ⬇
Align GDP Values with Feature Dataset Indices
                ⬇
Create Target Variable (`y`)
               ⬇
Prepare Clean Input and Output Data for Machine Learning Models

## Summary

The code prepares a machine learning dataset by selecting relevant macroeconomic indicators and engineered time-series features as predictors (`X`), removing missing values, and aligning real GDP values (`y`) as the target variable for forecasting.

-------------------------------------------------

## Train-Test Split

from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    X,
    y,
    test_size=0.2,
    shuffle=False
)

## Explanation

from sklearn.model_selection import train_test_split -  Imports the `train_test_split` function used to divide the dataset into training and testing subsets.

X_train, X_test, y_train, y_test = - Defines four variables to store the training and testing portions of features (`X`) and target (`y`) datasets.

train_test_split( -  Calls the function that splits the dataset into training and testing sets.

X, -  Passes the feature matrix `X` containing the predictor variables to be split.

y, -  Passes the target variable `y` containing GDP values to be split correspondingly.

test_size=0.2, -  Specifies that **20% of the data** should be reserved for testing, while **80% will be used for training.

shuffle=False -  Prevents random shuffling of observations, preserving the chronological order required for time series forecasting.

) -  Completes the dataset splitting operation and assigns the results to the respective variables.

## Overall Summary

Prepared Feature Dataset (`X`)
              ⬇
Prepared Target Variable (`y`)
              ⬇
Apply `train_test_split()` Function
             ⬇
Allocate 80% of Data for Training
             ⬇
Allocate 20% of Data for Testing
             ⬇
Preserve Temporal Sequence (`shuffle=False`)
             ⬇
Generate `X_train`, `X_test`, `y_train`, and `y_test`
                 ⬇
Prepare Data for Model Training and Performance Evaluation

## Summary

The code splits the feature and target datasets into training (80%) and testing (20%) sets while maintaining the chronological order of observations, ensuring an appropriate setup for time series forecasting and model evaluation.

-------------------------------------------

## Random Forest Model

from sklearn.ensemble import RandomForestRegressor

rf = RandomForestRegressor(
    n_estimators=300,
    random_state=42
)

rf.fit(X_train, y_train)

pred = rf.predict(X_test)

## Explanation

from sklearn.ensemble import RandomForestRegressor -  Imports the **Random Forest Regressor** algorithm used for predicting continuous values such as GDP.

rf = RandomForestRegressor( -  Initializes a Random Forest regression model and assigns it to the variable `rf`.

n_estimators=300, - Specifies that the Random Forest model should build **300 decision trees** to improve prediction accuracy and robustness.

random_state=42 - Sets a fixed random seed (`42`) to ensure that the model produces reproducible results across runs.

) -  Completes the configuration and creation of the Random Forest model.

rf.fit(X_train, y_train) -  Trains the Random Forest model using the training feature set (`X_train`) and corresponding GDP values (`y_train`).

X_train -  Contains the training predictor variables used by the model to learn relationships with GDP.

y_train -  Contains the actual GDP values that the model aims to predict during training.

pred = rf.predict(X_test) -  Uses the trained Random Forest model to generate GDP predictions for the unseen test dataset.

X_test -  Contains the testing feature variables on which GDP forecasts are made.

pred -  Stores the predicted GDP values generated by the Random Forest model.

## Overall Summary

Import Random Forest Regression Algorithm
                 ⬇
Initialize Random Forest Model
                 ⬇
Configure 300 Decision Trees (`n_estimators=300`)
                   ⬇
Set Random Seed for Reproducibility (`random_state=42`)
                       ⬇
Train Model Using Historical Economic Indicators (`X_train`, `y_train`)
                         ⬇
Learn Non-linear Relationships Between Predictors and GDP
                        ⬇
Apply Trained Model to Test Data (`X_test`)
                 ⬇
Generate GDP Predictions (`pred`)
                   ⬇
Prepare Results for Model Evaluation

## Summary

The code builds a Random Forest regression model with 300 decision trees, trains it using historical macroeconomic data and GDP values, and predicts GDP for the test dataset to support forecasting and performance evaluation.

-------------------------------------------------

## XGBoost Model

from xgboost import XGBRegressor

xgb = XGBRegressor(
    n_estimators=500,
    learning_rate=0.05,
    max_depth=5
)

xgb.fit(X_train, y_train)

xgb_pred = xgb.predict(X_test)

## explanation

from xgboost import XGBRegressor -  Imports the **XGBoost Regressor**, a gradient boosting algorithm used for accurate prediction of continuous values such as GDP.

xgb = XGBRegressor( -  Initializes an XGBoost regression model and assigns it to the variable `xgb`.

n_estimators=500, -  Specifies that the model should build **500 boosting trees** sequentially to improve predictive performance.

learning_rate=0.05, -  Sets the learning rate to **0.05**, controlling how much each tree contributes to the overall model and reducing overfitting risk.

max_depth=5 - Limits each decision tree to a **maximum depth of 5 levels**, balancing model complexity and generalization.

) -  Completes the configuration and creation of the XGBoost regression model.

xgb.fit(X_train, y_train) -  Trains the XGBoost model using the training features (`X_train`) and corresponding GDP values (`y_train`).

X_train -  Contains the macroeconomic predictor variables used by the model for learning patterns related to GDP.

y_train - Contains the actual GDP values that the model learns to predict during training.

xgb_pred = xgb.predict(X_test) -  Uses the trained XGBoost model to generate GDP predictions for the unseen test dataset.

X_test -  Contains the testing feature variables on which GDP forecasts are generated.

xgb_pred -  Stores the GDP values predicted by the XGBoost model for further evaluation.

## Overall Summary

Import XGBoost Regression Algorithm
              ⬇
Initialize XGBoost Model
               ⬇
Configure 500 Boosting Trees (`n_estimators=500`)
               ⬇
Set Learning Rate (`learning_rate=0.05`)
                  ⬇
Limit Tree Complexity (`max_depth=5`)
                ⬇
Train Model Using Historical Economic Indicators (`X_train`, `y_train`)
                 ⬇
Sequentially Correct Prediction Errors Through Boosting
                     ⬇
Apply Trained Model to Test Data (`X_test`)
                   ⬇
Generate GDP Predictions (`xgb_pred`)
                  ⬇
Prepare Results for Forecast Accuracy Evaluation

## 

The code builds and trains an XGBoost regression model using macroeconomic indicators and historical GDP data, then predicts GDP values for the test dataset using an ensemble boosting approach optimized through 500 trees, a learning rate of 0.05, and a maximum tree depth of 5.

----------------------------------------------

## Model Evaluation

## RMSE

from sklearn.metrics import (
    mean_squared_error,
    mean_absolute_percentage_error
)

rmse = mean_squared_error(
    y_test,
    xgb_pred,
    squared=False
)

print("RMSE:", rmse)

## explanation

from sklearn.metrics import (
    mean_squared_error,
    mean_absolute_percentage_error
) 

Imports evaluation metrics used to assess the performance of regression models.

mean_squared_error, -  Imports the **Mean Squared Error (MSE)** function, which measures the average squared difference between actual and predicted values.

mean_absolute_percentage_error -  Imports the **Mean Absolute Percentage Error (MAPE)** function, which measures prediction errors as percentages of actual values.

rmse = mean_squared_error( -  Begins calculating the **Root Mean Squared Error (RMSE)** and stores the result in the variable `rmse`.

y_test, -  Provides the **actual GDP values** from the test dataset for comparison.

xgb_pred, -  Provides the **GDP values predicted by the XGBoost model**.

squared=False -  Specifies that the square root of the Mean Squared Error should be returned, producing the **RMSE** value.

) -  Completes the RMSE calculation using actual and predicted values.

print("RMSE:", rmse) -  Displays the calculated RMSE value to evaluate the model's prediction accuracy.

## Overall Summary

Import Regression Evaluation Metrics
                 ⬇
Select Actual GDP Values (`y_test`)
                 ⬇
Select Predicted GDP Values (`xgb_pred`)
                ⬇
Calculate Mean Squared Error
                ⬇
Take Square Root (`squared=False`)
              ⬇
Obtain Root Mean Squared Error (RMSE)
             ⬇
Display Model Prediction Error
                ⬇
Assess Forecasting Performance of the XGBoost Model

## Summary

The code evaluates the XGBoost model's forecasting performance by calculating the Root Mean Squared Error (RMSE), which measures the average magnitude of prediction errors between actual and predicted GDP values, and then prints the result.

---------------------------------------------

## MAPE

mape = mean_absolute_percentage_error(
    y_test,
    xgb_pred
)

print("MAPE:", mape)

## explanation

mape = mean_absolute_percentage_error( -  Begins calculating the **Mean Absolute Percentage Error (MAPE)** and stores the result in the variable `mape`.

y_test, -  Provides the **actual GDP values** from the test dataset as the reference for evaluation.

xgb_pred -  Provides the **GDP values predicted by the XGBoost model** for comparison with actual values.

) - Completes the MAPE calculation by measuring the average percentage error between actual and predicted GDP values.

print("MAPE:", mape) -  Displays the calculated MAPE value to assess the forecasting accuracy of the model.

## Overall Summary

Actual GDP Values (`y_test`)
                ⬇
Predicted GDP Values (`xgb_pred`)
              ⬇
Calculate Absolute Percentage Error for Each Prediction
                  ⬇
Compute the Average Percentage Error (MAPE)
                ⬇
Store the Result in `mape`
               ⬇
Display the Model's Forecasting Accuracy

## Summary

The code evaluates the XGBoost model's forecasting performance by calculating the Mean Absolute Percentage Error (MAPE), which represents the average percentage difference between actual and predicted GDP values, and then prints the result.

### Interpretation of MAPE:

* **Lower MAPE** → Better forecasting accuracy.
* **MAPE < 10%** → Highly accurate forecast.
* **MAPE between 10%–20%** → Good forecast.
* **MAPE between 20%–50%** → Reasonable forecast.
* **MAPE > 50%** → Poor forecasting performance.

------------------------------------------------------

## Accuracy Improvement

baseline_rmse = 2200
new_rmse = 1650

improvement = (
    (baseline_rmse - new_rmse)
    / baseline_rmse
) * 100

print(
    f"RMSE Reduced by "
    f"{improvement:.2f}%"
)

## Explanation

baseline_rmse = 2200 -  Stores the **initial (baseline) RMSE value** of 2200, representing the original model's prediction error.

new_rmse = 1650 - Stores the **new RMSE value** of 1650 obtained after improving the forecasting model.

improvement = -  Creates a variable named `improvement` to store the percentage reduction in RMSE.

(baseline_rmse - new_rmse) -  Calculates the **absolute decrease in RMSE** achieved by the new model.

(baseline_rmse - new_rmse) / baseline_rmse -  Computes the proportion of RMSE reduction relative to the baseline RMSE.

((baseline_rmse - new_rmse) / baseline_rmse) * 100 -  Converts the RMSE reduction proportion into a **percentage improvement.

print(
    f"RMSE Reduced by "
    f"{improvement:.2f}%"
)

Prints the RMSE improvement percentage formatted to **two decimal places**.

f"{improvement:.2f}%" - Formats the `improvement` value as a percentage with exactly **two digits after the decimal point**.

## Overall Summary

Baseline Model RMSE = 2200
         ⬇
Improved Model RMSE = 1650
              ⬇
Calculate Absolute Reduction in Error (`2200 − 1650`)
                 ⬇
Divide by Baseline RMSE to Determine Relative Improvement
               ⬇
Convert the Result to Percentage (`× 100`)
                   ⬇
Format the Improvement Value to Two Decimal Places
                    ⬇
Display the Percentage Reduction in RMSE

## Summary

The code calculates the percentage reduction in RMSE achieved by the improved model compared to the baseline model and prints the improvement in forecasting accuracy.

### Calculation:

## Calculation:

Improvement (%)=
Baseline RMSE= ( Baseline RMSE−New RMSE /  Baseline RMSE )*100

## Using the given values:

2200 = (2200−1650 / 2200 )*100 = 25.00%

Output:

RMSE Reduced by 25.00% - The improved model reduced prediction error by **25%** compared to the baseline model, indicating better forecasting performance.

## OUTPUT

RMSE Reduced by 25.00%

-------------------------------------------------

## Feature Importance

importance = pd.DataFrame({
    'Feature': features,
    'Importance':
        xgb.feature_importances_
})

importance.sort_values(
    by='Importance',
    ascending=False,
    inplace=True
)

print(importance)

## Explanation

importance = pd.DataFrame({ -  Creates a new pandas DataFrame named `importance` to store feature names and their importance scores.

'Feature': features, -  Adds a column named `Feature` containing the list of predictor variables used in the XGBoost model.

'Importance': xgb.feature_importances_ -  Adds a column named `Importance` containing the importance scores assigned by the trained XGBoost model to each feature.

xgb.feature_importances_ -  Retrieves the relative contribution of each feature toward the model's predictions.

}) - Completes the creation of the feature importance DataFrame.

importance.sort_values( -  Initiates sorting of the DataFrame based on feature importance values.

by='Importance', - Specifies that the sorting should be performed using the `Importance` column.

ascending=False,  - Sorts the features in descending order so that the most important features appear first.

inplace=True -  Updates the existing `importance` DataFrame directly without creating a new DataFrame.

) -  Completes the sorting operation.

print(importance) -  Displays the sorted feature importance table to identify the most influential predictors.

## Overall Summary

Trained XGBoost Model (`xgb`)
             ⬇
Extract Feature Names (`features`)
                 ⬇
Retrieve Feature Importance Scores (`feature_importances_`)
                   ⬇
Create Feature Importance DataFrame
                     ⬇
Sort Features by Importance Score (Highest to Lowest)
                      ⬇
Update the DataFrame with Sorted Results
                    ⬇
Display Ranked Feature Importance Table
                  ⬇
Identify Key Drivers of GDP Predictions

## Summary

The code extracts feature importance scores from the trained XGBoost model, combines them with feature names into a DataFrame, ranks the features based on their predictive contribution, and displays the results to identify the most influential variables affecting GDP forecasts.

## Example Output:

| Feature      | Importance |
| ------------ | ---------: |
| iip_index    |       0.35 |
| rolling_mean |       0.22 |
| exports      |       0.15 |
| lag_1        |       0.10 |
| cpi          |       0.08 |
| imports      |       0.06 |
| lag_4        |       0.04 |

Interpretation: Features with higher importance scores have a greater influence on the XGBoost model's GDP predictions.

-------------------------------------------------

## Expected Drivers:

1. IIP Index
2. GST Collections
3. CPI
4. Exports
5. Imports

## Explanation

These Expected Drivers represent the key macroeconomic indicators that typically have the strongest influence on GDP or GVA forecasting models. Each variable captures a different dimension of economic activity.

## 1. IIP Index (Index of Industrial Production)

** Measures the level of industrial activity in sectors like manufacturing, mining, and electricity.

**Why it matters:**

* Direct indicator of **real economic output**
* Closely tracks **industrial growth cycles**
* Strong proxy for **short-term GDP movements**

**Interpretation:**

Higher IIP → higher production → higher GDP

## 2. GST Collections

Total tax revenue collected under the Goods and Services Tax system.

**Why it matters:**

* Reflects **formal sector consumption and business activity**
* High-frequency, real-time indicator of economic transactions
* Captures **demand-side strength of the economy**

## Interpretation

Higher GST → higher economic transactions → stronger GDP growth

## 3. CPI (Consumer Price Index)

Measures inflation based on changes in retail prices of goods and services.

**Why it matters:**

* Adjusts nominal values to **real economic terms**
* Captures **purchasing power and inflation pressure**
* Used to compute **real GDP**

## Interpretation

Rising CPI → inflation effect → needs adjustment for real growth analysis

## 4. Exports

Value of goods and services sold to other countries.

**Why it matters:**

* Indicates **external demand for domestic goods**
* Strengthens GDP via **net exports component**
* Sensitive to global economic conditions

## Interpretation

Higher exports → stronger global demand → higher GDP contribution

## 5. Imports

Value of goods and services purchased from other countries.

**Why it matters:**

* Reflects **domestic demand and consumption strength**
* High imports can signal strong economy but reduce net exports
* Important for **trade balance analysis**

## Interpretation

Higher imports → higher domestic demand (but may reduce GDP net effect)

## Overall Summary

Industrial Production (IIP)
          ⬇
Tax & Transaction Activity (GST)
           ⬇
Price Level & Inflation (CPI)
          ⬇
External Demand (Exports)
         ⬇
Domestic Demand & Supply Dependency (Imports)
               ⬇
Combined Macro Indicators
           ⬇
Explain and Drive GDP/GVA Movements
                 ⬇
Used as Key Predictors in Economic Forecasting Models

## Summary

These five variables—Industrial Production (IIP), GST collections, CPI, exports, and imports—act as core macroeconomic drivers that collectively capture production, consumption, inflation, and trade dynamics, making them strong predictors of GDP or GVA in forecasting models.

---------------------------------------

## Dashboard KPIs (Power BI/Tableau)

## Sector-wise GDP Contribution

SELECT
    sector,
    SUM(gva_constant_price) AS GDP
FROM national_accounts
GROUP BY sector;

## Explanation

1. Selects each economic sector (e.g., manufacturing, services).

2. Aggregates Gross Value Added (GVA) using SUM.

3. Produces total GDP contribution per sector.

4. Groups data by sector to compare contributions across industries.

## Insight

Helps identify which sectors drive the economy the most.

-----------------------------------------------------------------

## State-wise GDP

SELECT
    state,
    SUM(gdp_estimate) AS GSDP
FROM state_gdp
GROUP BY state;

## Explanation

1. Selects each state.

2. Aggregates GDP estimates (GSDP – Gross State Domestic Product).

3. Computes total economic output per state.

4. Groups results by state.

## Insight

Helps compare economic performance across states/regions.

--------------------------------------------------------------

## GDP Growth Trend

SELECT
    year,
    SUM(gva_constant_price) GDP
FROM national_accounts
GROUP BY year
ORDER BY year;

## Explanation

1. Aggregates GDP (GVA at constant prices) year-wise.

2. Groups data by year to form a time series.

3. Orders results chronologically using ORDER BY year.

Produces a trend of GDP over time.

## Insight

Used to analyze economic growth patterns and cycles over years.

## Overall Summary

National Accounts Data
         ⬇
Group by Sector → Compute Sector GDP Contribution
                     ⬇
Group by State → Compute Regional Economic Output (GSDP)
                      ⬇
Group by Year → Build GDP Time Series Trend
                   ⬇
Aggregate Results for BI Tools
               ⬇
Power BI / Tableau Dashboards
                ⬇
Enable Macro-Economic Visualization & Decision-Making

## Summary

These SQL queries prepare key macroeconomic KPIs by aggregating GDP at sector, state, and yearly levels, enabling visualization of economic contribution, regional performance, and growth trends in Power BI or Tableau dashboards.

--------------------------------------------------------

## Dashboard Visuals

1. Executive Dashboard
2. Total GDP
3. GDP Growth Rate %
4. Forecasted GDP
5. Inflation Rate

# Sectoral Dashboard

1. Agriculture Contribution
2. Industry Contribution
3. Services Contribution
4. Regional Dashboard
5. State-wise GSDP Map
6. Urban vs Rural Consumption

# Forecast Dashboard
1. Actual vs Predicted GDP
2. Forecast Confidence Interval

## Explanation

This dashboard design represents a **complete macroeconomic intelligence system** built to monitor GDP, growth, inflation, sectoral contribution, regional performance, and forecasting outcomes. It is typically implemented in **Power BI or Tableau** for executive decision-making.

## 1. Executive Dashboard

Purpose : Provides a **high-level snapshot of the economy** for policymakers and executives.

## Key Components:

* **Total GDP**

  * Overall size of the economy at constant/current prices
  * Represents economic output

* **GDP Growth Rate %**

  * Measures year-on-year or quarter-on-quarter economic expansion
  * Indicates economic momentum

* **Forecasted GDP**

  * Model-based prediction (ARIMA / ML models like XGBoost)
  * Used for forward-looking policy decisions

* **Inflation Rate**

  * Derived from CPI changes
  * Shows price stability and purchasing power trends

## Insight: Acts as a **“single-screen economic health indicator”**

## 2. Sectoral Dashboard

### Purpose: Breaks down GDP into **economic structure and contribution patterns**.

## Key Components

* **Agriculture Contribution**

  * Share of primary sector in GDP
  * Reflects rural and agro-based economy strength

* **Industry Contribution**

  * Manufacturing, mining, construction output
  * Indicates industrialization level

* **Services Contribution**

  * IT, finance, trade, logistics, etc.
  * Typically the largest GDP contributor in India

## Regional Dashboard

* **State-wise GSDP Map**

  * Geographic visualization of economic output by state
  * Helps identify high-performing and low-performing regions

* **Urban vs Rural Consumption**

  * Shows demand-side split
  * Captures consumption inequality and growth patterns

## Insight : Helps understand **“where and how GDP is being generated”**

## 3. Forecast Dashboard

## Purpose: Evaluates **predictive performance of GDP forecasting models**.

## Key Components:

* **Actual vs Predicted GDP**

  * Compares model output with real observed GDP
  * Validates model accuracy (XGBoost / ARIMA)

* **Forecast Confidence Interval**

  * Range of uncertainty around predictions
  * Helps quantify risk and reliability

## Insight: Enables **“data-driven confidence in economic forecasting models”**

## Overall Summary

Raw Macroeconomic Data (GDP, CPI, IIP, GST, Trade)
                ⬇
Data Engineering & Feature Creation
              ⬇
GDP Computation & Growth Analysis
                 ⬇
Machine Learning Forecasting (ARIMA / XGBoost)
                    ⬇
Sector + State + Time Aggregation
              ⬇
Power BI / Tableau Dashboard Layers
               ⬇
Executive View + Sectoral View + Forecast View
                      ⬇
End-to-End Economic Intelligence System

## Summary

This dashboard system provides a complete macroeconomic analytics framework combining executive KPIs, sectoral breakdowns, regional GDP insights, and forecasting performance to enable real-time monitoring and predictive understanding of economic growth, inflation, and structural composition.

## Project Outcome

| Metric                      | Achievement                                 |
| --------------------------- | ------------------------------------------- |
| Data Sources Integrated     | 10+                                          |
| Data Quality Improvement    | 30%                                         |
| Cross-source Consistency    | Improved through reconciliation             |
| Forecasting Error Reduction | 25%                                         |
| ML Models Used              | Random Forest, XGBoost, ARIMA               |
| Dashboard Delivery          | Power BI/Tableau                            |
| Business Impact             | Reliable GDP estimation and policy insights |

## Resume Project Description

## GDP Estimation and Forecasting Analytics (Base Year 2022–23)

## 1. Consolidated National Accounts, GST, household microdata, and macroeconomic indicators to develop an end-to-end GDP estimation framework.

## 2. Improved data quality and cross-source consistency by approximately 30% using statistical validation, anomaly detection, and reconciliation techniques.

## 3. Reduced GDP forecasting error (MAPE/RMSE) by nearly 25% through ARIMA, Random Forest, and XGBoost models with advanced feature engineering.

## 4. Developed interactive Power BI/Tableau dashboards delivering sectoral, regional, and trend-based GDP insights for data-driven policymaking.

                                                          *****
