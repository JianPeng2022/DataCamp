
## 01 Snowflake 

**Snowflake's data architecture**  
- Cloud Service:coordinate user activities
- Query processing: uses Massive parallel processing (MPP) architecture; distributes data and compute resources acrss a cluster of nodes (computers)
- Database Storage: stores data in a columnar format (optimized for analytical queries)

**Monitoring Query and Copy History**

**Setting up stage permissions**
- Storage integration: Reference cloud credentials and storage provider (AWS, GCP, Azure...)
- Stage: Reference storage integration and file directory
- Creating a table from an external stage:
  - Create storage integration
  - create the stage
  - refresh the stage
  - reference the stage in table creation
  - load data into a table