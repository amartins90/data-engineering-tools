# Personal Notes

## ACID
A for ATOMICITY: a transaction are composed by statements, if only one statement fails then the transaction should fail.
C for CONSISTENCY: each transaction should bring the database from a consistent state to another consistent state.
I for ISOLATION: even when transactions are processed concurrently the final state should be the same if the transactions are processed sequentially.
D for DURABILITY: a committed transaction should remains committed.

Locking does not allow change a row while this same row is being updated.
Multiversioning does not lock the table but send to the subsequent transaction the previous state - before being modified by the on going action.

## PL/SQL and SQL
SQL executes a single query at a time, PL/SQL can execute a block of code at a time. PL stands for Procedural Language.

## OLAP and OLTP
OLAP (the "A" stands for Analytical) is intendend to analyse aggregated data. OLTP (the "T" stands for Transaction) is to process database transactions.
OLAP: generate reports, perform complex data analysis and identify trends (read optimized).
OLTP: process orders, update inventory and manage customer accounts (write optimized).

## SCALA
- Function signatures tells what the function does.
- Pattern matching is a mechanism to check a value against a pattern.

## FACTS and DIMENSION tables
- Dimension tables store the descriptive context like Products table.
- Fact tables store measurable transactions like Sales table.
- Both tables are the backbone of star and snowflake schemas, being Fact table standing in the middle.

## DATA MESH and DATA FABRIC
- Data Mesh is a data architecture which the data is descentralized and empower domain specific teams.
- Data Fabric is a data architecture which the data is centralized in a unified architecture.

## CAP theorem
C stands for Consistency, A for Availability and P for Partition Tolerance.

## DATA ORCHESTRATION
Data Orchestration helps automate the flow of data between tools and systems to ensure organizations are working with complete, accurate and up-to-date information.

## TOOLS vs ARCHITECTURE
- Data Lake is a centralized repository to store structured and unstructured data. Data can be organized into zones. Tools: Amazon S3, Azure Data Lake Storage, Hadoop.
- Data Warehouse is a system store historical data for analytics and reporting. It can be architected using Star schema (central fact table connected to dimension tables) or Snowflake schema (similar to Star schema but with normalized dimension tables). Tools: Amazon RedShift, Google BigQuery, Snowflake.
- Data Mart is a subset of Data Warehouse designed to a particular line of business. Tools: SQL.
- Data Pipeline is data processing like EL, ETL and ELT. Tools: Apache Kafka, Apache NiFi, AWS Data Pipeline.
- Lambda Architecture combines batch and stream processing. Tools: Apache Haddop for batch processing and Apache Storm for real-time.
- Kappa Architecture focuses on real-time data processing. Tools: Apache Kafka.
- Medallion Architecture is a layered approach to structure data in a Data Lake: layer Bronze for raw data, Silver for cleaned and conformed data and Gold for aggregated and optimized data for business reporting. Tools: Databricks
- Hub and Spoke Model is a centralized data repository (hub) connected to Data Marts (spokes).
- Data Mesh is a architecture to descentralize data ownership treating data as a product. Each domain is responsible for your own data.
- Data Fabric is a distributed architecture that enables data integration and access across various environments.

## TRIGGERS vs STORED PROCEDURES
- A trigger is invoked when triggering an event like an UPDATE.
- A stored procedure is explicitly called using the keyword EXECUTE.