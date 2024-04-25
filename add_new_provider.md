1. **Bring the data from the new provider to the common schema** defined by the [marketing common data model (MCDM)](seeds/mcdm_paid_ads_basic_performance_structure.csv). To do so, create a staging model `models/staging/sgt_your_provider.sql` and put your data transformations here (see an [example](models/staging/stg_tiktok.sql)). 

In case some of the MCDM fields cannot be calculated based on your data, please fill them with NULLs. As there are no column default values defined in the schema, non-NULL backfilling may affect the calculations.

If data transformation requires more sophisticated operations than field renaming and NULL-ification, it's worth following dbt code convention and moving all the heavy lifting into a separate file like `models/staging/int_your_provider.sql`.

2. **Add your conventionally prettified data to [the common report table](models/marts/ads_basic_performance.sql)** by appending your intermediate model  data to the query: 
```
select *
from {{ ref("stg_bing") }}
union all
    ...
union all
select *
from {{ ref("stg_your_provider") }}
--- or use `ref("int_your_provider")` if you have an intermediate model
```

3. Save all the changes and **propagate your data to the BigQuery** to make it available for Looker and Co.
```
dbt run --full-refresh
```

4. To proudly **inspect the final result**, visit [the report page](https://lookerstudio.google.com/reporting/f67c1f3d-6962-40b8-80d8-44970bf5fb1b).