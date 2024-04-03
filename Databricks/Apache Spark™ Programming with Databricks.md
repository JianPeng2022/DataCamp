Databricks: the Lakehouse Platform

### Spark Cluster Execution
Task (Unit of work) = Running a Memory Partition on Core within Executor. The client runs **query**(select * from sales, for example), and the **driver** builds marching orders for bite-size **Memory Partitions** (Workers) that the Executor's **Cores** then creates and runs in **Parallel** as Tasks.

Spark API
- Spark SQL + Dataframes
- Streaming
- MLlib
- Spark Core API: R, SQL, Python, Scala, Java

Commands in Notebook cells:
- %python, %r, %scala, %sql
- %sh: run shell code, only on Spark Driver, not the Workers
- %fs: shortcut for **dbutils** filesystem commands ?
- %md: markdown for styling the display
- %run: execute a remote notebook from a notebook

**Hive Metastore**: When you create a new Hive table (CREATE TABLE), the information related to the schema (column names, data types) is stored in the Hive metastore relational database. Other information like input/output formats, partitions, and directory/file locations are all stored in the metastore.

row-oriented data file type: CSV. column-oriented data file type: Parquet, Delta, ORC

Widgets allow you to add parameters to your notebooks and dashboards. Widgets are best for building as is re-executed notebooks/dashboards with different parameters.  这是设置UI界面, 给用户输入相关可变参数用的
There are 4 types of Widgets:
- Text: Input a value in a text box
- Dropdown: Select a value from a list of provided values
- Combobox: Text and dropdown. Select a value from the provided list or input one in the text box.
- Multiselect: Select one or more values from a list of provided values
- for example:
    -   dbutils.widgets. text("Text", "Hello World! ")
    dbutils.widgets.dropdown ("Dropdown", "1", [str(x) for x in range (1, 10)]) dbutils.widgets.combobox ("Combobox", "A",["A", "B", "C"])
    dbutils widgets-multiselect ("Multiselect","Yes", ["Yes", "No", "Maybe"])
