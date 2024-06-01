# Inventory analysis

## Table of contents

- [Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Tools](#tools)
- [Data Cleaning/Preparations](#data-cleaningpreparation)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Results/Findings](#resultsfindings)
- [Recommendations](#recommendations)

### Project Overview

The data analysis projects aims to caluculate how much inventory is being used, identify inventory that needs reordering, and calculate how much each pizza cost based on the ingredients.

### Data Sources

The primary dataset used for this analysis is the "pizza_proj" file , containing detailed information about all aspects of the pizza business.

### Tools

- Excel - Data Cleaning
- SQL server - Data Analysis


### Data Cleaning/Preparation

In the initail data preparation phase, I performed the following task:
1. Data loading and inspection.
2. Handling missing values.
3. Made custom Queries

### Exploratory Data Analysis

EDA involed exploring the pizza business data to keep an eye on pricing an P/L, here is what was needed:

- Total quantity by ingredients
- Total cost of ingredients
- Calculated cost of pizza
- Percentage stock remaining by ingredient
- List of ingredients to re-order based on remaining inventory
- Total orders
- Total sales
- Total items
- Average order value
- Sales by category
- Top selling items
- Orders by hour
- Orders by address
- Orders by delivery/pick up


### Data analysis

code/features worked with

```sql
SELECT
  o.order_id,
  i.item_price,
  o.quantity,
  i.item_cat,
  i.item_name,
  o.created_at,
  a.delivery_address1,
  a.delivery_address2,
  a.delivery_city,
  a.delivery_zipcode,
  o.delivery 
FROM
  orders o
  LEFT JOIN item i ON o.item_id = i.item_id
  LEFT JOIN address a ON o.add_id = a.add_id
```

```sql
select
s1.item_name,
s1.ing_id,
s1.ing_name,
s1.ing_weight,
s1.ing_price,
s1.order_quantity,
s1.recipe_quantity,
s1.order_quantity*s1.recipe_quantity as ordered_weight,
s1.ing_price/s1.ing_weight as unit_cost,
(s1.order_quantity*s1.recipe_quantity)*(s1.ing_price/s1.ing_weight) as ingredient_cost
  from(SELECT
  o.item_id,
  i.sku,
  i.item_name,
  r.ing_id,
  ing.ing_name,
  r.quantity AS recipe_quantity,
  sum( o.quantity ) AS order_quantity ,
  ing.ing_weight,
  ing.ing_price 
FROM
  orders o
  LEFT JOIN item i ON o.item_id = i.item_id 
  LEFT JOIN recipe r ON i.sku = r.recipe_id
  LEFT JOIN ingredient ing ON ing.ing_id = r.ing_id 
GROUP BY
  o.item_id,
  i.sku,
  i.item_name,
  r.ing_id,
  r.quantity,
  ing.ing_name,
  ing.ing_weight,
  ing.ing_price) s1
```

'''sql
SELECT
  s2.ing_name,
  s2.ordered_weight,
  ing.ing_weight*inv.quauntity as total_inv_weight,
  (ing.ing_weight*inv.quauntity)-s2.ordered_weight as remaining_weight
FROM
  ( SELECT ing_id, ing_name, sum( ordered_weight ) AS ordered_weight FROM stock1 GROUP BY ing_name, ing_id ) s2
  LEFT JOIN inventory inv ON inv.item_id = s2.ing_id
  LEFT JOIN ingredient ing ON ing.ing_id = s2.ing_id
  ```

``sql
SELECT
r.date,
s.first_name,
s.last_name,
s.hourly_rate,
sh.start_time,
sh.end_time,
((hour(timediff(sh.end_time,sh.start_time))*60)+(minute(timediff(sh.end_time,sh.start_time))))/60 as hours_in_shift,
((hour(timediff(sh.end_time,sh.start_time))*60)+(minute(timediff(sh.end_time,sh.start_time))))/60 *s.hourly_rate as staff_cost
from rota r
left join staff s on r.staff_id=s.staff_id
left join shift sh on r.shift_id=sh.shift_id
```

### Results/Findings

The analysis results are summarized as follows:
1. Total quantity by ingredients: know exactly how much of each ingredient you have in stock, allowing the ability to keep track of inventory levels.
2. Total cost of ingredients: Calculated the overall cost of all the ingredients used in pizza, giving clear picture of expenses.
3. Calculated cost of pizza: By factoring in the cost of ingredients, you would know how much it cost to make each pizza. This information will help monitor pricing and profitability.
4. Percentage stock remaining by ingredients: You would havethe percentage of stock remaining for each ingredient, enabling you to identify when its time to reorder specific ingredients.


### Recommendations

Based on the analysis, we recommend the following actions:
1. Set reoder points
2. Analyze ingredient cost
3. Optimize pricing strategy
4. Monitor ingredient usage



