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
<img width="620" alt="planttopdefects" src="https://github.com/user-attachments/assets/3d94aae5-7483-4df3-a1e7-ffc9e386dd96">

 - Which vendors/plants are causing the greatest downtime?
 - Is there a particular combination of material and vendor that perform    poorly?
 - Is there a particular combination of Vendor and plant that performs    poorly?<img width="606" alt="vendorplantcombination" src="https://github.com/user-attachments/assets/534830a9-d1f1-4308-9204-2e71b1bf4c2a">

 - How does the same vendor and material perform across different plants?

### Data Analysis

I created a new date table in PowerBI to capture all dates in my data.
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
I created a segment to know the vendors that supplied materials which caused high downtime
```PowerBI
Risk category = 
        var curven = SELECTEDVALUE(dim_vendors[Vendor])
        var totdown = CALCULATE([Total Downtime Hrs], FILTER(dim_vendors, dim_vendors[Vendor] = curven))
        RETURN
            IF(ISINSCOPE(dim_vendors[Vendor]),IF([Total Downtime Hrs] > 800, "High risk", IF([Total Downtime Hrs] <= 800 && [Total Downtime Hrs] >= 400, "Medium risk", "Low risk")))
```

### Results/Finding
