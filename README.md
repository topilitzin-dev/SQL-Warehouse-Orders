### SQL-Warehouse-Orders
I wrote this query as part of the Google Data Analysis Professional Certificate from Coursera. The objective was to aggregate data into a table with warehouse ID, state, alias and number of orders, as well as the total of orders from all warehouses. I also added a column that classifies each warehouse by the percentage of orders fulfilled: 0–20%, 21-60%, or > 60%. I added annotations and steps on each line to better understand SQL's logic and order when executing queries. 

## Datasets Used
- warehouse_orders.warehouse.csv: Contains warehouse_id, warehouse_alias, maximum_capacity, employee_total and state.
- warehouse_orders.orders.csv: Contains order_id, customer_id, warehouse_id, order_date and shipper_date.

 ## Objectives
- Join both datasets using warehouse_id as the key.
- Calculate total of orders from all warehouses.
- Calculate fulfillment rates by warehouse.

## SQL Techniques Applied
- LEFT JOIN for dataset merging
- GROUP BY and COUNT() for aggregation
- CASE WHEN logic to sort by fulfillment rate
- HAVING to filter grouped rows
- SQL subqueries

 ## Insights Generated
- Warehouses 2666, 8118 and 6509 have the best fulfillment rates
- Warehouses 1543, 9080 and 4338 have the lowest fulfillment reates
- Out of the total of 9999 orders from all warehouses, 2666 and 8118 account for about 2/3 of all orders.

## Query
-- 3. Build subquery logic. This was written after steps 1-2
SELECT
  Warehouse.warehouse_id,
  CONCAT(Warehouse.state, ': ', Warehouse.warehouse_alias) AS warehouse_name,
  COUNT(Orders.order_id) AS number_of_orders,
  (SELECT COUNT(*) FROM my-first-project-458301.warehouse_orders.orders AS Orders) AS total_orders,
-- 4. Create categories using CASE
  CASE
    WHEN COUNT(Orders.order_id)/(SELECT COUNT(*) FROM my-first-project-458301.warehouse_orders.orders AS Orders) <= 0.20 # (This defines the first possible condition.)
    THEN 'Fulfilled 0-20% of Orders' # (THEN defines the label to apply when the first condition is true.)
    WHEN COUNT(Orders.order_id)/(SELECT COUNT(*) FROM my-first-project-458301.warehouse_orders.orders AS Orders) > 0.20 # (This is the first part of the second condition.)
    AND COUNT(Orders.order_id)/(SELECT COUNT(*) FROM my-first-project-458301.warehouse_orders.orders AS Orders) <= 0.60 # (This is the second part of the second condition.)
    THEN 'Fulfilled 21-60% of Orders' # (This defines the label to apply when the second condition is true.)
    ELSE 'Fulfilled more than 60% of Orders' # (This defines the label to apply when neither of the first two conditions is true.)
    END AS fulfillment_summary # (The END keyword terminates the CASE declaration. Then the AS keyword indicates what the resulting column should be named.)
-- 1. Combine and alias the tables
FROM my-first-project-458301.warehouse_orders.warehouse AS Warehouse
LEFT JOIN my-first-project-458301.warehouse_orders.orders AS Orders
ON Orders.warehouse_id = Warehouse.warehouse_id
-- 2. Organize your new table
GROUP BY
  Warehouse.warehouse_id,
  warehouse_name
-- 5.Filter using HAVING
HAVING #HAVING clause filters groups of rows after they have been grouped
  COUNT(Orders.order_id) > 0
