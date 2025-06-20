# Automotive-Sales-Analytics

![image](https://github.com/user-attachments/assets/9b6175b1-feb7-43c4-b8b1-02304456b766)

## Watch a video demo [here](https://drive.google.com/file/d/1QBYXfnFoi6blxJIJ-H-wpLiky0m8Tm1d/view?usp=drive_link)
---

## Table of Content
- [1. Project Overview and Objectives](#1-project-overview-and-objectives)
- [2. Tools used](#2-tools-used)
- [3. Methodology and Process](#3-methodology-and-process)
  - [3.1. Data Collection/Data Source](#31-data-collectiondata-source)
  - [3.2. Data Preparation/Cleaning](#32-data-preparationcleaning)
  - [3.3. Data Modelling and Transfomation](#33-data-modelling-and-transfomation)
  - [Exploratory Data Analysis](#34-exploratory-data-analysis)
  - [3.5. Results/Insights](#35-resultsinsights)
  - [3.6. Data Visualization](#36-data-visualization)
- [4. Recommendations](#4-recommendations)

---


## 1. Project Overview and Objectives
### 1.1. Overview
This project aims to provide insights on automotive sales performance of particular dealerships and regions in the United Sates in the year 2022 and 2023. With the analysis of different aspects of the automotive data, I seek to identify trends and patterns in revenue and customer preferences, and provide data informed recommendations for customers and potential new dealers.

### 1.2. Project Objectives
To provide guidance in the project, I set the following objectives:
- Analyze revenue and sales trends across dealerships, brands, and regions to identify growth opportunities.
   - What is the total revenue generated over the two years?
   - What is the total revenue generated each month?
   - What is the sales performance of each dealer?...
- Understand customer demographics (income, gender), segmentations and preferences (body style, transmission type).
  - What is the distribution of customers by gender?
  - What is the average annual income of customers?
  - Can customers be segmented based on their annual income?
  - Are there any differences in purchasing behavior between male and female customers?
  - What are the preferences of high-income customers compared to low-income customers?...
- Evaluate seasonal trends to optimize inventory and marketing strategies.
  - Are there any seasonal trends in car sales?
  - How has the average price of cars changed over time?
  - Are there any significant changes in sales patterns over the dataset period?
- Compare performance of car brands (domestic vs. foreign) and top models.
- Identify top-performing dealerships and regions to guide expansion or resource allocation.
  - Which dealer sold the most cars and generated the highest revenue?

## 2. Tools used
- Microsoft Excel: Data Inspection
- Power Query (Power BI): Data Cleaning
- Power BI: For Exploratory Data Analysis, Data Modelling and Visualization

## 3. Methodology and Process
### 3.1. Data Collection/Data Source
The data used in this project is the `Car Sales.xlsx - car_data.csv` file which covers automotive car sales in the United States from 1st January 2022 to the
30th December 2023. The data was made available on Kaggle by [Vasu Avasthi](https://www.kaggle.com/missionjee) and it can be accessed [here](https://www.kaggle.com/datasets/missionjee/car-sales-report).

### 3.2. Data Preparation/Cleaning
In the prepation and cleaning phase, I performed the following tasks:
- Data Inspection: I loaded the data in Microsoft Excel to have an overview of the data. After that, I then loaded the data in Power BI for further inspection.
- Date Dimension Table creation: After inspecting the data, I realized that to properly perform time/date analysis, I had to create a date dimesion table where I could split the date into more elements/components. After creating the table, I then proceeded to split/break the date into the following components for time analysis purposes:
   - Year
   - Month
   - Day of the week
   - Start of week
   - Start of month
   - Start of year,...

I used the following M code to create a date dimension table:
```M Code
= List.Dates(
Source,
Number.From(#date(2023, 12, 31))- Number.From(Source),
#duration(1,0,0,0)
)
```

With the source here being `2022/01/01` as it was the start temporal coverage start date of the data.

- Data formating: Here I renamed the sales table, renamed some columns and changed the data types of some columns.

### 3.3. Data Modelling and Transfomation
#### 3.3.1. Data Modelling
After creating the date dimension table, I then created a relationship between the *Date table* and the *Car Sales* table through the `Date` column in each of the two tables.

<img width="386" alt="Data Modelling" src="https://github.com/user-attachments/assets/2f1140cb-0bfa-4d84-8294-fb28e6188e43" />

#### 3.3.2. Data Transformation
Here I created some calculated measures and calculated columns to help me in my analysis. To keep things clean, I decided to create a table for all measures which I named *Measure Table*. All the DAX calculated measures can be accessed [here](https://github.com/PacifiqueNteta/Automotive-Sales-Analytics/blob/main/DAX%20Measures) and the DAX calculated columns [here](https://github.com/PacifiqueNteta/Automotive-Sales-Analytics/blob/main/DAX%20Calculated%20Columns)

**Here some measures I used:**
```DAX code
Average Car Price = 
DIVIDE(
    SUM('Car Sales'[Price]),
    DISTINCTCOUNT('Car Sales'[Car_id])
)
```

```
Total Revenue = SUM('Car Sales'[Price])  
```
```
Previous Month Sales = 
CALCULATE(
    [Total Sales(Cars Sold)],
    DATEADD(
        'Date Table'[Date],
        -1,
        MONTH
    )
)
```
```
Average Revenue Per Customer = 
DIVIDE(
    [Total Revenue],
    [Total Customers]
)
```

**Some calcuted columns generated:**
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
```
Dealer_State = 
SWITCH(
    'Car Sales'[Dealer_Region],
    "Aurora", "Colorado",
    "Austin", "Texas",
    "Greenville", "South Carolina",
    "Janesville", "Wisconsin",
    "Middletown", "Ohio",
    "Pasco", "Washington",
    "Scottsdale", "Arizona",
    "Unknown"
)
```
### 3.4. Exploratory Data Analysis

#### 3.4.1. Customer Identifier issue
After initial exploratory analysis of the data I noticed that it was missing a unique identifier for customers; the only identifier in the table was the column `Customer Name` which only had customers first and since some customers had the same first name, this was creating an aggregation issue. This can be seen in the table below where one customer (Thomas) appeared to have bought 90 cars in a space of 2 years; this didn't seem realistic. 

<img width="250" alt="image" src="https://github.com/user-attachments/assets/db612e60-5912-40b7-97b0-5a4acda6f351" />

To solve this I created a unique customer identifier using:
- First 3 + last 2 letters of the customer name
- The customer Name length
- The customer Gender initial
- And the customer Annual income
I named this column `CustomerID`.

After this workaround, the numbers pulled seemed more realistic although they can still be questionned. The customers who purchased most cars now appeared to have only purchased 17 cars instead of 90; and now we have more details on him as we can see how much he earns annually.

<img width="250" alt="image" src="https://github.com/user-attachments/assets/12582415-68c1-4d94-892b-e5f52dbf4173" />

### 3.4.2. Some Data Exploratory Insights
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

### 3.5. Results/Insights
Here are the key insights I found in my analysis
#### 3.5.1. Revenue & Sales Growth
- Total Revenue: $672M over 2 years, with 23.3% growth from 2022 ($300M) to 2023 ($370M).
- Total Cars Sold: 23,906 (10,645 in 2022 → 13,226 in 2023; 24.2% growth).
- Customer Growth: 20,657 total customers (9,630 in 2022 → 11,890 in 2023), averaging $32.5K revenue per customer.
#### 3.5.2. Top Performers
- Dealerships:
 - Progressive Shippers Cooperative Association (1,318 cars, $36.7M) and Rabun Used Car Sales (1,313 cars, $37.4M) led in sales.
 - Progressive Shippers had 26% revenue growth ($16.2M → $20.4M).
- Regions: Austin ($117M) and Janesville ($106M) outperformed.
- Brands:
 - Top 5 Sold: Chevrolet (1,819), Ford (1,674), Dodge (1,761), Volkswagen (1,333), Mercedes-Benz (1,285).
 - Top 5 Revenue: Chevrolet ($47.6M), Ford ($47.2M), Dodge ($44.1M), Oldsmobile ($35.4M), Mercedes-Benz ($34.6M).
 - USA brands dominated: 52% of revenue ($352M), followed by Japanese ($172M) and German ($107M).
- Models:
 - Lexus LS400: Highest revenue ($14.3M, 354 cars).
 - Mitsubishi Diamante: Most sold (418 cars, $9.3M).
#### 3.5.3. Customer & Vehicle Preferences
- Demographics:
 - 78% of revenue ($524M) came from high-income earners.
 - Male customers contributed 78.5% of revenue ($527M).
- Vehicle Features:
 - Body Style: SUVs ($170.6M) and hatchbacks ($166.2M) led revenue.
 - Transmission: 52.6% of sales were automatic (12,571 cars).
#### 3.5.4. Seasonal Trends
- Peaks:
 - May: 1,895 cars sold ($48.8M).
 - September: 3,305 cars ($93.6M).
 - December: 3,511 cars ($98.3M).
- Lowest Avg. Price: March ($27,170).

### 3.6. Data Visualization
After the explorarory data analysis, I developed dashboards to present properly the insights I got.

I grouped the charts into 5 dashboards/pages: Summary, Customer Details, Dealers Details, Car Spec Details and Map. The summary page provides an overview of the insights, the Customer Details has the named indicates provides more details on customer insights, the Dealers Details more details on the dealers, the Car Spec page provides insights related to car features and the Map page provide geolocalization insights.

#### 3.6.1. Summary Page

![image](https://github.com/user-attachments/assets/9b6175b1-feb7-43c4-b8b1-02304456b766)

On the Summary page, I added more visualizations on trends on the Revenue trends chart. To acces these viz, you just have to click on the info button as shown below 
![image](https://github.com/user-attachments/assets/1daef977-a498-4b16-bb73-21c261e2b2b5)

And when you click, you get the dashboard below:

![image](https://github.com/user-attachments/assets/39aa16f1-755f-49b3-b0ce-dbe7fcb00de5)


#### 3.6.2. Customer Details Page

![image](https://github.com/user-attachments/assets/ffac1c8a-aa31-4f40-82e7-a61e27e91ea8)

#### 3.6.3. Dealers Details Page

![image](https://github.com/user-attachments/assets/42474ab8-fd35-4bce-ac33-85ab081a20b9)

#### 3.6.4. Car Spec Page

![image](https://github.com/user-attachments/assets/d6e7d3a4-f967-426f-a4f1-ef4e312c9299)

#### 3.6.5.Map Page

![image](https://github.com/user-attachments/assets/3e04341a-e4d7-473b-b729-8b3fdab033e5)

The report containing all the pages can be accessed [here](https://app.powerbi.com/view?r=eyJrIjoiZTA5NGQ0MzctNGFlOC00ZmRiLWJiMDYtYWRlNTBmZTVjM2E4IiwidCI6ImNhOWE4YjhjLTNlYTMtNDc5OS1hNDNlLTU1MTAzOThlN2EzYiIsImMiOjh9&pageName=96c58c348a5581de78ec)

## 4. Recommendations
### 4.1. For Dealerships/New Entrants:
1.	Expand in High-Growth Regions: Prioritize Austin and Janesville for new dealerships or marketing spend.
2.	Leverage Top Brands/Models: Stock more Chevrolet, Ford, and Dodge vehicles, and promote high-revenue models like Lexus LS400.
3.	Target High-Income & Male Buyers: Tailor ads for SUVs/hatchbacks (e.g., luxury features for high-income males).
4.	Seasonal Promotions: Boost inventory before peaks (May, September, December) and offer discounts in slower months (March).
### 4.2. For Customers:
•	Budget Buyers: Explore Buddy Storbeck’s Diesel Service Inc (avg. price $27,217; cheapest car at $900).
•	Luxury Seekers: Consider Lexus LS400 (high revenue per unit) or German brands (premium appeal).
### 4.3. For Manufacturers:
•	USA Brands: Ramp up production of SUVs/hatchbacks (high demand).
•	Foreign Brands: Compete with US brands on other aspects such as innovation to gain more markets.







