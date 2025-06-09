# Sales-Revenue-Analysis
This project focuses on analyzing customer sales data to distinguish between direct and indirect revenue streams.  
The goal is to provide clear, actionable insights through visualizations that highlight customer behavior and revenue trends.
-	Direct Revenue refers to income generated from purchases of Product Groups A, B, or C. Customers who buy from these groups are classified as Preferred Customers.
-	Indirect Revenue is derived from purchases of other product groups made by these Preferred Customers.

## Data Sources:
The dataset consists of 1 spreadsheet. 
-	“Technical pbi.csv”
## Tools
  -	Power Query – ETL (data sorting, data transformation, data cleansing, data validation)
  -	Power Pivot - ERD diagram (table joins/relationships for data modeling)
  -	PowerBI - Data Visualization (dashboard)
  -	DAX - Data aggregation and Data Analysis

## Data Preparation
- After examining and extracting the data from the “Technical PBI.xlsx” file, which contains one excel file.                                   
 •	I imported the data to Power BI and created dimensional tables, where I noticed quantity column and revenue column had special characters and null values.                                  
 •	There were values like country code,custmerID that contained null values which I then eliminated null vaues from key columns like customerID.
 •	I then perfomed data cleaning and transformation.                                                                                                                                      
                                                                                                                             

## Exploratory Data Analysis
  1.	A detailed breakdown of total, direct, and indirect revenue. 
  2.	A monthly bar chart comparing revenue types over time. 
  3.	A cumulative revenue chart to visualize growth trends.
  4.	KPI cards summarizing key metrics such as total revenue, direct/indirect revenue, and the number of active Preferred Customers.

## Data Analysis
- Writing DAX functions in POWERBI to create measures for data analysis like creating new calculated column to clean quantity column and revenue column.                                                                       
- The below DAX function show how I cleaned the Revenue (NetSales) column.
  ```dax                         
    	Clean_NetSales = 
		  VAR CleanedValue = SUBSTITUTE(SUBSTITUTE('Fact Sales'[NetSalesinGC], "(", ""), ")", "")
		  RETURN VALUE(CleanedValue)
   ```                                               
- The below DAX function shows how I cleaned the Quantity column and removed characters and string text from a integer column.                            
  ```dax
    Clean_Quantity = 
		VAR CleanedValue = SUBSTITUTE(SUBSTITUTE(SUBSTITUTE(SUBSTITUTE(SUBSTITUTE(SUBSTITUTE(SUBSTITUTE(SUBSTITUTE
		(SUBSTITUTE(SUBSTITUTE(SUBSTITUTE(SUBSTITUTE(SUBSTITUTE(SUBSTITUTE(SUBSTITUTE(SUBSTITUTE(SUBSTITUTE
		(SUBSTITUTE('Fact Sales'[Quantity], "EA", ""),"BIN", ""),"BAG",""),"KG",""),"AU",""),"BOX",""),
		"CAN",""),"DR",""),"GAL",""),"H",""),"HA",""),"L",""),"IBC",""),"KG",""),"M",""),"M3",""),"PAC",""),"PAL","")
		RETURN VALUE(CleanedValue)
  ```
                                           
- The below DAX function shows how direct revenue was calculated.
   ```dax                      
      Direct_Revenue = 
		    CALCULATE(
			  SUMX('Fact Sales',
			  'Fact Sales'[Clean_NetSales] ),
			  'Fact Sales'[Product Group] IN {"Product Group A", "Product Group B", "Product Group C"},
		  TREATAS(
			  VALUES('Fact Sales'[Customer]),
			'Fact Sales'[Customer]
		)
	)
  ```                                     
- The below DAX function shows how indirect revenue was calculated.                         
  ```dax
    Indirect_Revenue = 
		  CALCULATE(
			  SUMX('Fact Sales',
			  'Fact Sales'[Clean_NetSales]),
			  NOT('Fact Sales'[Product Group] IN {"Product Group A", "Product Group B", "Product Group C"}),
			  'Fact Sales'[Customer] IN 
			CALCULATETABLE(
				VALUES('Fact Sales'[Customer]),
            'Fact Sales'[Product Group] IN {"Product Group A", "Product Group B", "Product Group C"}
		)
	)
	```
- The below DAX function shows how cumulative revenue was calculated.                         
  ```dax
    Cumulative_Revenue = 
		  CALCULATE(
			  SUM('Fact Sales'[Clean_NetSales]),
			  FILTER(
			  ALL('Fact Sales'),
				'Fact Sales'[Fiscal Period] <= MAX('Fact Sales'[Fiscal Period])
			)
		)
		
- The below DAX function shows how monthly sale revenue trend was calculated for over fiscal period.                         
  ```dax
    Monthly_Sales_Trend = 
		  VAR CurrentMonth = SELECTEDVALUE('Fact Sales'[MonthNumber])
		  VAR Previous_Month = 
			CALCULATE(
				SUM('Fact Sales'[Clean_NetSales]),
				'Fact Sales'[MonthNumber] = CurrentMonth - 1
			)
		VAR CurrentSales = [Total_Revenue]
		RETURN
		SWITCH(
			TRUE(),
			CurrentSales > Previous_Month, "▲", 
			CurrentSales < Previous_Month, "▼",  
			"▬"  
		)                                                                                   
  ```
## Findings/Results using PowerBI visuals:
### Refer to the dashboard screenshot for below answers.

- Looking at the KPI in the dashboard, this indicates that a relatively small portion of the total revenue is direct, 
  while a significantly larger portion is indirect—highlighting the importance of cross-selling to Preferred Customers.       
- South Africa (ZA) is the dominant market, contributing 46.76% of total revenue,
  whereas Brazil (BR) shows the highest revenue contribution across Product Groups A, B, and C, with each group generating over €5M.              
- The horizontal bar chart shows that certain product groups contribute more to indirect revenue than direct,
  reinforcing the value of Preferred Customers purchasing beyond the core product groups             
- The vertical bar chart reveals monthly fluctuations in revenue, with clear distinctions between direct, indirect, and total revenue.     
- The line graph of cumulative revenue shows a steady upward trend, indicating consistent revenue growth over time.  
- Product Groups C and D are likely top performers in terms of volume, which may correlate with revenue trends.
- Revenue is heavily driven by Product Groups C and D in the early fiscal periods. Later periods indicate a drop in sales this maybe
  caused by seasonal changes or marking periods.

## Further Analysis
-	Indirect revenue is a major driver, suggesting strong customer engagement beyond initial purchases.                   
-	Cumulative revenue growth suggests healthy business performance and retention among Preferred Customers.                           
-	ZA and US are key revenue drivers.   
