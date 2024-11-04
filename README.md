Staffelter Hof Winery is Germany's oldest business, established in 862 under the Carolingian dynasty. It has continued to serve customers through dramatic changes in Europe, such as the Holy Roman Empire, the Ottoman Empire, and both world wars. What characteristics enable a business to stand the test of time?

To help answer this question, BusinessFinancing.co.uk researched the oldest company still in business in **almost** every country and compiled the results into several CSV files. This dataset has been cleaned.

Having useful information in different files is a common problem. While it's better to keep different types of data separate for data storage, you'll want all the data in one place for analysis. You'll use joining and data manipulation to work with this data and better understand the world's oldest businesses.

## The Data
`businesses` and `new_businesses`
|Column|Description|
|------|-----------|
|`business`|Name of the business (varchar)|
|`year_founded`|Year the business was founded (int)|
|`category_code`|Code for the business category (varchar)|
|`country_code`|ISO 3166-1 three-letter country code (char)|
---
`countries`
|Column|Description|
|------|-----------|
|`country_code`|ISO 3166-1 three-letter country code (varchar)|
|`country`|Name of the country (varchar)|
|`continent`|Name of the continent the country exists in (varchar)|
---
`categories`
|Column|Description|
|------|-----------|
|`category_code`|Code for the business category (varchar)|
|`category`|Description of the business category (varchar)|

## What is the oldest business on each continent?

```sql
SELECT MIN(b.year_founded) AS oldest_business, c.continent
FROM businesses b
INNER JOIN countries c
	ON c.country_code = b.country_code
GROUP BY c.continent
ORDER BY oldest_business ASC
```
|continent|country_count|
|------|-----------|
|Africa|3|
|Asia|7|
|Europe|2|
|North America|5|
|Oceania|10|
|South America|3|	



## How many countries per continent lack data on the oldest businesses </br>  Does including the `new_businesses` data change this?

```sql
WITH allbusiness AS (
    SELECT b.business, b.year_founded, b.category_code, b.country_code 
    FROM businesses b
    UNION ALL
    SELECT nb.business, nb.year_founded, nb.category_code, nb.country_code 
    FROM new_businesses nb
)

SELECT c.continent, COUNT(c.country) AS country_count
FROM countries c
LEFT JOIN allbusiness ab
    ON c.country_code = ab.country_code
WHERE ab.business IS NULL
GROUP BY c.continent
```
