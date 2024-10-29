# 📙 Supplier Quality

### Project Overview
Currently there is no procurement system in place and there is no way for the company to validate which suppliers are providing quality goods and which are not. There is also no consistency between different plants and the vendors the company is purchasing from. The need to centralize and understand supplier quality is top priority. This project delved into unravelling questions asked by the management team and also brought to light more insights about our data.

### Data Sources
Supplier Data: The main data used for this project is the "Supplier data" thats shows information about the company's suppliers, items supplied and other relevant informations. This is a flat table as we are aware, I need to create seperate dimension tables to make it easier to manage and analyze our datasets. 

### Tools
 - Excel - Data Exploration
 - PowerQuery - Data validation, Creation of Dimension table and primary key while also removing duplicates.
 - PowerBI - Data interpretation, Analysis and Visualization

### Data Cleaning/Preparation
 In the data cleaning phase, various steps were taken to clean our data which include;
 1. Data normalisation - The data was splitted into various segments(dimensions and fact) to help create a data modelling for better analysis.<img width="960" alt="Dimension" src="https://github.com/user-attachments/assets/ad8fa318-e1d0-4f05-9953-45fe3661acf0"><img width="952" alt="Modelling" src="https://github.com/user-attachments/assets/f751b1bc-00f8-4213-913e-462db9900bc6">


 2. Unique Keys - Primary key were created for the dimension tables so we can identify each supply and also connect to our transaction table.
 3. Data duplication removal  - Duplicates were truncated. 

### Exploratory Data Analysis
 - Which vendors/plants are causing the greatest defect quantity?<img width="616" alt="TopVendordefect" src="https://github.com/user-attachments/assets/eab549e5-84b9-43bf-8816-3e4f686c2ecd">
    - Avamm has the highest defect count of 29 followed by Izio and Roombo both with 28 counts. However, Yombu lead the list of suppliers with highest defects quantity of 15.1M. Avamm and Meejo tagged along with 14.7 and 14.2 respectively.
<img width="620" alt="planttopdefects" src="https://github.com/user-attachments/assets/3d94aae5-7483-4df3-a1e7-ffc9e386dd96">
 

 - Which vendors/plants are causing the greatest downtime?
   - Avamm caused us the most downtime hours of 1165 alongside Izio(1144) and Meetz (1134). 
 - Is there a particular combination of material and vendor that perform    poorly?
 - Is there a particular combination of Vendor and plant that performs    poorly?<img width="606" alt="vendorplantcombination" src="https://github.com/user-attachments/assets/534830a9-d1f1-4308-9204-2e71b1bf4c2a">

 - How does the same vendor and material perform across different plants?

### Data Analysis

First, I created a new date table in PowerBI to capture the least and maximum date period in my data.
```PowerBI
  Date = ADDCOLUMNS(
                    CALENDARAUTO(),
                    "Year", YEAR([Date]),
                    "Month", MONTH([Date]),
                    "Qtr", "Q" & QUARTER([Date]),
                    "Month Name", FORMAT([Date], "mmmm"),
                    "Month name short", FORMAT([Date], "mmm"),
                    "Day", DAY([Date]),
                    "Day of the week number", WEEKDAY([Date]),
                    "Day of the week ", FORMAT([Date], "dddd"),
                    "Week of the year", WEEKNUM([Date]),
                    "Is Weekend?", IF(WEEKDAY([Date]) IN {1,7},"True","False")

)
```
Base measures were also created to here in writing other Dax measures. I created a segment to know the vendors that supplied materials which caused high downtime
```PowerBI
Risk category = 
        var curven = SELECTEDVALUE(dim_vendors[Vendor])
        var totdown = CALCULATE([Total Downtime Hrs], FILTER(dim_vendors, dim_vendors[Vendor] = curven))
        RETURN
            IF(ISINSCOPE(dim_vendors[Vendor]),IF([Total Downtime Hrs] > 800, "High risk", IF([Total Downtime Hrs] <= 800 && [Total Downtime Hrs] >= 400, "Medium risk", "Low risk")))
```
In other to create context in my report, I created measures to calculate values for the same period previous month and year. For the previous month measure, here is the Dax code;
```PowerBI
Total defects LM = 
        CALCULATE([Total Defects Quantity],PARALLELPERIOD('Date'[Date],-1,MONTH))
```
Alternatively for previous year;
```PowerBI
Total Defects LY = 
    CALCULATE(
        [Total Defects Quantity], 
        SAMEPERIODLASTYEAR('Date'[Date]
    ))
```
It is interesting to note also that the MOM and LY growth icon is dynamic to reflect if our value is increasing or decreasing. The dax measure used is;
```PowerBI
DEFECT TREND = 
            VAR positive = "⮝"
            VAR negative = "⮟"
            VAR MOM = [Defects growth MOM]
            VAR YOY = [Defects growth YOY]
            VAR MM = IF(MOM > 0, positive, negative)
            VAR YY = IF(YOY > 0, positive, negative)
            RETURN
            MM & " " & FORMAT(MOM, "0.00%") & " vs LM" & " | " & YY & " "& FORMAT(YOY, "0.00%") & " vs LY"
```
For the ranking of vendors, I created a numeric parameter to give users some form of control. The user can decide how many poor performing vendors they want to see (TopN). For our chart to show the number of input by the user, I wrote this dax measure;
```PowerBI
Top vendor defect qty = 
            var topven = 
                IF(ISINSCOPE(dim_vendors[Vendor]), RANKX(ALL(dim_vendors[Vendor]), [Total Defects Quantity], ,DESC))
            var Botven = 
                IF(ISINSCOPE(dim_vendors[Vendor]), RANKX(ALL(dim_vendors[Vendor]), [Total Defects Quantity], ,ASC))
            VAR ranking = 
                IF(SELECTEDVALUE(TopBottom[Value]) = "Top", topven, Botven)
            RETURN
                IF(ranking <= 'Top N prm'[Top N prm Value], [Total Defects Quantity] )
```

### Results/Finding
1. Defect quantity keep rising compared to last year. In 2018 total Defect quantity was 1.16bn which cost £1.93M as against 1.44bn defects which cost £2.38M in 2019.

2. No impact has 39.86%, Impact has 31.71% while Rejected has 28.43% of the Defects in our data.

3. Meejo (14,206,469), Avamm (14,719,726) and Yombu (15,136,374) are the worst performing vendors with highest defect quantity.

4. Twin Rocks(96,903,184), Charles City (99,390,908) and Hingham (100,174,656) are our poorest performing plants.

5. Raw materials has the highest defect quantity (495.9m) with Corrugate having 308.7M and Film 135.1M.

### Recommendations
 Base on the analysis, the following is recommended;
 1. Top 3 Worst performing vendors could be replaced with new ones
 2. Vendors supplying Plants with least defect quantity should be used for the plants with highest defect quantity.
 3. 
 
