---
title: "Interactive analytics" # MODIFY THIS TITLE
chapter: true
weight: 5 # MODIFY THIS VALUE TO REFLECT THE ORDERING OF THE MODULES
---

## Interactive analytics

Starburst Galaxy allows you to run one query and return values that join together information from multiple data sources. In this example first investigate the data provided from the COVID-19 data lake and then join this with the TPC-H dataset which is stored in an entirely different location.

### Query the enigma_jhu table

Analyze the enigma\_jhu table to determine the most valuable insights to be reported. Run a select query to return most of the pertinent values and observe the nature of the data within the table.

    SELECT
        fips,
        province_state,
        country_region,
        last_update,
        confirmed,
        recovered,
        active
    FROM
        enigma_jhu
    ORDER BY
        fips;
    

There are many different `last_update` times. Evaluate one specific FIPS code for more information on the data contained within the table.

    SELECT
        fips,
        province_state,
        country_region,
        last_update,
        confirmed,
        recovered,
        active
    FROM
        enigma_jhu
    WHERE
        fips = '36121'
    ORDER BY
        last_update DESC;
    

Look at the confirmed case count. It’s an aggregate, adding the previous confirmed cases to any additionally confirmed cases. Only evaluate data that has a `last_update` equal to the maximum value.

Run a query to find the most recent update.

    SELECT max(last_update) FROM enigma_jhu;
    

Only evaluate data that has a `last_update` equal to the maximum value.

    SELECT
        fips,
        province_state,
        country_region,
        last_update,
        confirmed
    FROM
        enigma_jhu
    WHERE
        last_update = '2020-05-30T02:32:48'
    ORDER BY
        last_update DESC;
    

This query is successful for any location that has a `last_update` value equal to that maximum entry. However, this is not accurate for any location that has a different `last_update` value not equivalent to the maximum entry as their total count will be excluded. Run the following example focusing on the state of Utah that this issue.

    SELECT DISTINCT
        fips
    FROM
        enigma_jhu
    WHERE
        province_state = 'Utah'
        AND fips NOT IN
        (
            SELECT
                fips
            FROM
                enigma_jhu
            WHERE
                province_state = 'Utah'
                AND last_update = '2020-05-30T02:32:48'
        );
    

The query returns all the distinct FIPS codes in Utah that do not have an entry containing the maximum `last_update` value. Look specifically at one FIPS code within Utah.

    SELECT * FROM enigma_jhu WHERE fips = '49005' ORDER BY last_update DESC;
    

The maximum `last_update` for Cache, Utah is on April 16th, 2020, which is at least one month before the final May 30th, 2020 date. Therefore, you need a different solution for calculating the sum of confirmed cases. Add a [first value window function](../sql/ref/functions/window.html#value-functions) to create a column identifying the most recent update for each `fips`.

    SELECT
        fips,
        admin2 AS county,
        province_state,
        country_region,
        confirmed,
        first_value(last_update) OVER (
          PARTITION BY fips ORDER BY last_update DESC) AS most_recent,
        last_update
    FROM
        enigma_jhu;
    

To only return records that contain the latest `last_update` value for each `fips`, run a nested query which only selects values where `last_update = most_recent`.

    SELECT
     fips,
     county,
     province_state,
     country_region,
     confirmed,
     last_update
    FROM
     ( SELECT
         fips,
         admin2 AS county,
         province_state,
         country_region,
         confirmed,
         first_value(last_update) OVER (
          PARTITION BY fips ORDER BY last_update DESC) AS most_recent,
         last_update
       FROM
         enigma_jhu
         ) cases
    WHERE
     last_update = most_recent
    GROUP BY
        fips,
        county,
        province_state,
        country_region,
        confirmed,
        last_update;
    

You can also run this same query using a `WITH` statement.

    WITH
        cases AS (
            SELECT
                fips,
                admin2 AS county,
                province_state,
                country_region,
                confirmed,
                first_value(last_update) OVER (
                  PARTITION BY fips ORDER BY last_update DESC) AS most_recent,
                last_update
            FROM
                enigma_jhu
        )
    SELECT
        fips,
        county,
        province_state,
        country_region,
        confirmed,
        last_update
    FROM
        cases
    WHERE
        last_update = most_recent
    GROUP BY
        fips,
        county,
        province_state,
        country_region,
        confirmed,
        last_update;
    

### Query the TPC-H dataset

The TPC-H dataset provides two tables of interest: the nation table and the region table. Both tables assist in aggregating the total confirmed cases per region.

Run a query to achieve familiarity with the nation table.

    SELECT * FROM tpch.tiny.nation LIMIT 10;
    

There are 25 nations within the table.

Run a query to achieve familiarity with the region table.

    SELECT * FROM tpch.tiny.region LIMIT 10;
    

There are 5 regions in the table.

The `region_key` in the region table acts as the foreign key in the nation table. Visit the [TPC-H dataset page](../catalogs/tpch.html) for more information on the relationships within the dataset.

### Federate your data sources

To determine the region aggregation, join the data lake table with the TPC-H nation table. Only consider the 25 countries that are accounted for in the nation table.

Run the query to append the COVID-19 data with the proper region. Notice this query joins together data from two different data sources.

    SELECT
        country_region,
        first_value(last_update) OVER (
          PARTITION BY fips ORDER BY last_update DESC) AS most_recent,
        last_update,
        confirmed,
        nationkey,
        name,
        regionkey
    FROM
        aws_covid.query_federation.enigma_jhu enigma
        INNER JOIN tpch.tiny.nation nation ON UPPER(enigma.country_region) = nation.name
    ORDER BY
        confirmed DESC;
    

This query is a good start; however, it only returns 24 of the 25 countries. Run a distinct query to identify which country is missing.

    SELECT DISTINCT
        name
    FROM
        tpch.tiny.nation
    WHERE
        name NOT IN (
            SELECT
                UPPER(country_region)
            FROM
                aws_covid.query_federation.enigma_jhu);
    

The United States is assigned the country\_region value of ‘US’ in the Enigma data. Fix the join query to account for this mismatch.

    SELECT
        country_region,
        confirmed,
        nationkey,
        name,
        regionkey,
        FIRST_VALUE(last_update) OVER (
          PARTITION BY fips ORDER BY last_update DESC) AS most_recent,
        last_update
    FROM
        aws_covid.query_federation.enigma_jhu enigma
        INNER JOIN tpch.tiny.nation nation ON UPPER(enigma.country_region) = REPLACE(nation.name, 'UNITED STATES', 'US')
    ORDER BY
        nationkey DESC;
    

Add another inner join to append the region information to each record.

    SELECT
        country_region,
        nationkey,
        confirmed,
        region.name AS region_name,
        FIRST_VALUE(last_update) OVER (
          PARTITION BY fips ORDER BY last_update DESC) AS most_recent,
        last_update
    FROM
        aws_covid.query_federation.enigma_jhu enigma
        INNER JOIN tpch.tiny.nation nation ON UPPER(enigma.country_region) = replace(nation.name, 'UNITED STATES', 'US')
        INNER JOIN tpch.tiny.region region ON nation.regionkey = region.regionkey;
    

Now, put the puzzle pieces together to fulfill the initial ask of aggregating the confirmed case count by region.

    WITH
        cases AS (
            SELECT
                country_region,
                nationkey,
                confirmed,
                region.name AS region_name,
                FIRST_VALUE(last_update) OVER (
                    PARTITION BY fips ORDER BY last_update DESC) AS most_recent,
                last_update
            FROM
                aws_covid.query_federation.enigma_jhu enigma
                INNER JOIN tpch.tiny.nation nation ON UPPER(enigma.country_region) = REPLACE(nation.name, 'UNITED STATES', 'US')
                INNER JOIN tpch.tiny.region region ON nation.regionkey = region.regionkey
        )
    SELECT
        SUM(confirmed) AS total_confirmed_cases,
        region_name
    FROM
        cases
    WHERE
        last_update = most_recent
    GROUP BY
        region_name
    ORDER BY
        total_confirmed_cases DESC;

### Challenge & Next Steps

Now that you have explored the query federation capabilities of Starburst Galaxy using the curated dataset and prebuilt SQL queries, try exploring the full dataset by writing your own SQL queries or by connecting your own data to the cluster.