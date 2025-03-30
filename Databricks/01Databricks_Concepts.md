## 01 Databricks Lakehouse Platform
**Data Warehouse**
- Great for Structural Data, BI and SQL analytics
- Highly performant
- Easy to keep data clean
- But very expensive, not built for AI, Data Science...

**Data Lake**
- Support for all use cases, Structural, Textual, Unstructured Data 
- Very flexible
- Cost effective
- But data can become messy
- Historically not ver performant

**Data Lakehouse - Databricks**
- Single platform for all data workloads
- Built on open source technology
- Collaborative environment
- Simplified architecture
- **unification**: it unifies your entire data stack into a single location, every use case from AI to BI, benefits of both data warehouse and data lake
- **multi-cloud**: the lakehouse is a multi-cloud technology that runs on all of the leading cloud platforms
- **collaborative**: it creates a collaborative platform for your data teams
- **open-source**: Apache Spark is the leading framework for data processing and powering the engine, databricks is underpinned by Apache Spark, and supports for most popular languages(Python, R, Scala, SQL)

**Core features of the Databricks Lakehouse Platform**
Benefits of Spark:
- Extensible, flexible open-source framework, you can support many data sources, data volumes, and use cases in a single framework, all while using whatever programming language you want
- Apache Spark has a very large and active developer community, meaning you can learn from their collective experience and apply that to your situation
- Spark is very performant when processing Big Data and will scale with your data
- Databricks has included a variety of optimizations into the platform-specific version of Spark, which results in significantly better performance and some quality-of-life improvements.


Databricks Compute: In the Databricks environment, there are a few different options to provide computing power to the Spark framework. The default compute is **Clusters**, which are collections of virtual machines that Databricks installs its Runtime. These clusters can be used for any data workload, or use case, and run any of the supported languages(Python, R, Scala, SQL...). **SQL Warehouses** are relatively new to the Databricks platform and provide a SQL-optimized engine for your Business Intelligence use cases. **Photon** is one such optimization, which is a rewritten Spark engine that significantly increases SQL performance.  

Cloud data storage: In the cloud, there historically have been two main ways to store data. First, many organizations put data into various databases hosted in the cloud, which provide a **rigid location for their structured data**. These are great for traditional data needs but **lack the flexibility** that many datasets need to grow with the business. Organizations can also store data in various file formats, such as CSV, JSON, and Parquet. These formats are usually open-source and much more flexible but **require more work to maintain data quality**.

Delta: With Databricks, we recommend storing data in **Delta Lake tables**, an open-source file format built around Parquet. By storing data in Delta, you will gain the performance and reliability of traditional data warehouse tables but stored in the flexible and scalable data lake. **ACID transactions ensure that data is only written once**, and can be **unified across batch and streaming workloads**. Delta maintains the flexibility of the data lake and allows for **schema evolution and restoring previous versions of data through time travel**.

Unity Catalog: **Unity Catalog** is the centralized governance solution for the Databricks Lakehouse. Using straightforward **SQL GRANT** statements, administrators can control **access to all data assets in the Lakehouse**, even **beyond just the data**. Governance covers data catalogs, notebooks, clusters, warehouses, even the Databricks Feature Store, and any machine learning models.

## 02Administering a Databricks workspace
**Account Admin** 
The **top level** of administration in Databricks is the Account Admin. This initially is the individual who sets up the Databricks Account, and has full control over the Databricks deployment. Typically, the Account Admin is focused on making sure all the Databricks environments are set up the right way. Their key responsibilities include creating workspaces, adding users to the specific workspaces, governing access to those workspaces, and monitoring the account subscription for billing.

**Account Console** 
If you are the Account Admin for your Databricks deployment, you will have access to the Account Console. This can be found at the URL on the screen and allows for various account-wide activities. This will be a key location during the initial setup. In the account console, you can see things like the total list of workspaces in your account, where your datasets are hosted in the data lakehouse, and how much your account uses from a Databricks Unit, or DBU, perspective.
- Workplace: information about all workplaces in your account, create a new workplace in any cloud as part of your account
- Data: The Data section is a single location where you can view and manage your various data catalogs as part of your overall data governance strategy with Unity Catalog.
- Users & Groups: manage how you provide access to your organization once users have been added to your account.
- Settings: allows you to create account-wide configurations, such as integrating your identity provider or enabling features for your workspaces.

**Workspace Admin** 
- Focuses on a specific set of workspaces, usually as part of their business unit within the organization
- Managing identities in your workspace
- Creating and managing compute resources
- Managing workspace features and settings

A Databricks workspace consists of two distinct pieces. 
- The first component is the **Data Plane**, the workspace and resources directly deployed into the customer cloud environment. This is where all **data, code, and compute resources reside**, ensuring that Databricks conforms to your existing cloud security practices.
- The second component is the **Control Plane**, which resides in the Databricks cloud environment. This section will control back-end processes like security and version updates and gather basic metadata about what is happening in your deployment, as well as sending requests to the Data Plane in your environment to run jobs, create clusters, and any other activities needed. When users login to Databricks, they are logging into the Web Application that is hosted in the Databricks Control Plane.

## 03 Data Engineering in Databricks
Common data sources and types:
- Delta tables(Databricks format)
- File formats (SCV, JSON, Parquet, XML...)
- Database (MySQL, postgres, EDW)
- Streaming data
- Images/Videos
  
Kinds of tables in Databricks:
  - Managed Tables: Default type, stored with Unity Catalog, Databricks managed
  - External tables: Stored in another location, have to set LOCATION, customer managed the table

Data transformations in Databricks
- **schema manipulation**: add, remove and redefine columns...
- **filtering**: reduce Dataframe to subset of data, pass multiple criteria...
- **nested data**: arrays or struct data, expand or contract, wide to long or long to wide
- **aggregation**: group data based on colunms, calculate data summarizations

**Auto Loader** processes new data files as they land in a data lake. Pipe data from cloud storage into Delta Lake as it arrives, "Set and frogot" model eliminates complex setup.
*spark.readStream <br>
    .format("cloudFiles") <br>
    .option("cloudFiles.format","json")<br>
    .load(file_path)*

*Spark Structured Streaming* format("kafka")
1. Gather information about the Kafka topic
2. Read the data into a Structured Streaming Dataframe
3. Clean the streaming data
4. Join our other dataset into the stream
5. Write the stream as a Delta table in a new data lake bucket

**Data orchestration**: a form of automation, enables data engineers to automate the end-to-end data life cycle. Databricks can do this.

## 04 Databricks SQL
Photon: SQL-optimized performance
Databricks SQL vs. other Data Warehouses:
- open file format (Delta) vs. proprietary data format
- separation of compute and storage vs. Storage often tied to compute
- ANSI SQL (open american national standart) vs. Tech-specific SQL
- Integrated into other data workloads vs. usually lacking advanced analytics

Raw data -> Bronze (Raw integration) -> Silver(filtered, cleaned, augmented)-> Gold (business level aggregates) -> BI,ML...