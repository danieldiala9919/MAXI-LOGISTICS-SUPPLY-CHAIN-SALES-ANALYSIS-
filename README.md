# **MAXI LOGISTICS: SUPPLY CHAIN SALES ANALYSIS**

*Shipping Reliability, Returns and Geographic Profitability Analysis*



**Prepared by:**  Danie Diala 

**Period analysed:** January 2014 to December 2017 

**Tools:** Excel, Power Query, DAX, Power BI 


---

## Table of Contents

1. [Project Background](#1-project-background)
   - [Problem Statement](#problem-statement)
   - [Objectives](#objectives)
   - [Focus Areas](#focus-areas)
2. [Data Structure and Initial Checks](#2-data-structure-and-initial-checks)
3. [Executive Summary](#3-executive-summary)
4. [Insights Deep Dive](#4-insights-deep-dive)
5. [Takeaway](#5-takeaway)
6. [Recommendations](#6-recommendations)
7. [Limitations and Assumptions](#7-limitations-and-assumptions)
8. [Skills Demonstrated](#8-skills-demonstrated)

---

## 1. Project Background

Maxi Logistics is the logistics subsidiary of a US e-commerce business. Its job is to take retail orders placed online and move them to customers across the United States under the promise of being **Faster, Safer and Further**. Between 2014 and 2017 the order book grew from 969 to 1,687 orders a year (up 74%), and the company now serves 793 customers across 49 states and four regions (West, East, Central and South) through four ship modes: Standard Class, Second Class, First Class and Same Day. Products fall into three categories (Furniture, Office Supplies and Technology) and 17 sub-categories.

As volume grew, leadership could see sales and profit but could not answer basic operational questions with confidence: how fast do orders really leave the warehouse, can the shipping data be trusted, where do returns come from, and which geographies and products earn money versus quietly lose it. This project is the analytical answer to those questions.

### Problem Statement

Growth was masking weaknesses in fulfilment. The order and ship dates in the source data were corrupted (day and month values swapped), roughly one in five orders carried a ship date that could not be trusted, returns were climbing and appeared to cluster in specific places, and several high-sales states and products were destroying profit. Without a clean, reliable view of order-to-ship performance, returns and geographic profitability, management could not set service targets, fix root causes or decide where to invest.

### Objectives

1. **Clean and standardise** the order and ship dates, and flag orders whose ship dates cannot be trusted so they do not distort lead-time metrics.
2. **Measure fulfilment speed** (order to ship) by ship mode, region and year, and test whether it is improving.
3. **Quantify the data exceptions** (unreliable ship dates) and show where they occur.
4. **Size and locate returns** by year, city, region and product sub-category.
5. **Profile customers and geographies** to separate where sales are earned from where profit is lost.
6. **Recommend actions** that Operations, Merchandising, Finance and Data teams can act on, each backed by evidence.

### Focus Areas

- **Shipping Performance:** speed, volume and sales by ship mode, and the lead-time trend.
- **Shipping Operations and Exceptions:** order demand by region, regional lead times and unreliable ship dates.
- **Returns Analysis:** return value, return rate, trend, cities and sub-categories.
- **Customers and Geography:** customer base, order value, state-level sales and profit, and product-level drill-through.


---

## 2. Data Structure and Initial Checks

The Power BI model is built from an Excel workbook with two tables, plus a measures table (`_Dax`) holding the DAX calculations used across the report. In total the model holds **9,994 order lines** and a **1,826-day calendar**.


### Key fields in the order table

| Field | Meaning |
|---|---|
| **Order ID / Row ID** | Order identifier (5,009 distinct) and unique line identifier (9,994). One order can contain several lines. |
| **Order Date / Ship Date** | Dates used to derive shipping processing days (order to ship). |
| **Ship Mode** | Standard Class, Second Class, First Class or Same Day. |
| **Customer ID / Segment** | 793 customers across Consumer (5,191 lines), Corporate (3,020) and Home Office (1,783). |
| **City / State / Region** | 531 cities, 49 states, 4 regions. |
| **Category / Sub-Category / Product** | 3 categories, 17 sub-categories, 1,862 product IDs. |
| **Returned** | Order-level return flag, coded "Yes" or "Not". |
| **Sales / Quantity / Discount / Profit** | Line-level financials. Discount ranges from 0% to 80%; 1,871 lines have negative profit. |

### Initial checks and what they revealed

- **Completeness:** no null values and no duplicate rows; Row ID is unique across all 9,994 lines.
- **Grain:** the data is at order-line level, while attributes such as ship date, ship mode and returned flag are order-level and repeated on every line of the same order. Order counts therefore use distinct Order ID rather than row counts.
- **Date corruption:** 3,013 lines (30%) show a raw ship date more than 10 days after the order date, which is impossible for these ship modes. The pattern is the signature of day and month values being swapped (for example, an order placed on 30 Dec 2017 showing a ship date of 1 May 2018 instead of 5 Jan 2018).
- **Impossible zero-day shipments:** 889 orders on Standard, Second or First Class have a ship date equal to the order date, which a non-same-day service cannot achieve. These were treated as unreliable.

> **Cleaning (Power Query):** swapped day and month values in the order and ship dates were corrected so that every order follows a valid sequence, and the 889 orders with unreliable ship dates were flagged and excluded from lead-time measures. All other KPIs (sales, profit, orders, returns) use the full dataset.


---

## 3. Executive Summary

<p align="center">
<img width="942" height="506" alt="Maxi 3" src="https://github.com/user-attachments/assets/ec0ddba3-73ef-46fe-94ff-62d3883d7596" />
 <br><em>Executive Overview dashboard</em>
</p>
Maxi Logistics grew sales from \$484K in 2014 to \$733K in 2017 and almost doubled profit (\$49.5K to \$93.4K), but fulfilment speed has not improved, one in five shipping records cannot be trusted, returns are rising and concentrated in the West, and five of the ten biggest-selling states lose money.

| KPI | Result || KPI | Result |
|---|---|---|---|---|
| Total Sales | \$2.30M || Total Orders | 5,009 |
| Total Profit | \$286.4K || Number of Customers | 793 |
| Profit Margin | 12.47% || Average Order Value | \$458.61 |
| Total Quantity Sold | 37,873 units || Orders per Customer | 6.32 |
| Average Shipping Lead Time | 3.81 days (4,120 valid shipments) || Orders Returned | 296 (5.91%) |

- **Speed is flat, and Standard Class carries the load.** The average order-to-ship time is 3.81 days and has barely moved since 2014 (3.87 days in 2014, 3.77 in 2016, 3.81 in 2017). Standard Class handles 59.8% of orders at 4.95 days.
- **A reliability blind spot.** 889 orders (17.75%) have ship dates that cannot be trusted, 73% of them on Standard Class, so lead-time metrics cover only 82.25% of orders.
- **Returns are rising and geographically concentrated.** The return rate climbed from 5.47% to 6.22% and the West generates 64% of returned orders with a return rate of 11.73% against about 3% elsewhere. Machines (11.61%) and Tables (9.77%) are the worst sub-categories.
- **Profit leaks in big markets.** Texas ranks third on sales yet loses \$25.7K; Texas, Ohio and Pennsylvania together lose \$58.3K. Furniture earns only a 2.5% margin against about 17% for Technology and Office Supplies.


---

## 4. Insights Deep Dive

The report contains five analytical pages that sit behind the Executive Overview. Each page below is explained with its objective, the dashboard view, and the insight behind every chart. All pages respond to Year and Region slicers (the Returns page uses the same two), and the figures shown are for all years and all regions.

### 4.1 Shipping Performance

**Objective:** measure how quickly and how consistently orders leave the warehouse, and whether speed differs by ship mode or has changed over time.

<p align="center">
  <img width="904" height="505" alt="dashboard_shipping_performance" src="https://github.com/user-attachments/assets/9dcec7f4-02b8-4edb-b1df-72bfa68eac33" />
  <br><em>Shipping Performance dashboard</em>
</p>


#### KPI cards: Total Orders, Valid Shipments, Valid Shipments % and Avg Shipping Processing Days
<p align="center">
<img width="175" height="398" alt="chart_ship_kpis" src="https://github.com/user-attachments/assets/c0dcf9d1-5635-4311-8e93-28ed1aa38923" />


- **5,009 orders were placed but only 4,120 (82.25%) have a trustworthy ship date.** The 3.81-day average processing time is therefore measured on four out of five orders, a limitation every lead-time figure in this report inherits.
- **3.81 days is the headline benchmark** for order-to-ship time across the business.


#### Orders by Ship Mode 
<p align="center">
<img width="344" height="197" alt="chart_ship_orders" src="https://github.com/user-attachments/assets/450ad585-1731-4880-a981-0d68ca0f65c0" />


- **Standard Class dominates with 2,994 orders (59.8%),** followed by Second Class (964, 19.2%), First Class (787, 15.7%) and Same Day (264, 5.3%).
- **Premium speed is niche:** First Class and Same Day together account for only 21% of orders, so most customers accept the slowest service tier.


#### Average Shipping Processing Days by Ship Mode
<p align="center">
<img width="344" height="197" alt="chart_ship_days" src="https://github.com/user-attachments/assets/0dc7284d-2265-42ab-adea-3c3321f03b9c" />

- **Speed follows the service tier as expected:** Standard Class 4.95 days, Second Class 3.19, First Class 2.14 and Same Day 0.04.
- **Standard Class is about 2.8 days slower than First Class** and 1.8 days slower than Second Class.
- **Lead times sit in tight, predictable windows.** Every valid Standard Class shipment took 4 to 7 days (28.5% took 6 to 7 days), Second Class 2 to 5 days, First Class 1 to 3 days (one order took 4) and Same Day 0 to 1 day. These windows are a ready-made basis for ship-mode service targets.
> **Note:** The Average Shipping Processing Days was rounded up and displayed as a whole numbers in the report charts for easy and clearer perception.



#### Sales by Ship Mode
<p align="center">
<img width="344" height="270" alt="chart_ship_sales" src="https://github.com/user-attachments/assets/302e5a38-c03b-4827-b050-0a06fc5841be" />


- **Standard Class generates 59.1% of sales (\$1.36M)**, Second Class 20.0% (\$459K), First Class 15.3% (\$351K) and Same Day 5.6% (\$128K). Each mode's share of sales almost mirrors its share of orders, so no mode is attracting unusually large or small baskets.
- **Standard Class earns 57% of total profit,** but First Class earns the best margin at 13.9% against 12.1% for Standard, while shipping about 2.8 days faster.

#### Average Shipping Processing Time Trend 
<p align="center">
<img width="454" height="194" alt="chart_ship_trend" src="https://github.com/user-attachments/assets/5709e98b-00e3-4420-99a0-e78f73fd42a7" />


- **No improvement in four years:** the average moved from 3.87 days (2014) to 3.81 (2015), 3.77 (2016) and 3.81 (2017), a swing of only 0.10 days.
- **Operations scaled without slowing down, but also without getting faster.** Yearly order volume grew 74% (969 to 1,687) over the same period while processing time held flat.

<sub>**Note:** the value axis starts at zero, so differences between years look small by design. Also, the Average Shipping Processing Time was rounded up and displayed as a whole numbers in the report charts for easy and clearer perception.</sub>

> **Why it matters:** speed is explained almost entirely by ship mode, and nothing in the process has improved. Service targets per mode are the missing control.


---

### 4.2 Shipping Operations and Exception Analysis

**Objective:** see where order demand sits, whether regions fulfil at different speeds, and where and how often shipping data fails validation.

<p align="center">
 <img width="1920" height="1080" alt="dashboard_shipping_operations" src="https://github.com/user-attachments/assets/57058496-74ca-4154-ac43-f187999c8c3e" />
  <br><em>Shipping Operations and Exception Analysis dashboard</em>
</p>



#### Orders by Region 
<p align="center">
<img width="510" height="230" alt="chart_ops_region" src="https://github.com/user-attachments/assets/15a270dd-1d7e-49fa-b042-3ededd9b2b0e" />


- **West leads with 1,611 orders (32.2%),** then East 1,401 (28.0%), Central 1,175 (23.5%) and South 822 (16.4%).
- **West and East carry 60% of all demand,** which is where capacity and stock planning should be weighted. South is the smallest market at about one in six orders.



#### Exception KPI cards: Unreliable Ship Date Orders and % Unreliable
<p align="center">
<img width="600" height="150" alt="chart_ops_kpis" src="https://github.com/user-attachments/assets/31f88e16-96c9-4a84-bac1-4b7e6e7d41f0" />


- **889 orders, or 17.75% of all orders, have unreliable ship dates.** These orders are shown as shipped on the same day they were ordered even though they used Standard, Second or First Class.



#### Shipping Exception Analysis
<p align="center">
<img width="510" height="230" alt="chart_ops_region" alt="chart_ops_exceptions" src="https://github.com/user-attachments/assets/1c30d105-ca3b-452d-b833-ade43cc22c2e" />


- **Standard Class holds 648 exceptions (73% of the total),** Second Class 160 and First Class 81. Same Day has none.
- **The slower the service, the worse the record:** exception rates are 21.6% of Standard Class orders, 16.6% of Second Class and 10.3% of First Class.
- **The problem is systemic, not local.** Exception rates are almost identical across regions (17.1% to 18.3%) and years (17.5% to 18.1%), which points to a process or system gap in how ship dates are captured, not to one warehouse or period.



#### Shipping Processing Time by Ship Mode and Region
<p align="center">
<img width="909" height="600" alt="chart_ops_table" src="https://github.com/user-attachments/assets/27d48d02-28ff-46ff-84b7-e3811d6aad0f" />

- **Ship mode, not geography, drives lead time.** Standard Class averages 4.95 to 4.97 days in every region, Second Class 3.13 to 3.26, First Class 2.06 to 2.20 and Same Day 0.00 to 0.08.
- **Regional averages are close:** West 3.76, East 3.80, South 3.83 and Central 3.89 days, a gap of just 0.13 days. There is no regional bottleneck to fix; improvement has to come from the process itself.

<sub>The table scrolls in the report; the first rows (Central and East) are shown here.</sub>

> **Why it matters:** performance is uniform across regions, but one in five shipping records is unusable, so the business cannot currently prove or manage its own service level.


---

### 4.3 Returns Analysis

**Objective:** size the cost of returns, see whether they are growing, and pinpoint the cities, regions and sub-categories where they concentrate.

<p align="center">
<img width="905" height="503" alt="dashboard_returns_analysis" src="https://github.com/user-attachments/assets/cfd4cd46-781b-4c9b-a793-a3fff65da203" />
  <br><em>Returns Analysis dashboard</em>
</p>



#### KPI cards: Total Sales Returns and % Order Returns
<p align="center">
<img width="949" height="215" alt="chart_ret_kpis" src="https://github.com/user-attachments/assets/13c49a89-9304-4df2-995e-46c8d8f5220c" />


- **296 of 5,009 orders were returned (5.91%),** carrying \$180.5K of sales, equal to 7.9% of total sales.
- **Returned orders are bigger than average:** \$610 per returned order against \$449 for orders that stayed, about 36% higher, which is why the sales impact (7.9%) exceeds the order rate (5.9%).



#### % Order Returns Trend 
<p align="center">
<img width="449" height="198" alt="chart_ret_trend" src="https://github.com/user-attachments/assets/1f79c29f-b27d-4dd5-a399-111a3b4b49b4" />

- **The return rate is drifting up:** 5.47% (2014), 5.88% (2015), 5.86% (2016) and 6.22% (2017).
- **Returned orders nearly doubled in volume** from 53 in 2014 to 105 in 2017, with 2017 the worst year, while orders grew 74%. The rate rose about 14% in relative terms (0.75 percentage points).



#### Order Returns by City 
<p align="center">
<img width="919" height="603" alt="chart_ret_city" src="https://github.com/user-attachments/assets/65a08dac-b7bc-4161-b113-98cd113d63db" />


- **Los Angeles leads with 50 returned orders,** 17% of all returns, followed by Seattle (25) and San Francisco (24). These three West Coast cities account for 99 returns, a third of the total.
- **Return rates in these cities are very high:** Los Angeles 13.0% and Seattle 11.8% of orders, roughly double the company average.
- **Filtering the Region slicer to West confirms the pattern:** the West produces 189 of the 296 returned orders (64%) from only 32% of orders, a return rate of 11.73% against 2.9% to 3.3% in every other region.

#### % Order Returns by Sub-Category (top 10)
<p align="center">
<img width="932" height="433" alt="chart_ret_subcategory" src="https://github.com/user-attachments/assets/ff722f55-0660-4bee-912c-c84484303e33" />


- **Machines (11.61%) and Tables (9.77%) are the worst,** roughly 2.0 and 1.7 times the company-wide rate of 5.91%.
- **The whole top 10 sits between 7.64% and 11.61%,** so return risk is broad across products, but the lowest sub-categories (Envelopes at 5.22%, Labels at 5.78%) show it is not unavoidable.
- **Tables and Machines are a double problem:** both combine high returns with weak profitability (Tables lose \$17.7K overall; Machines earn only \$3.4K on \$189.2K of sales).

> **Why it matters:** returns are not random. They are a regional and product-specific issue that is getting worse, so the root cause (quality, packaging, delivery or policy) can be investigated where it is concentrated.


---

### 4.4 Customers and Geography

**Objective:** understand the customer base and order behaviour, and show where sales are earned and where profit is lost across states.

<p align="center">
<img width="906" height="508" alt="dashboard_customers_geography" src="https://github.com/user-attachments/assets/69c1d63f-8615-4d0f-b1ed-7596ca1d2c0e" />
  <br><em>Customers and Geography dashboard</em>
</p>

####  No. of Customers, Avg Order Value and Order per Customer
<p align="center">
<img width="434" height="95" alt="chart_geo_kpis" src="https://github.com/user-attachments/assets/151d38ce-7ee7-4300-86b1-c399d728fd2f" />


- **793 customers placed 5,009 orders, or 6.32 orders each,** about 1.6 orders per customer per year. This is a repeat-purchase customer base, so retention matters as much as acquisition.
- **The average order is worth \$458.61,** which means the average customer spent roughly \$2,900 over the four years (6.32 orders at \$458.61).

#### Top 10 States by Sales 
<p align="center">
<img width="966" height="427" alt="chart_geo_top10" src="https://github.com/user-attachments/assets/49704fd5-e746-42c3-a6d8-f733b1e51986" />


- **California (\$457.7K), New York (\$310.9K) and Texas (\$170.2K) lead.** California and New York alone generate 33.5% of total sales.
- **The top 10 states account for 69.2% of sales,** so revenue is highly concentrated in a handful of markets.

#### State-Level Performance
<p align="center">
<img width="914" height="594" alt="chart_geo_table" src="https://github.com/user-attachments/assets/e738bef6-0dbb-49e7-898b-5f1960caf022" />


- **Sales rank does not equal profit rank.** Texas is third on sales but loses \$25.7K. Five of the ten biggest-selling states are loss-making (Texas, Pennsylvania, Florida, Illinois and Ohio), losing \$74.3K between them.
- **Ten states lose money in total (\$98.2K).** Texas (-\$25.7K), Ohio (-\$17.0K), Pennsylvania (-\$15.6K) and Illinois (-\$12.6K) are the largest leaks; Texas, Ohio and Pennsylvania alone lose \$58.3K.
- **Profitable growth exists:** California earns \$76.4K and New York \$74.0K on strong volumes, showing the model works when discounts and product mix are right.

<sub>The table scrolls in the report and is sorted alphabetically by state; the first rows are shown here.</sub>

#### Bottom 10 States by Sales 
<p align="center">
<img width="950" height="436" alt="chart_geo_bottom10" src="https://github.com/user-attachments/assets/706c32fb-f7a5-487a-bc74-f5e22c760109" />


- **North Dakota (\$0.9K), West Virginia (\$1.2K) and Maine (\$1.3K) are the weakest,** with South Dakota, Wyoming, District of Columbia, Kansas, Idaho, Iowa and New Mexico completing the list.
- **The ten weakest states together contribute only \$25.8K, 1.1% of sales.** These are thin markets, not a major lost opportunity, so effort is better spent fixing loss-making large states than chasing small ones.

> **Why it matters:** growth is concentrated in a few states, and some of the largest are unprofitable. Volume alone is not a success measure for these markets.


---

### 4.5 Product Details (drill-through page)

**Objective:** let an analyst trace any headline number back to the product, order status, ship mode and location behind it. This hidden page is reached by drilling through from Product Name, Category or Sub-Category. The view below is filtered to the Technology category.

<p align="center">
  <img width="1920" height="1080" alt="dashboard_product_details" src="https://github.com/user-attachments/assets/2b6293a8-4263-4164-82b4-183d3475f291" />
  <br><em>Product Details drill-through page (Technology)</em>
</p>

- **One row per product and order situation:** product ID and name, category, sub-category, total orders, quantity, total sales, **Order Status** (returned or not), ship mode, average shipping days, state and city.
- **It turns findings into investigations.** For example, drilling into the Machines sub-category (11.61% return rate) shows that three of its five best-selling products lose money: the Cubify CubeX 3D Printer loses \$8.9K on \$11.1K of sales at an average 53% discount, the Lexmark MX611dhe Printer loses \$4.6K on \$16.8K at 40%, and the Cisco TelePresence EX90 loses \$1.8K on \$22.6K at 50%.
- **Discounting is a visible driver.** Machines carry an average discount of 30.6% against 15.6% across the whole dataset, which links the profit problem to pricing decisions rather than demand.
- **Rows with a blank Avg Shipping Days** are orders whose ship dates were flagged as unreliable, so the exception logic carries through to product level.


---

## 5. Takeaway

The project set out to answer one question: **where is Maxi Logistics losing reliability, service quality and profit while its order volume grows?** The analysis shows that growth has outpaced operational control.

- **Fulfilment is consistent but not improving.** Orders ship in an average of 3.81 days, with speed set almost entirely by ship mode and almost no variation by region or year, even as order volume grew 74%.
- **Data reliability is the biggest operational risk.** After repairing swapped dates, 17.75% of orders still carry unusable ship dates, spread evenly across regions and years. This is a systemic capture problem, and until it is fixed the business cannot measure or promise delivery performance.
- **Returns are a growing, concentrated cost.** The rate rose to 6.22%, the West generates 64% of returned orders, and Machines and Tables are the weakest products. Returned orders are about 36% larger than average, so each one hurts more.
- **Revenue is concentrated and not always profitable.** Two states produce a third of sales, and five of the ten largest states lose money. Heavy discounting on Furniture and Machines pushes sales without earning margin.

> In short: the company ships on time by its own measures, but it cannot prove it, returns are growing in a predictable place, and some of its biggest sales are unprofitable. All of these are fixable with targeted action rather than broad change.

---

## 6. Recommendations

| # | Observation | Recommendation | Owner |
|---|---|---|---|
| 1 | 17.75% of orders (889) have unreliable ship dates, 73% on Standard Class, evenly spread by region and year. | **Enforce ship-date capture and validation at source:** one date format, ship date recorded at dispatch, and automatic rejection of impossible values. | Data / IT, Operations |
| 2 | Standard Class is 60% of orders at 4.95 days, and lead time has not improved since 2014. | **Set on-time targets per ship mode** (for example, 7 days Standard, 5 Second, 3 First, 1 Same Day, in line with the observed windows) and track on-time shipping weekly. The file has no promised delivery date, so the targets will be internally defined. | Operations |
| 3 | The West has 64% of returned orders and an 11.73% return rate against about 3% elsewhere; Los Angeles (50) and Seattle (25) lead. | **Find the root cause of West-region returns** (quality, packaging, delivery or returns policy) before changing anything, starting with Los Angeles and Seattle. | Customer Experience, Regional Operations |
| 4 | Machines have an 11.61% return rate and only \$3.4K profit on \$189.2K sales; Tables return at 9.77% and lose \$17.7K. | **Review quality, packaging and margin for Machines and Tables.** | Merchandising, Quality |
| 5 | Furniture earns a 2.5% margin against about 17% for Technology and Office Supplies; Tables lose \$17.7K and Bookcases \$3.5K. | **Rework Furniture pricing, costs and discounting,** starting with Tables and Bookcases. | Pricing, Finance |
| 6 | 1,393 order lines discounted 30% or more lost \$135.4K, while the other 8,601 lines earned \$421.8K. | **Cap deep discounts with an approval threshold above roughly 20%.** | Pricing, Finance |
| 7 | Texas, Ohio and Pennsylvania together lose \$58.3K; five of the ten biggest states are loss-making. | **Fix loss-making states** by reviewing discounts and product mix in Texas, Ohio and Pennsylvania first. | Regional Sales, Finance |
| 8 | First Class earns a 13.9% margin against 12.1% for Standard and ships about 2.8 days faster. | **Encourage First Class on orders where speed matters.** | Sales, Operations |
| 9 | Q4 holds 37.4% of annual orders, and West (1,611) and East (1,401) carry the volume. | **Plan inventory and staffing for the Q4 peak,** weighted towards the West and East. | Supply Planning |
| 10 | Technology and Office Supplies earn about 17% margin against 2.5% for Furniture. | **Grow Technology and Office Supplies** rather than relying on Furniture volume. | Merchandising, Sales |


---

## 7. Limitations and Assumptions

The dataset was not free of errors. The table below summarises each data quality issue, what was done about it, and what should change so it does not recur.

| Issue found | Mitigation | Recommendation |
|---|---|---|
| **Swapped day and month values** in order and ship dates (30% of lines had a ship date over 10 days after the order date, an impossible value). | Corrected the swapped values in Power Query so each order follows a valid date sequence, and checked the result against the expected lead-time window for each ship mode. | Store dates in a single unambiguous format (ISO, yyyy-mm-dd) in a true date field at source, and add a validation rule that ship date cannot be before order date. |
| **889 orders (17.75%) with unreliable ship dates:** Standard 648, Second 160, First 81, all showing ship date equal to order date. | Flagged the orders and excluded them from lead-time metrics only; 4,120 valid shipments (82.25%) remain. Sales, profit and returns still use all orders. | Record the ship date automatically at dispatch and block same-day ship dates for non-same-day modes. |
| **Order-line grain with order-level attributes repeated** (ship date, mode, returned flag on every line). | Counted orders with distinct Order ID and measured returns and shipping at order level to avoid double counting. | Provide a separate order-header table alongside the line-level table. |
| **Returned flag coded "Yes" / "Not"** and carrying no refund, reason or return date. | Created an Order Status field (returned or not returned) for reporting. Sales and profit are reported gross, because refunds are unknown. | Capture return reason, return date and refund amount with a standard Yes/No code. |
| **No promised or actual delivery date,** and no cost, inventory or supplier fields. | Limited the lead-time analysis to order-to-ship (fulfilment) time and called it Shipping Processing Time rather than delivery time. | Capture promised and actual delivery dates and shipping cost so on-time delivery and cost-to-serve can be measured. |
| **No currency stated** and no service targets in the source. | All customers are in the United States, so values are shown in dollars. Ship-mode service targets are analyst-defined, not taken from the source. | Document currency and agree official service targets per ship mode with Operations. |
| **Short time window and small groups:** four years of data, and only 264 Same Day orders. | Reported figures as observed and avoided strong claims about small segments. | Refresh the report with newer data and review small segments before acting on them. |

### Assumptions

- Orders whose ship date equals the order date on Standard, Second or First Class are treated as data entry errors rather than true same-day shipments.
- Shipping processing days means order date to ship date; it is not delivery time.
- Return rate is the share of distinct orders flagged as returned. At sub-category level it is the share of orders containing that sub-category that were returned.
- The sales figure for returned orders is gross (before any refund).
- Yearly figures use the order year; area charts start their value axis at zero, so small changes appear gentle.


---

## 8. Skills Demonstrated

| Skill area | How it was applied in this project |
|---|---|
| **Data cleaning and preparation (Power Query)** | Detected and repaired swapped day and month values, created flags for unreliable ship dates, standardised the order status and prepared the data model for analysis. |
| **Data quality investigation** | Profiled 9,994 rows, found date corruption and zero-day shipments, quantified their size (889 orders, 17.75%) and documented mitigations. |
| **Data modelling** | Built a date dimension (1,826 days) and a dedicated measures table over an order-line fact table. |
| **DAX measures** | Created 16 measures including distinct-order counts, valid shipment logic, average shipping days, return rates and profit margin. |
| **Business KPI design** | Defined fulfilment, reliability, returns and customer KPIs that connect directly to operational decisions. |
| **Dashboard design and storytelling (Power BI)** | Designed a branded, multi-page report with slicers, KPI cards, drill-through, page navigation and a consistent colour theme. |
| **Data visualisation** | Matched chart types to questions: column, bar, donut, area, table and card visuals. |
| **Analytical thinking and insight generation** | Linked symptoms to causes (for example, returns to region and sub-category, and sales to profit leakage by state). |
| **Business problem solving** | Translated findings into ten evidence-based, owner-assigned recommendations. |
| **Documentation and communication** | Wrote an executive summary, stated assumptions and limitations transparently, and explained every insight for a non-technical reader. |


**Tools:** Excel, Power Query, DAX, Power BI.
***Prepare by Daniel Diala***
---

<p align="center"><a href="#table-of-contents">Back to top</a></p>

