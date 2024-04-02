Snowflake 
- **cloud data warehouse**: Scalability, Accessibility, Cost-efficiency, Lower management effort
- **Columnar data storage**
  
| Category         | Data Organization | Data Retrieval     | Operations     | Example                                               |
|------------------|-------------------|--------------------|----------------|-------------------------------------------------------|
| Row              | By Rows           | Complete records   | Transactional  | Postgres, MySQL, Oracle, Microsoft SQL Server         |
| Columnar         | By Columns        | Relevant columns   | Analytical     | Snowflake, Amazon Redshift, Google BigQuery, Vertica  |

Transactional operations: like inserting, updating, and deleting individual records within a table, all the data for a single record is stored together.
Analytical operations: it only needs to access and process the specific columns relevant to the query, leading to significantly faster response times.
