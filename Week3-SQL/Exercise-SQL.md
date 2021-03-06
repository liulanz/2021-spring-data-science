# SQL: Structured Query Language Exercise

### Getting Started

1. Go to BigQuery UI https://console.cloud.google.com/bigquery
2. Add in the public data sets. 3. Click the Add Data icon 4. Add any dataset 5. `bigquery-public-data` should become visible and populate in the BigQuery UI.
3. Add your queries where it says [YOUR QUERY HERE].
4. Make sure you add your query in between the triple tick marks.

---

For this section of the exercise we will be using the `bigquery-public-data.austin_311.311_service_requests` table.

5. Write a query that tells us how many rows are in the table.

   ```sql
   SELECT COUNT(*) as row_number FROM `bigquery-public-data.austin_311.311_service_requests`
   ```

6. Write a query that tells us how many _distinct_ values there are in the complaint_description column.

   ```sql
   SELECT
   COUNT(DISTINCT(complaint_description)) AS distinct_com_des_count
   FROM `bigquery-public-data.austin_311.311_service_requests`

   ```

7. Write a query that counts how many times each owning_department appears in the table and orders them from highest to lowest.

   ```sql
   SELECT owning_department, COUNT(owning_department) as owning_department_cnt
   FROM `bigquery-public-data.austin_311.311_service_requests`
   GROUP BY owning_department
   ORDER By owning_department_cnt DESC
   ```

8. Write a query that lists the top 5 complaint_description that appear most and the amount of times they appear in this table. (hint... limit)
   ```sql
   SELECT complaint_description, COUNT(complaint_description) as complaint_description_cnt
   FROM `bigquery-public-data.austin_311.311_service_requests`
   GROUP BY complaint_description
   ORDER By complaint_description_cnt DESC
   LIMIT 5
   ```
9. Write a query that lists and counts all the complaint_description, just for the where the owning_department is 'Animal Services Office'.

   ```sql
   SELECT owning_department, COUNT(owning_department) as owning_department_cnt
   FROM `bigquery-public-data.austin_311.311_service_requests`
   WHERE owning_department = 'Animal Services Office'
   GROUP BY owning_department
   ```

10. Write a query to check if there are any duplicate values in the unique_key column (hint.. There are two was to do this, one is to use a temporary table for the groupby, then filter for values that have more than one count, or, using just one table but including the `having` function).
    ```sql
    WITH tmp AS (
    	SELECT unique_key, COUNT(unique_key) as unique_key_cnt
    	FROM `bigquery-public-data.austin_311.311_service_requests`
    	GROUP BY unique_key
    )
    SELECT * FROM tmp
    WHERE unique_key_cnt >1
    ```

### For the next question, use the `census_bureau_usa` tables.

1. Write a query that returns each zipcode and their population for 2000 and 2010.
   ```sql
   SELECT popu_2000.zipcode, popu_2000.population as population_2000, popu_2010.population as population_2010
   FROM `bigquery-public-data.census_bureau_usa.population_by_zip_2000` as popu_2000
   JOIN
   `bigquery-public-data.census_bureau_usa.population_by_zip_2010` as popu_2010
   ON popu_2000.zipcode = popu_2010.zipcode
   ```

### For the next section, use the `bigquery-public-data.google_political_ads.advertiser_weekly_spend` table.

1. Using the `advertiser_weekly_spend` table, write a query that finds the advertiser_name that spent the most in usd.
   ```sql
   SELECT advertiser_name, SUM(spend_usd) as total_spend_usd
   FROM `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
   GROUP BY advertiser_name
   ORDER BY total_spend_usd DESC
   ```
2. Who was the 6th highest spender? (No need to insert query here, just type in the answer.)

   ```
   SENATE LEADERSHIP FUND
   ```

3. What week_start_date had the highest spend? (No need to insert query here, just type in the answer.)

   ```
   2020-10-25
   ```

4. Using the `advertiser_weekly_spend` table, write a query that returns the sum of spend by week (using week_start_date) in usd for the month of August only.
   ```sql
   SELECT week_start_date, SUM(spend_usd) as total_spend_usd
   FROM `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
   WHERE Extract(MONTH FROM week_start_date) = 08
   GROUP BY week_start_date
   ```
5. How many ads did the 'TOM STEYER 2020' campaign run? (No need to insert query here, just type in the answer.)
   ```
   50
   ```
6. Write a query that has, in the US region only, the total spend in usd for each advertiser_name and how many ads they ran. (Hint, you're going to have to join tables for this one).
   ```sql
   With tmp AS(
   	SELECT d1.advertiser_id, d1.advertiser_name, d1.spend_usd, country
   	FROM `bigquery-public-data.google_political_ads.advertiser_weekly_spend` d1
   	JOIN `bigquery-public-data.google_political_ads.advertiser_geo_spend` d2
   	ON d1.advertiser_id = d2.advertiser_id
   	WHERE country = 'US'
   )
   SELECT advertiser_id, advertiser_name, SUM(spend_usd) as total_spend_usd
   FROM tmp
   GROUP BY advertiser_id, advertiser_name
   ```
7. For each advertiser_name, find the average spend per ad.

   ```sql
    SELECT  advertiser_name, avg(spend_usd) as usd, avg(spend_czk) as czk, avg(spend_dkk) as dkk,
    avg(spend_eur) as eur, avg(spend_gbp) as gdp, avg(spend_hrk) as hrk, avg(spend_huf) as huf,
    avg(spend_inr) as inr, avg(spend_nzd) as nzd, avg(spend_pln) as pln, avg(spend_ron) as ron
    FROM `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
    GROUP BY advertiser_name

   ```

   ```sql
    SELECT  advertiser_name, avg(spend_usd)as cnt
    FROM `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
    GROUP BY advertiser_name
    ORDER By cnt
   ```

8. Which advertiser_name had the lowest average spend per ad that was at least above 0.

   ```sql
   WITH tmp as(
   	SELECT  advertiser_name, avg(spend_usd)as cnt
   	FROM `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
   	GROUP BY advertiser_name
   )
   SELECT * FROM tmp
   WHERE cnt >0
   ORDER By cnt
   ```

   ```
   BRANDY K. CHAMBERS
   ```

## For this next section, use the `new_york_citibike` datasets.

1. Who went on more bike trips, Males or Females?
   ```sql
   SELECT  gender, count(gender) as cnt
   FROM `bigquery-public-data.new_york_citibike.citibike_trips`
   GROUP BY gender
   ```
2. What was the average, shortest, and longest bike trip taken in minutes?

   ```sql
   SELECT  (AVG(tripduration)/60) as avg_trip_duration_in_min, (Min(tripduration)/60) as min_trip_duration_in_min,
   (Max(tripduration)/60) as max_trip_duration_in_min
   FROM `bigquery-public-data.new_york_citibike.citibike_trips`

   ```

3. Write a query that, for every station_name, has the amount of trips that started there and the amount of trips that ended there. (Hint, use two temporary tables, one that counts the amount of starts, the other that counts the number of ends, and then join the two.)
   ```sql
   WITH
   start_station AS(
   	SELECT start_station_name, count(start_station_name) as start_cnt
   	FROM `bigquery-public-data.new_york_citibike.citibike_trips`
   	GROUP BY start_station_name
   ),
   end_station AS (
   	SELECT end_station_name, count(end_station_name) as end_cnt
   	FROM `bigquery-public-data.new_york_citibike.citibike_trips`
   	GROUP BY end_station_name
   )
   SELECT start_station_name, start_cnt,end_cnt
   FROM start_station s
   JOIN end_station e
   ON s.start_station_name = e.end_station_name
   ```

# The next section is the Google Colab section.

1. Open up this [this Colab notebook](https://colab.research.google.com/drive/1kHdTtuHTPEaMH32GotVum41YVdeyzQ74?usp=sharing).
2. Save a copy of it in your drive.
3. Rename your saved version with your initials.
4. Click the 'Share' button on the top right.
5. Change the permissions so anyone with link can view.
6. Copy the link and paste it right below this line.
   - YOUR LINK: `[LZ - BigQuery in Colab](https://colab.research.google.com/drive/1DdOZwZylwjMNDhO65vCrOPiEbN-CKstk?usp=sharing)`
7. Complete the two questions in the colab notebook file.
