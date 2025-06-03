# Automotive-Sales-Analytics
---
## 1. Project Overview and Objectives
### 1.1 Overview
This project aims to provide insights on automotive sales performance of particular dealerships and region in the United Sates in the year 2022 and 2023. With the analysis of different aspects of the automotive data, I seek to identify trends and patterns in revenue and customer preferences, and provide data informed recommendations for customers and potential new dealers.

### 1.1 Project Objectives
To provide guidance in the project, I set the following objectives:
- Analyze revenue and sales trends across dealerships, brands, and regions to identify growth opportunities.
- Understand customer demographics (income, gender) and preferences (body style, transmission type).
- Evaluate seasonal trends to optimize inventory and marketing strategies.
- Compare performance of car brands (domestic vs. foreign) and top models.
- Identify top-performing dealerships and regions to guide expansion or resource allocation.

## 2. Tools used
- Microsoft Excel: Data Inspection
- Power Query (Power BI): Data Cleaning
- Power BI: For Exploratory Data Analysis, Data Modelling and Visualization

## 3. Methodology and Process
### 3.1 Data Collection/Data Source
The data used in this project is the `Car Sales.xlsx - car_data.csv` file which covers automotive car sales in the United States from 1st January 2022 to the
30th December 2023. The data was made available on Kaggle by [Vasu Avasthi](https://www.kaggle.com/missionjee) and it can be accessed [here](https://www.kaggle.com/datasets/missionjee/car-sales-report).

### 3.2 Data Preparation/Cleaning
In the prepation and cleaning phase, I performed the following tasks:
- Data Inspection: I loaded the data in Microsoft Excel to have an overview of the data. After that, I then loaded the data IN Power BI for further inspection.
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

### 3.3 Data Modelling and Transfomation
#### 3.3.1 Data Modelling
After creating the date dimension table, I then created a relationship between the *Date table* and the *Car Sales* table through the `Date` column in each of the two tables.

<img width="386" alt="Data Modelling" src="https://github.com/user-attachments/assets/2f1140cb-0bfa-4d84-8294-fb28e6188e43" />

#### 3.3.2 Data Transformation
Here I created some calculated measures and calculated columns to help me in my analysis. To keep things clean, I decided to create a table for all measures which I named *Measure Table*. All the calculated measures and calculated columns I created for this project can be accessed [here]()

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
### 3.4 Exploratory Data Analysis

#### Customer Identifier issue
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



