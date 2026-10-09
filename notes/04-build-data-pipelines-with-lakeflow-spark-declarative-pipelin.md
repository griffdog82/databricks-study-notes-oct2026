# Build Data Pipelines with Lakeflow Spark Declarative Pipelines

*Order: 3 · Status: Ok-isk · Type: Module*

What is the Multi-File Editor?

- It makes developing and debugging your ETL pieplines easier and more efficient
- Extract, Load, Transform data pipelines. This is the complex way of using a Connector essentially.

Options for Compute Clusters to Run a Pipeline

Serverless (Recommended) vs Classic (Fixed Size)

- Serverless is cheaper, allows for incremental refresh
- If you need it to be good for time-sensitive Lakeflow jobs, use Serverless Performance Optimized. Meaning, you use more compute and receive a faster pipeline

Classic Compute

- Fixed-Size Clusters, Permissions can be set very easily. That being said, autoscaling is enabled by default, meaning workload volume can allow for the cluster to increase or decrease in size if needed

Code Assets

- Code Assets → Pipeline Root FOlder → Oftentimes a Git folder, allowing you to store your relevant files for a pipeline project
- Source Code Section →These are the actual files in your root folder, i.e. what you are running. Python scripts, SQL files, Notebooks etc.

Configuration (Parameters)

- These are key:value pairs that you can use to parameterize your code (i.e. use variables and arguments to pass values into functions)

There’s a demo here. We will watch that demo, then describe step by step what that demo entails.

## Demo: Developing a Simple Pipeline

Create a pipeline and reference a folder with the asset

Right on developing a simple

How Cfonguration is used

  - You can set a parameter called source → that points to a volume

HOw to switch a pipeline mode into contionoius vs triggered

- Go to advanced settings on the right hand panel.

The pipeline uses a streaming table, i.e. using CREATE OR REFRESH STREAMING TABLE

FROM STREAM read_files ( — Performs reads with checkpoints “${source}/orders:, —uses the source config variable set earlier

format → ‘JSON’

What is the dry run feature?

- Basically it is validating that the pipeline script actually could compile
- It is confirming that the source parameter and the created tables could actually exist

Now that the pipeline is ready, the teacher is ready to run.

Full Refresh → Removes all checkpoints and re-process everything

```
-- TABLE PROPERTY TO AVOID FULL REFRESH
Ppipelines.reset.allowed = false
```

For now, if you use this option, understand what it does. You must be fully aware of what it will do

Anyhow

The teacher runs the file, and can use the pipeline graph to view the streaming tables. They follow a sequential path, with the bronze → sliver → gold.

ONce the teacher runs a second time, and checs the imcremtalization tab for each table, he notices that nothing has changed. This is because the datatable is a streaming table, and it is set to autoload automcailly.

**Declarative Pipelines →** Know data is already processed. They are incremental, i.e. only adding new data to a process

- Later, the teacher runs C, and runs a copy_file script to add a new file to the orders volumes. (sidenote: catalog.schema.table/volume/function)
- You can select a table using catalog explorer
- Catalog explorer gives you insights into the history of the table, using the history tab in the catalog explorer UI.
- You can see using the operation metrics tab the incremental updates to a table, showing the number of records added each time

Then the teacher shows the Jobs and Pipelines Tab under Workflows

- You mniotor how pipelines work

## Ensure Data Quality with Expectations

- Essentially, this lesson is about how you can create and add data expectations to your pipelines. I.e. you can validate streaming data and have actions setup to handle records that fail data quality checks

What are Expectations

- Expectations are data quality rules that are run during ETL processes

SYNTAX

```
CONSTRAINT constraint_name
EXPECT (column_condition)
[ON VIOLATION action]
```

What are the three actions that an Expectation can be set to?

- WARN → Adds to the log that a record has violated a constraint. It will still add the record/row tot the target. Metrics including count of valid vs invalid are provided
- DROP → Exclude invalid rows. These dropped rows are loggest alongside other metrics
- FAIL → The single flow that is running is set to fail upon encountering an invalid record. Manual intervention is required at this point to resolve the issue

WARN Example

```
CONSTRAINT valid_notification
EXPECT (notifications IN ('Y','N'))
```

DROP

```
CONSTRAINT valid_date
EXPECT (order_timestamp > "2021-01-01")
ON VIOLATION DROP ROW
```

FAIL

```
CONSTRAINT valid_id
EXPECT (customer_id IS NOT NULL)
ON VIOLATION FAIL UPDATE
```

Adding Expectations

Imagine a streaming table named orders_sliver. Below I will write out how you add constraints

Streaming table creation → Warn constraint on valid_notifications, FAIL CONSTRAINT on valid_date, DROP Constraint on valid_id

```
CREATE OR REFRESH STREAMING TABLE 2_silver_db.orders_silver
 (
   CONSTRAINT valid_notifications EXPECT (notifications IN ('Y','N')),
   CONSTRAINT valid_date EXPECT (order_timestamp > "2021-01-01") ON VIOLATION FAIL UPDATE,
   CONSTRAINT valid_id EXPECT (customer_id IS NOT NULL) ON VIOLATION DROP ROW
 )
AS
SELECT
  order_id,
  timestamp(order_timestamp) AS order_timestamp,
  customer_id,
  notifications
FROM STREAM 1_bronze_db.orders_bronze;
```

Thing to Understand Here: Basically, you create the constraints when you define the table.

## Demo 3: Adding Data Quality Expectations

- Completed it.

*[Image in the Notion page, not copied]*

## Demo 5 - Deploying a Pipeline to Production

Basically they explain how to form joins between two streaming tables (covered in the clickthrough lecture above as a stream-stream)

- Switch a pipeline between on

Gonna take notes on it instead of doing it

Bringing in orders then status is how this will work

He runs SELECT * from read_files on orders and sees that each order has a specific order_id.

Then he runs SELECT * from read_files on status, and filiters for a given order_number (75123)

- The status directory contains JSON files that describe placed with timestamp, preparing with timestamp column, on the way with timestamp column, and delivered with you guessed it, timestamp column.

Joining it together

- Get the order_id, order_timestamp, order_status and order_status_timestamp
- subtract order_status less order_status_timestamp
  - Stopping this, not worth the time. Need to focus on whatever the test questions end up being

## Change Data Capture (CDC)

- All CDC is a techique to tracka dn capture chagnes in a data source, lakehouse, or data warehouse. Changes are applied to a target table, your Lakehouse and the latest state from the source.
- Source Data → Inserts, Updates, Deletes
- CDC can be one of two types → Slowly Changing Dimensions (SCD)
  - Represent how historical changes are handled
  - SCD Type 1 OVerwrite target with latest values
    - Really simple. Just means you enact changes (INSERT, UPDATE, DELETE) without recording a history of changes
  - SCD Type 2 → Historical Tracking/Versioning
    - Using an additional set of columns (START AT and END AT), you can specify what dates make a particular column active or inactive
    - Instead of deleteing or updating actual rows, you just set a record as inactive, then add an additional row in the database. For a dlete, you just set it to inactive. For an add, you add the record and set END_AT to null

---
[Back to contents](../README.md)
