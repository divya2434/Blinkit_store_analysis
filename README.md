# Blinkit_store_analysis
This project aims to analyze web-scraped product data from Blinkit (an online grocery platform) to derive actionable business insights. Using SQL, we transform raw scraping data into an analytical report containing product-level sales estimations, pricing patterns, and distribution metrics across cities. We estimate demand using inventory snapshots over time, calculate weighted On Shelf Availability (OSA), and extract modal pricing for better price benchmarking. The project demonstrates how scraped e-commerce data can be cleaned, structured, and enriched to help decision-making in the retail and supply chain space.

1. inventory_snapshots CTE
We begin by creating a snapshot of product data using inventory_snapshots. It includes relevant product details like SKU, brand, price, inventory, category, city, and store. We also calculate two row numbers (rn_min and rn_max) using ROW_NUMBER() to identify the earliest and latest inventory records per SKU per city. This helps in estimating how much inventory has changed over time, which we use later to infer sales quantity.

2. min_inventory CTE
This step extracts the minimum inventory (earliest snapshot) for each SKU and city using the rn_min = 1 filter. It tells us how much stock was available at the beginning of the observed time window.

3. max_inventory CTE
Similar to the previous step, we now get the maximum inventory (latest snapshot) per SKU and city using rn_max = 1. It shows us the closing inventory. When compared to the minimum, we can estimate sales activity for that period.

4. est_qty_sold_calc CTE
Here, we compute the estimated quantity sold as the difference between the earliest and latest inventory. This gives a proxy for sales assuming that the only change in inventory is due to consumer purchases. This is a common technique when real-time sales data isn't available.

5. mode_prices CTE
This step determines the most frequently occurring price (mode) per SKU per city using ROW_NUMBER() ordered by COUNT(*) DESC. It helps eliminate outliers or temporary price changes and gives a stable price reference (for both selling_price and mrp) to use in revenue calculations.

6. store_stats CTE
We calculate distribution-related metrics here. It captures how many stores listed a product (listed_ds_count) and how many had the product in stock (in_stock_ds_count). This helps measure availability and presence in the market.

7. total_ds_count CTE
This is a global count of all unique stores (store_id) present in the dataset. We use this number in the next step to calculate weighted OSA metrics that compare how widely available a product is.

8. final_data CTE
This is the core output step where we join all previous CTEs and assemble a comprehensive dataset. It includes product info, estimated sales (est_qty_sold × price), modal pricing, category details, distribution metrics, OSA across all stores (wt_osa) and listed stores (wt_osa_ls), and discount percentage. Calculations are wrapped with COALESCE to handle nulls and avoid errors.

9. Final SELECT
We perform a SELECT DISTINCT * from the final_data CTE to produce the cleaned, enriched, and analytics-ready data. This final output can be exported or visualized further using BI tools or shared with stakeholders.

