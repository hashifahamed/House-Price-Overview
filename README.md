# 🏡 Danish Real Estate Market & Sales Performance Dashboard (Power BI)

## 📌 Project Purpose
This interactive Power BI dashboard provides a diagnostic and exploratory analysis of the Danish residential housing market. It tracks property valuation trends, transactional margins, sales velocity, and macroeconomic indicators (inflation, nominal interest rates, and bond yields). Designed for real estate portfolio managers, market appraisers, and investment analysts, the report delivers granular visibility into regional disparities, property-type pricing dynamics, and value-driving factors.

---

## 🛠️ Step-by-Step Implementation Workflow
1. **Data Ingestion & Power Query Transformation:**
   - Sourced raw transactional data containing pricing, property specifications, geographic attributes, and macroeconomic indicators.
   - Cleaned text schemas and standardized region names (`Zealand`, `Jutland`, `Fyn & islands`, `Bornholm`).
   - Converted numerical columns (`purchase_price`, `Offer Price`, `sqm`, `sqm_price`, rates) to appropriate currency/decimal data types.
   - Handled date hierarchies by configuring temporal relationships via the transaction `date` column.
2. **Data Modeling & Architecture:**
   - Isolated measures into a centralized container (`Measures Table 1`) to separate analytical DAX logic from base physical tables.
   - Leveraged native Power BI AI tooling (Key Influencers) to extract non-linear feature impacts on property values.
3. **UI/UX Design:**
   - Structured three dedicated analytical layers: High-level Market Overview, Operational Sales Performance, and Property Type Unit Economics.
   - Integrated slicers for real-time slicing by `AREA`, `CITY`, `SALES TYPE`, and `REGION`.

---

## 📐 Data Dictionary: Columns & Measures

### 1. Base Table Columns (`Housing`)
| Column Name | Data Type | Description |
| :--- | :--- | :--- |
| `house_id` | Integer / ID | Unique identifier for each real estate unit. |
| `date` | Date | Date of sale or closing transaction. |
| `address`, `city`, `area`, `region`, `zip_code` | Categorical | Geographic location attributes across Denmark. |
| `house_type` | Categorical | Classification of property: Farm, Villa, Townhouse, Apartment, Summerhouse. |
| `sales_type` | Categorical | Method of sale: `regular_sale`, `family_sale`, `auction`, `other_sale`. |
| `no_rooms` | Integer | Total number of rooms in the property. |
| `year_build` | Integer | Original construction year. |
| `Age` | Calculated/Int | Derived property age relative to modern/sale year. |
| `sqm` | Decimal / Int | Living area size in square meters. |
| `sqm_price` | Decimal | Price paid per square meter. |
| `Offer Price` | Currency | Listing or asking price set by seller/agent. |
| `purchase_price` | Currency | Final executed contract transaction price. |
| `%_change_between_offer_and_purchase` | Calculated | Percentage variance between asking and final price: $\frac{\text{Purchase Price} - \text{Offer Price}}{\text{Offer Price}}$. |
| `dk_ann_infl_rate%` | Decimal / % | Danish annual inflation rate at time of transaction. |
| `nom_interest_rate%` | Decimal / % | Nominal mortgage interest benchmark rate. |
| `yield_on_mortgage_credit_bonds%` | Decimal / % | Yield on mortgage credit bonds impacting financing costs. |

---

### 2. Analytical DAX Measures (`Measures Table 1`)
* **`Average Price SQM`**  
  $$\text{Average Price SQM} = \text{AVERAGE}(\text{Housing}[\text{sqm\_price}])$$  
  *Computes the benchmark square-meter cost across filtered geographies or segments.*

* **`Last 12 Month Sales`**  
  Calculates trailing 12-month (L12M) sales revenue using rolling calendar time-intelligence:
  $$\text{CALCULATE}(\text{SUM}(\text{Housing}[\text{purchase\_price}]), \text{DATESINPERIOD}(\text{Housing}[\text{date}], \text{MAX}(\text{Housing}[\text{date}]), -1, \text{YEAR}))$$

* **`Units Sold in Latest Year & Quarter`**  
  Evaluates transaction volume velocity for the most recent calendar quarter:
  $$\text{CALCULATE}(\text{COUNTROWS}(\text{Housing}), \text{LASTDATE}(\text{Housing}[\text{date}]))$$

* **`Total YTD Sales`**  
  Accumulates total purchase transaction value from the start of the current year up to the selected date:
  $$\text{TOTALYTD}(\text{SUM}(\text{Housing}[\text{purchase\_price}]), \text{Housing}[\text{date}])$$

* **`Median Sales Price Change`**  
  $$\text{DIVIDE}(\text{MEDIAN}(\text{Housing}[\text{purchase\_price}]) - \text{MEDIAN}(\text{Housing}[\text{Offer Price}]), \text{MEDIAN}(\text{Housing}[\text{Offer Price}]))$$  
  *Monitors median price growth or contraction across regions.*

* **`YOY_Sales_Growth`**  
  $$\text{DIVIDE}(\text{Total Sales} - \text{CALCULATE}(\text{Total Sales}, \text{SAMEPERIODLASTYEAR}(\text{Housing}[\text{date}])), \text{CALCULATE}(\text{Total Sales}, \text{SAMEPERIODLASTYEAR}(\text{Housing}[\text{date}])))$$  
  *Measures Year-over-Year revenue expansion/contraction across sales types.*

* **`Sales By Region`**  
  Aggregates total portfolio revenue distributed regionally for visual partitioning:
  $$\text{CALCULATE}(\text{SUM}(\text{Housing}[\text{purchase\_price}]), \text{ALLEXCEPT}(\text{Housing}, \text{Housing}[\text{region}]))$$

* **`Offer to SQM Ration`**  
  $$\text{DIVIDE}(\text{AVERAGE}(\text{Housing}[\text{Offer Price}]), \text{AVERAGE}(\text{Housing}[\text{sqm}]))$$  
  *Measures asking rate per square meter to assess listing premium behavior.*

---

## 📈 Visuals & Report Breakdown

### Page 1: House Market Overview
* **KPI Cards:** 
  * `Units Sold in Latest Year & Quarter` (77 units) tracking real-time sales liquidity.
  * `Last 12 Month Sales` (13bn DKK) tracking rolling annual revenue.
* **Median Sales Price Change by region (Diverging Bar Chart):** Compares regional appreciation versus contraction (e.g., positive trends in Jutland, Fyn & islands, and Zealand vs. contraction in Bornholm).
* **Offer Price Vs Purchase Price (Scatter Plot):** Assesses bid-ask spread and listing efficiency along a 45° trend line up to 40M DKK.
* **Year On Year Sales Growth By Sales Type (Area Chart):** Measures YoY change by channel—showing growth in auctions (+0.29) against negative contractions in regular, other, and family sales (-0.75).

### Page 2: Sales Performance
* **Sales By Region (Funnel / Flow Chart):** Visualizes capital concentration across regional markets (Zealand: 95bn, Jutland: 81bn, Fyn & islands: 15bn, Bornholm: minimal).
* **Key Influencers (Power BI AI Visual):** Diagnoses top drivers influencing increases in `purchase_price` (highlights how younger property age segments drive +501.1K increases).
* **Offer to SQM Ration by sales_type (Horizontal Bar Chart):** Tracks asking density across transaction channels (`regular_sale` leading at 15K/sqm down to `auction` at 11K/sqm).
* **Average Price SQM by region (Donut Chart):** Compares realized price per square meter across Jutland (26.42%), Fyn & islands (25.75%), Bornholm (23.97%), and Zealand (23.86%).
* **Temporal Transaction Ledger (Matrix/Table):** Tabulates granular closing dates, YTD sales progressions, and daily aggregate sums.

### Page 3: Property Type Economics & Macro Metrics
* **Global Slicers:** Top ribbon filters for `AREA`, `CITY`, `SALES TYPE`, and `REGION`.
* **Avg Offer / Purchase Price By House Type (Clustered Bar Chart):** Side-by-side spread comparison of asking price vs. transaction price across Farm, Apartment, Townhouse, Villa, and Summerhouse.
* **Avg Inflation/Interest/Yield by House Type (Multi-Bar Chart):** Maps market environment indices (Inflation, Nominal Interest, Bond Yield) against property classes.
* **Avg Sqm / Sqm_Price By House Type (Combination Bar & Stepped Line Chart):** Contrasts average physical living footprint (e.g., Farm properties peaking at 196.32 sqm) against price realization per square meter (Apartments peaking at 28.7K DKK/sqm).
