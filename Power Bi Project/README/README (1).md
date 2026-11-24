# Restaurants, Members & Orders Dashboard

A Power BI project that shows an operations manager which cities, restaurants, meals and members perform well, and where to act to grow the company.

## Objective

The operations manager needs one place to see revenue, orders, commission and lost orders, and to find weak areas quickly. The dashboard answers four questions:

- Which cities and restaurants earn the most, and which earn the least?
- Which restaurant types bring in the most commission?
- Which meals and meal types sell best?
- How many orders are lost because they contain no meals, and how do members spend?

**Intended user:** the operations manager, the person who can make changes in restaurants.

## Dataset

- **Source:** Kaggle, *Restaurants, Members & Orders* (CC0 public domain licence)
- **Size:** 10 CSV files, 50 columns in total
- **Reporting period:** January to June 2020
- **Key fields:** Order Date, Total Order, Restaurant ID, Member ID, Meal ID, Income Percentage, Monthly Budget
- **Main numbers:** 30 restaurants, 5 cities, 200 active members, 30,782 orders with meals
- **Data-sharing limits:** none. The dataset is CC0, so the raw and cleaned data can be shared.

## Repository Structure

```
├── BRD/            Business Requirements Document (BRD_Submitted.pdf)
├── FRD/            Functional Requirements Document (FRD_Submitted.pdf)
├── Data/
│   ├── raw/        Original Kaggle files, unchanged
│   ├── cleaned/    Cleaned tables (CSV) and Power_Query_Steps.xlsx
│   └── assessment/ Column_Assessment.xlsx
├── Visuals/        Dashboard mockup (editable .pptx and PDF export)
├── PowerBI/
│   ├── pbix/       dashboard.pbix
│   └── exports/    dashboard_export.pdf
├── Reports/        Analysis_Report.pdf (includes the data model documentation)
└── README.md
```

## Tools Used

- Power BI Desktop (report, data model, theme)
- Power Query (cleaning and transformations)
- DAX (measures and the Date table)
- Excel (column assessment and Power Query steps)

## Data Preparation

Every column of the 10 tables was assessed in `Column_Assessment.xlsx` and cleaned in Power Query. The steps are listed in `Power_Query_Steps.xlsx`, and the raw files were not changed. The main steps were:

- Set correct data types (for example Price and Monthly Budget as Fixed decimal number, Income Percentage as Percentage).
- Corrected spelling (Desert to Dessert) and trimmed and standardised text, including lowercase emails and Sex values.
- Renamed ID columns so each key is clear.
- Merged the small lookup tables (cities, restaurant types, meal types, serve types) into the dimension tables with Left Outer joins to build a star schema. Row counts were checked before and after the merges and did not change.
- Treated orders with a Total Order of 0 and no meals as empty orders, and used them only for the Empty Order Rate.
- Cleaned `monthly_member_totals` but did not load it into the model, because it could double count revenue.

## Data Model and Measures

The model is a star schema with 2 fact tables (`fact_orders`, `fact_order_details`) and 4 dimension tables (`dim_restaurants`, `dim_members`, `dim_meals` and a `dim_Date` table made with DAX), joined by 5 one-to-many relationships, all active. The cities, restaurant types, meal types and serve types tables were merged into the dimension tables in Power Query, so every dimension connects straight to a fact table. All relationships filter from the dimension to the fact.

| Measure | What it calculates |
| --- | --- |
| Total Revenue | Sum of Total Order |
| Number of Orders | Distinct orders that contain meals |
| Average Order Value | Total Revenue divided by Number of Orders |
| Meal Revenue | Price of each meal sold, added up |
| Commission Earned | Revenue multiplied by each restaurant's Income Percentage |
| Empty Order Rate | Share of orders with a Total Order of 0 |
| Active Members, Revenue per Member, Average Monthly Budget, Members by City | Member measures for the Member Analysis page |

The full model documentation, including the diagram, is in `Reports/Analysis_Report.pdf`.

## Dashboard Pages

1. **Overall Summary:** 5 KPI cards, revenue by month and revenue by city
2. **Restaurant Performance:** revenue by restaurant, commission by restaurant type and a details table
3. **Meal Analysis:** revenue by meal type, top 10 selling meals and a decomposition tree
4. **Member Analysis:** members by city, revenue by sex and revenue per member by monthly budget
5. **Recommendations:** insight, evidence and action for each area

Each page has slicers (Date, City, Meal Type, Restaurant Name), a Reset button and navigation buttons. A hidden tooltip page shows extra detail when you hover over a restaurant.

## Steps to Reproduce

1. Download the dataset from Kaggle (*Restaurants, Members & Orders*, CC0) or use the files in `Data/raw/`.
2. Read `Data/assessment/Column_Assessment.xlsx` and `Data/cleaned/Power_Query_Steps.xlsx`.
3. Open `PowerBI/pbix/dashboard.pbix` in Power BI Desktop.
4. If Power BI cannot find the data, open **Transform data**, then **Data source settings**, and point each source to your local folder.
5. Click **Refresh** and check the main numbers: Total Revenue ₹3.16M, Number of Orders 31K, Average Order Value ₹102.8, Commission Earned ₹242.83K and Empty Order Rate 14.5%.
6. Compare with `PowerBI/exports/dashboard_export.pdf` and `Reports/Analysis_Report.pdf`.

## Key Insights and Recommendations

- **Fast Food earns the most commission** (₹77K, against ₹28K for Italian). Add more Fast Food restaurants and promote the Italian ones.
- **Cheese is the top meal type** (39.54% of meal revenue, ₹1.25M, against ₹0.47M for Beef). Keep Cheese meals well stocked and run offers on Beef.
- **14.5% of orders have no meals**, and even the worst restaurants are only about 16%. The problem is spread across all restaurants, so review the ordering process everywhere and start with the highest.
- **Herzelia is the strongest city** (₹0.85M) and **Givatayim the weakest** (₹0.31M), even though Givatayim has 42 members. Check how many restaurants it has and promote them.
- **Members spend about the same (₹15.6K to ₹16K) at every budget level**, so do not target members by budget. Focus on cities such as Herzelia, which has the most members (48).

## Limitations

- The data covers only six months (January to June 2020), so yearly trends cannot be tested.
- `order_details` has no quantity column, so each row is counted as one meal. Revenue uses the Total Order amount, cross-checked against meal prices.
- City revenue uses the restaurant's city, while Members by City uses the member's home city.
- The data does not explain why patterns happen, so the recommendations suggest checks before acting.
- The dataset is static, so refresh is manual.
