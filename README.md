# 🚗 Automotive Sales Performance & Customer Behavior Analysis (2022–2023)

![image](https://github.com/user-attachments/assets/9b6175b1-feb7-43c4-b8b1-02304456b766)

[▶️ Watch Demo](https://drive.google.com/file/d/1QBYXfnFoi6blxJIJ-H-wpLiky0m8Tm1d/view?usp=drive_link)

[📊 Live Dashboard](https://app.powerbi.com/view?r=eyJrIjoiZTA5NGQ0MzctNGFlOC00ZmRiLWJiMDYtYWRlNTBmZTVjM2E4IiwidCI6ImNhOWE4YjhjLTNlYTMtNDc5OS1hNDNlLTU1MTAzOThlN2EzYiIsImMiOjh9)

---

## Table of Content
- [1. Project Overview & Objectives](#1-project-overview-and-objectives)
- [2. Tools used](#2-tools-used)
- [3. Data Source & Context](#3-data-source-and-context)
- [4. Methodology](#4-methodology)
  - [4.1. Data Preparation/Cleaning](#41-data-preparationcleaning)
  - [4.2. Data Modeling](#42-data-modeling)
  - [4.3. Data Transfomation](#43-data-transformation)
  - [4.4. Exploratory Data Analysis](#44-exploratory-data-analysis)
- [5. Key Insights](#5-key-insights)
- [6. Data Visualization](#6-data-visualization)
- [7. Recommendations](#7-recommendations)
- [8. Limitations & Lessons Learned](#8-limitations-and-lessons-learned)

---


## 1. Project Overview and Objectives
### 1.1. Overview
This project analyzes U.S. automotive sales data from **January 2022 to December 2023**, aiming to uncover trends in revenue, customer behavior, dealership performance, and seasonal patterns.

### 1.2. 🎯 Project Objectives
- Identify top-performing dealerships, brands, and regions for strategic expansion.
- Segment customers by income and gender to understand purchasing behavior.
- Evaluate monthly and yearly sales trends to support inventory and marketing planning.
- Compare domestic vs. foreign brand performance.
- Deliver actionable insights for dealerships, customers, and manufacturers

## 2. Tools used
| Tool | Purpose |
|------|--------|
| **Power BI** | Data modeling, DAX calculations, visualization |
| **Power Query** | Data cleaning, transformation, date table creation |
| **Excel** | Initial data inspection and validation |
| **DAX & M** | Calculated columns, measures, and dimension tables |

## 3. Data Source and Context

- **Source**: [Kaggle – Car Sales Report](https://www.kaggle.com/datasets/missionjee/car-sales-report) by Vasu Avasthi
- **Dataset**: `car_data.csv` – 23,906 car sales across the U.S.
- **Temporal Coverage**: January 1, 2022 – December 30, 2023
- **Key Fields**: Price, Customer Name, Annual Income, Gender, Dealer Region, Brand, Model, Body Style, Transmission, Date

> ⚠️ **Note**: This dataset may not represent the full U.S. market. It includes select dealerships and lacks full customer identifiers (e.g., unique customer ID), which impacts individual-level analysis.

---
## 4. Methodology

### 4.1. Data Preparation/Cleaning
- **Inspection**: Loaded data into Excel and Power BI to assess structure, completeness, and quality.
- **Cleaning**:
  - Renamed and standardized column names.
  - Fixed data types (e.g., Date → Date/Time).
  - Removed trailing spaces in text fields (e.g., `"Audi "` → `"Audi"`).
- **Date Dimension Table**:
  - Created using M code in Power Query for robust time intelligence:

  ```M Code(powerquery)
  = List.Dates(
     Source,
     Number.From(#date(2023, 12, 31))- Number.From(Source),
     #duration(1,0,0,0)
  )
  ```
With the source here being `2022/01/01` as it was the start temporal coverage start date of the data.

- Expanded into: Year, Month, Day of Week, Start of Month, etc.


### 4.2. Data Modeling 

- Built a **star schema** with:
  - **Fact Table**: `Car Sales` (transactions)
  - **Dimension Table**: `Date Table`
- Created a **one-to-many relationship** between `Date Table[Date]` and `Car Sales[Date]`.



<img width="386" alt="Data Modelling" src="https://github.com/user-attachments/assets/2f1140cb-0bfa-4d84-8294-fb28e6188e43" />

### 4.3. Data Transformation

Here I created some calculated measures and calculated columns to help me in my analysis. To keep things clean, I decided to create a table for all measures which I named *Measure Table*. All the DAX calculated measures can be accessed [here](https://github.com/PacifiqueNteta/Automotive-Sales-Analytics/blob/main/DAX%20Measures) and the DAX calculated columns [here](https://github.com/PacifiqueNteta/Automotive-Sales-Analytics/blob/main/DAX%20Calculated%20Columns)

#### 🔹 Calculated Columns
| Column | Logic |
|-------|------|
| `Car Country of origin` | Mapped brand to country (e.g., Toyota → Japan) |
| `Income Level` | Segmented customers:<br>– Lower: < $35K<br>– Middle: $35K–$100K<br>– Upper-Middle: $100K–$200K<br>– High: > $200K |
| `Dealer_State` | Mapped region to state (e.g., Austin → Texas) |

Calculated column Eg.:
```
Car Country of origin = 
SWITCH(
  'Car Sales'[Company],
  "Acura","Japan",
  "Audi","Germany",
  "BMW","Germany",
  "Buick","USA",
  "Cadillac","USA",
  "Chevrolet","USA",
  "Chrysler","USA",
  "Dodge","USA",
  "Ford","USA",
  "Honda","Japan",
  "Hyundai","South Korea",
  "Infiniti","Japan",
  "Jaguar","United Kingdom",
  "Jeep","USA",
  "Lexus","Japan",
  "Lincoln","USA",
  "Mercedes-B","Germany",
  "Mercury","USA",
  "Mitsubishi","Japan",
  "Nissan","Japan",
  "Oldsmobile","USA",
  "Plymouth","USA",
  "Pontiac","USA",
  "Porsche","Germany",
  "Saab","Sweden",
  "Saturn","USA",
  "Subaru","Japan",
  "Toyota","Japan",
  "Volkswagen","Germany",
  "Volvo","Sweden",
  "Unknown"
)
```

#### 🔹 Key Measures (DAX)
All measures stored in a dedicated **Measure Table** for clarity.

```dax
Total Revenue = SUM('Car Sales'[Price])

Average Car Price = 
DIVIDE(
    SUM('Car Sales'[Price]),
    DISTINCTCOUNT('Car Sales'[Car_id])
)
```
```dax
Previous Month Sales = 
CALCULATE(
    [Total Sales(Cars Sold)],
    DATEADD('Date Table'[Date], -1, MONTH)
)
```
```dax
Average Revenue Per Customer = 
DIVIDE([Total Revenue], [Total Customers])
```


### 4.4. Exploratory Data Analysis

#### 4.4.1. 🛠️ Critical Issue: Missing Unique Customer ID
- **Problem**: Only Customer Name (first name only) available → duplicate names caused inflated customer-level metrics (e.g., one "Thomas" appeared to buy 90 cars).
- **Impact**: Risk of false aggregation and misleading insights.

<img width="250" alt="image" src="https://github.com/user-attachments/assets/db612e60-5912-40b7-97b0-5a4acda6f351" />

- ✅ Solution: Composite CustomerID
 Created a proxy identifier using:
  - First 3 + last 2 letters of name
  - Name length
  - Gender initial
  - Annual income
  - Example: Tho-ma-9-M-75000 for Thomas (9 letters, Male, $75K income) 

- **Result**: Reduced max purchases per "customer" from 90 → 17, a more plausible figure.

<img width="250" alt="image" src="https://github.com/user-attachments/assets/12582415-68c1-4d94-892b-e5f52dbf4173" />

>**⚠️⚠️ Limitation**: This is not a true unique ID. It assumes no two people share the same combination which may not hold.  


### 4.4.2. Some Data Exploratory Insights
After this I did some further exploratory analysis to try to answer the business questions and meet the objectives set at the beginning of the project.

Here are some insights found:

1. Total revenue by car brands:

   
![image](https://github.com/user-attachments/assets/834192b6-8310-4b1b-9052-160046f09ec9)

2. Sales Quantity by Gender

![image](https://github.com/user-attachments/assets/ef2fdba3-1167-40db-a1c1-2a10d9653b67)

3. Income Level group

Here based on the average salary in the USA, I created a customer segmentation on the annual salary.

I used the following DAX to create the segmentation:
```
Income Level = 
SWITCH(
    TRUE(),
     'Car Sales'[Annual Income] < 35000, "Lower Income",
    'Car Sales'[Annual Income] <= 100000, "Middle Income",
    'Car Sales'[Annual Income] <= 200000, "Upper-Middle",
    "High Income"
)
```

![image](https://github.com/user-attachments/assets/923ebdf1-27de-434c-aff1-b337cc280a2c)

## 5. Key Insights
Here are the key insights I found in my analysis
### 5.1. Revenue & Sales Growth
- Total Revenue: $672 million over 2 years
- YoY Growth: +23.3% ($300M in 2022 → $370M in 2023)
- Cars Sold: 23,906 (+24.2% YoY)
- Avg. Revenue per Customer: $32,500
### 5.2. Top Performers

| CATEGORY | LEADER | PERFORMANCE |
|-------|------|------|
| Dealerships(Revenue) | Rabun Used Car Sales | $37.4M |
| Dealerships(Units) | Progressive Shippers Cooperative Association | 1,318 cars |
| Regions(Revenue) | Austin, TX | $117M |
| Brands(Revenue and Units) | Chevrolet | $47.6M and 1,819 Cars |
| Models(Revenue) | Lexus LS400 | $14.3M |
| Models(Units) | Mitsubishi Diamante | 478 Cars |

- **U.S Brands Dominance**: 52% of total revenue ($352M), followed by Japanese ($172M) and German ($107M).


### 5.3. Customer Behavior
- **78% of revenue ($524M)** came from **high-income earners**($200K+).
- **78.5% of revenue ($527M)** are **male buyers**.
- **Top Body Styles** : SUVs ($170.6M) and hatchbacks ($166.2M) led revenue.
- **Transmission**: 52.6% of sales were automatic transmission cars (12,571 cars).

### 5.4. Seasonal Trends
- Peak Months :
 - May: 1,895 cars sold ($48.8M).
 - September: 3,305 cars ($93.6M).
 - December: 3,511 cars ($98.3M).
- Lowest Avg. Price: March ($27,170).

## 6. Data Visualization

Interactive Power BI dashboard with 5 dedicated pages:

|PAGE | PURPOSE |
| ----| ---- |
|Summary | High-level KPIs, revenue trends, growth metrics |
| Customer Details | Demographics, income levels, gender trends |
| Dealers Details | Performance by dealership and region |
| Car Spec Details |Brand, model, body style, transmission analysis |
|Map | Geographic revenue distribution |

**🔍 Interactive Features**
- Click info buttons to drill into trend details.
- Filter by year, region, brand, or income level.
- Hover for tooltips and exact values.

#### Summary Page

![image](https://github.com/user-attachments/assets/9b6175b1-feb7-43c4-b8b1-02304456b766)

On the Summary page, I added more visualizations on trends on the Revenue trends chart. To acces these viz, you just have to click on the info button as shown below 

![image](https://github.com/user-attachments/assets/1daef977-a498-4b16-bb73-21c261e2b2b5)

And when you click, you get the dashboard below:

![image](https://github.com/user-attachments/assets/39aa16f1-755f-49b3-b0ce-dbe7fcb00de5)


#### Customer Details Page

![image](https://github.com/user-attachments/assets/ffac1c8a-aa31-4f40-82e7-a61e27e91ea8)

#### Dealers Details Page

![image](https://github.com/user-attachments/assets/42474ab8-fd35-4bce-ac33-85ab081a20b9)

#### Car Spec Page

![image](https://github.com/user-attachments/assets/d6e7d3a4-f967-426f-a4f1-ef4e312c9299)

#### Map Page

![image](https://github.com/user-attachments/assets/3e04341a-e4d7-473b-b729-8b3fdab033e5)

 [📊 View Live Dashboard](https://app.powerbi.com/view?r=eyJrIjoiZTA5NGQ0MzctNGFlOC00ZmRiLWJiMDYtYWRlNTBmZTVjM2E4IiwidCI6ImNhOWE4YjhjLTNlYTMtNDc5OS1hNDNlLTU1MTAzOThlN2EzYiIsImMiOjh9)

## 7. Recommendations
### 7.1. 🏢 For Dealerships & New Entrants:
• **Expand in High-Growth Regions**: Prioritize Austin and Janesville for new dealerships or marketing spend.
• **Stock up Top Brands/Models**: Stock more Chevrolet, Ford, and Dodge vehicles, and promote high-revenue models like Lexus LS400.
• **Target High-Income Male Buyers**: Tailor ads for SUVs/hatchbacks (e.g., luxury features for high-income males).
• **Run Seasonal Promotions**: Boost inventory before peaks (May, September, December) and offer discounts in slower months (March).
### 7.2. 👤 For Customers:
•	Budget Buyers: Explore Buddy Storbeck’s Diesel Service Inc (avg. price $27,217; cheapest car at $900).
•	Luxury Seekers: Consider Lexus LS400 (high revenue per unit) or German brands (premium appeal).
### 4.3. 🏭 For Manufacturers:
•	USA Brands: Ramp up production of SUVs/hatchbacks (high demand).
•	Foreign Brands: Compete with US brands on other aspects such as innovation to gain more markets.

## 8. Limitations and Lessons Learned
### ⚠️ Key Limitations
- No True Customer ID: Composite `CustomerID` reduces but does not eliminate duplication risk.
- Data Representativeness: Dataset may not reflect national trends; limited to specific dealers.
- Short Time Span: Only 2 years of data; longer history would improve trend reliability.
- Self-Reported Income: Potential inaccuracies in income data.

### 💡 Lessons Learned
- Always validate **data uniqueness** early in the pipeline.
- *Time intelligence* is critical, always build a proper date table.
- *Transparency* about data limitations builds credibility.
- Even **imperfect data** can yield **valuable insights** when **handled responsibly**.





