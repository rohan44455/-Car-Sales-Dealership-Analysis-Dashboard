# 🚗 Car Sales & Dealership Analysis Dashboard

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=Power%20BI&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-0078D4?style=for-the-badge&logo=microsoft&logoColor=white)
![Data Analytics](https://img.shields.io/badge/Data%20Analytics-FF6F00?style=for-the-badge&logo=databricks&logoColor=white)

A comprehensive **3-page Power BI dashboard** analyzing car sales performance, customer behavior, dealership metrics, geography, pricing, and salesperson effectiveness across India — built on a dataset of **200 car sales transactions**.

---

## 📊 Dashboard Pages

### Page 1 — Car Sales & Dealership Analysis
![Car Sales & Dealership Analysis](Car%20Sales%20%26%20Dealership%20Analysis.png)

Key KPIs and visuals:
- **Repeat Buyer Rate:** 54%
- **Avg Ex-Showroom Price:** ₹2.00M
- **Avg Discount Given:** ₹41.15K
- **Total Revenue:** ₹401M
- **Avg Customer Rating:** 3.38 / 5
- **Total Cars Sold:** 200
- **Test Drive Conversion Rate:** 50.50%
- **Avg Waiting Days:** 24.13

Visuals include Revenue by Segment (donut chart), Sales by Brand (bar chart), and Sales Trend by Month broken down by Fuel Type.

---

### Page 2 — Customer & Product Analysis
![Customer & Product Analysis](Customer%20%26%20Product%20Analysis.png)

Explores:
- Gender split across vehicle segments (Hatchback, SUV, Sedan, Budget, Mid-Range, MUV, Luxury)
- Transmission preference (AMT, Manual, Automatic, CVT)
- Average discount by segment (MUV highest, Mid-Range lowest)
- Fuel type distribution: Petrol 25%, Hybrid 22.5%, Diesel 21%, CNG 18%, Electric 13.5%

Filters: Finance Type, Gender, Segment

---

### Page 3 — Geography, Pricing & Performance
![Geography, Pricing & Performance](Geography%2C%20Pricing%20%26%20Performance.png)

Covers:
- Sales by City (map visual across Indian states)
- Finance Type Split: EMI 27.5%, Bank Loan 28%, Cash 26%, In-House 11%, Leasing 7.5%
- Avg Price by Brand (Hyundai highest at ~₹55M cumulative, Toyota lowest)
- Color Popularity: White > Silver > Black > Red > Blue > Grey
- Salesperson Performance (SP101 leads with ~15 sales)

Filters: Transmission, City, Salesperson

---

## 📁 Dataset Overview

**File:** `CarSales_Data_Raw.csv`
**Rows:** 200 | **Columns:** 22

| Column | Description |
|---|---|
| `Sale_ID` | Unique identifier for each sale (e.g., CAR30000) |
| `Dealership_City` | State/city where the dealership is located |
| `Brand` | Car manufacturer (Hyundai, Volkswagen, Mahindra, etc.) |
| `Model` | Specific car model |
| `Fuel_Type` | CNG / Diesel / Electric / Hybrid / Petrol |
| `Transmission` | AMT / Manual / Automatic / CVT |
| `Color` | Exterior color of the vehicle |
| `Segment` | Market segment (Budget, Hatchback, Luxury, Mid-Range, MUV, Sedan, SUV) |
| `Customer_Age` | Age of the buyer |
| `Gender` | Male / Female |
| `Ex_Showroom_Price` | Price before taxes and registration |
| `Discount_INR` | Discount amount offered (INR) |
| `Finance_Type` | Bank Loan / Cash / EMI / In-House Finance / Leasing |
| `EMI_Months` | Duration of EMI in months |
| `Test_Drive_Taken` | Whether a test drive was taken (Yes/No) |
| `Trade_In_Vehicle` | Whether the customer traded in a vehicle |
| `Customer_Rating` | Rating given by customer (1–5) |
| `Repeat_Buyer` | Whether the customer is a returning buyer |
| `Sale_Date` | Date of transaction |
| `Salesperson_ID` | ID of the salesperson (SP101–SP120) |
| `Accessories_Added` | Whether accessories were added |
| `Waiting_Days` | Days between booking and delivery |

---

## 🧮 DAX Measures

All custom measures used in the dashboard are documented below.

### KPI Measures

```dax
-- Total Revenue
Total Revenue =
SUMX(
    CarSales,
    VALUE(SUBSTITUTE(CarSales[Ex_Showroom_Price], ",", ""))
)

-- Total Cars Sold
Total Cars Sold =
COUNTROWS(CarSales)

-- Avg Ex-Showroom Price
Avg Ex-Showroom Price =
AVERAGEX(
    CarSales,
    VALUE(SUBSTITUTE(CarSales[Ex_Showroom_Price], ",", ""))
)

-- Avg Discount Given
Avg Discount Given =
AVERAGEX(
    CarSales,
    VALUE(SUBSTITUTE(CarSales[Discount_INR], ",", ""))
)

-- Avg Customer Rating
Avg Customer Rating =
AVERAGE(CarSales[Customer_Rating])

-- Repeat Buyer Rate %
Repeat Buyer Rate % =
DIVIDE(
    COUNTROWS(FILTER(CarSales, CarSales[Repeat_Buyer] = "Yes")),
    COUNTROWS(CarSales),
    0
) * 100

-- Test Drive Conversion Rate %
Test Drive Conversion Rate % =
DIVIDE(
    COUNTROWS(FILTER(CarSales, CarSales[Test_Drive_Taken] = "Yes")),
    COUNTROWS(CarSales),
    0
) * 100

-- Avg Waiting Days
Avg Waiting Days =
AVERAGEX(
    CarSales,
    VALUE(SUBSTITUTE(CarSales[Waiting_Days], " days", ""))
)
```

---

### Revenue & Segment Measures

```dax
-- Revenue by Segment
Revenue by Segment =
CALCULATE(
    [Total Revenue],
    ALLEXCEPT(CarSales, CarSales[Segment])
)

-- Segment Revenue %
Segment Revenue % =
DIVIDE(
    [Revenue by Segment],
    CALCULATE([Total Revenue], ALL(CarSales[Segment])),
    0
) * 100

-- Avg Discount by Segment
Avg Discount by Segment =
CALCULATE(
    [Avg Discount Given],
    ALLEXCEPT(CarSales, CarSales[Segment])
)
```

---

### Brand & Pricing Measures

```dax
-- Sales by Brand
Sales by Brand =
CALCULATE(
    COUNTROWS(CarSales),
    ALLEXCEPT(CarSales, CarSales[Brand])
)

-- Avg Price by Brand
Avg Price by Brand =
CALCULATE(
    [Avg Ex-Showroom Price],
    ALLEXCEPT(CarSales, CarSales[Brand])
)

-- Total Price by Brand (for bar chart)
Total Price by Brand =
CALCULATE(
    [Total Revenue],
    ALLEXCEPT(CarSales, CarSales[Brand])
)
```

---

### Customer & Demographic Measures

```dax
-- Female Buyer Count
Female Count =
CALCULATE(
    COUNTROWS(CarSales),
    CarSales[Gender] = "Female"
)

-- Male Buyer Count
Male Count =
CALCULATE(
    COUNTROWS(CarSales),
    CarSales[Gender] = "Male"
)

-- Gender Split % (Female)
Female % =
DIVIDE([Female Count], [Total Cars Sold], 0) * 100

-- Gender Split % (Male)
Male % =
DIVIDE([Male Count], [Total Cars Sold], 0) * 100

-- Avg Customer Age
Avg Customer Age =
AVERAGE(CarSales[Customer_Age])
```

---

### Fuel & Transmission Measures

```dax
-- Sales by Fuel Type
Sales by Fuel Type =
CALCULATE(
    COUNTROWS(CarSales),
    ALLEXCEPT(CarSales, CarSales[Fuel_Type])
)

-- Fuel Type %
Fuel Type % =
DIVIDE([Sales by Fuel Type], [Total Cars Sold], 0) * 100

-- Sales by Transmission
Sales by Transmission =
CALCULATE(
    COUNTROWS(CarSales),
    ALLEXCEPT(CarSales, CarSales[Transmission])
)
```

---

### Finance Measures

```dax
-- Sales by Finance Type
Sales by Finance Type =
CALCULATE(
    COUNTROWS(CarSales),
    ALLEXCEPT(CarSales, CarSales[Finance_Type])
)

-- Finance Type %
Finance Type % =
DIVIDE([Sales by Finance Type], [Total Cars Sold], 0) * 100

-- Avg EMI Duration (Months)
Avg EMI Months =
CALCULATE(
    AVERAGE(CarSales[EMI_Months]),
    CarSales[Finance_Type] = "EMI"
)
```

---

### Salesperson Performance Measures

```dax
-- Sales by Salesperson
Sales by Salesperson =
CALCULATE(
    COUNTROWS(CarSales),
    ALLEXCEPT(CarSales, CarSales[Salesperson_ID])
)

-- Top Salesperson
Top Salesperson =
TOPN(1, VALUES(CarSales[Salesperson_ID]), [Sales by Salesperson], DESC)

-- Salesperson Avg Rating
Salesperson Avg Rating =
CALCULATE(
    AVERAGE(CarSales[Customer_Rating]),
    ALLEXCEPT(CarSales, CarSales[Salesperson_ID])
)
```

---

### Time Intelligence Measures

```dax
-- Sales by Month
Sales by Month =
CALCULATE(
    COUNTROWS(CarSales),
    ALLEXCEPT(CarSales, 'Date'[Month])
)

-- Month Number (for sorting)
Month Number =
MONTH(CarSales[Sale_Date])

-- YTD Revenue
YTD Revenue =
TOTALYTD([Total Revenue], 'Date'[Date])

-- MoM Sales Growth %
MoM Growth % =
VAR CurrentMonth = [Total Cars Sold]
VAR PreviousMonth =
    CALCULATE(
        [Total Cars Sold],
        DATEADD('Date'[Date], -1, MONTH)
    )
RETURN
DIVIDE(CurrentMonth - PreviousMonth, PreviousMonth, 0) * 100
```

---

### Color & Geography Measures

```dax
-- Sales by Color
Sales by Color =
CALCULATE(
    COUNTROWS(CarSales),
    ALLEXCEPT(CarSales, CarSales[Color])
)

-- Color Popularity Rank
Color Rank =
RANKX(
    ALL(CarSales[Color]),
    [Sales by Color],
    ,
    DESC,
    DENSE
)

-- Sales by City
Sales by City =
CALCULATE(
    COUNTROWS(CarSales),
    ALLEXCEPT(CarSales, CarSales[Dealership_City])
)
```

---

## 🛠️ Tools & Tech Stack

- **Power BI Desktop** — Dashboard development
- **DAX (Data Analysis Expressions)** — Custom measures and KPIs
- **Power Query (M Language)** — Data cleaning and transformation
- **Microsoft Bing Maps** — Geography visual (Page 3)

---
## 📂 Repository Structure

```
car-sales-dashboard/
│
├── data/
│   └── CarSales_Data_Raw.csv          # Raw dataset (200 records, 22 columns)
│
├── screenshots/
│   ├── Car_Sales___Dealership_Analysis.png
│   ├── Customer___Product_Analysis.png
│   └── Geography__Pricing___Performance.png
│
├── CarSalesDashboard.pbix             # Power BI report file
└── README.md                          # This file
```

---

## 📌 Key Insights

- **Hatchback and Sedan** are the top revenue-generating segments, together contributing over 42% of total revenue.
- **Hyundai** leads in total sales volume across all brands.
- **54% repeat buyer rate** suggests strong brand/dealer loyalty.
- **Petrol and Hybrid** vehicles together account for ~47.5% of units sold.
- **AMT transmission** is the most preferred (highest count), though Automatic and Manual are close.
- **White** is the most popular exterior color, followed by Silver and Black.
- **SP101** is the top-performing salesperson with approximately 15 sales.
- A **50.5% test drive conversion rate** indicates strong in-person sales effectiveness.

---
## 🤝 Connect With Me

<div align="center">

If you found this project useful or interesting, feel free to reach out or collaborate!

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/rphan6151)
[![Email](https://img.shields.io/badge/Email-Contact-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:kumarrohan71116@gmail.com)

---
