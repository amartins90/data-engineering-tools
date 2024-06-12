# Google Cloud Platform

## Professional Data Engineer

- BigTable is a NoSQL solution for data like time-series (severless Data Warehouse).
- Federated queries allow access of Cloud SQL data directly from BigQuery. Useful when data is frequently changing.
- When data is non-finite but you need intermediate results, windowing helps separates the entire time period into intermediate time periods of processing.
- Materialized views improves query performance by precomputing and periodically caching query results. Results are periodically refreshed.
- Access policies can be applied to tables, columns and rows. A view offers a security layer because it is possible to see the results but neither tables or export the data.
- Data Loss Prevention (DLP) identifies PII in a file.
- User Defined Functions (UDF) allows create functions to extend SQL functionalities using programming languages as JavaScript. It is temporary only.
- Query __TABLES__ to retrieve metadata.
- Data Warehouse often denormalize data.
- Best practice: tables up to 10GB keep them normalized and use JOINS, above 10GB denormalize them.
- Arrays are called REPEATED fields in BigQuery.
- Arrays allows you go deep into your schema while Structs go wider.
- Array:
    - Finding the number of elements with ARRAY_LENGTH(<array>)
    - Deduplicating elements with ARRAY_AGG(DISTINCT <field>)
    - Ordering elements with ARRAY_AGG(<field> ORDER BY <field>)
    - Limiting ARRAY_AGG(<field> LIMIT 5)
- Struct:
    - A SQL STRUCT is simply a container of other data fields which can be of different data types. 
    - STRUCTs (and ARRAYs) must be unpacked before you can operate over their elements. Wrap an UNNEST() around the name of the struct itself or the struct field that is an array in order to unpack and flatten it.
- A partitioned table is a table that is divided into segments. Doing this can lead to reduce costs and improve performance.