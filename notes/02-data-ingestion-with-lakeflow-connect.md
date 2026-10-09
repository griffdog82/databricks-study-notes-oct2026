# Data Ingestion with Lakeflow Connect

*Order: 1 · Status: Completed · Type: Module*

## Lakeflow Overview

### What is Databricks Lakeflow

- It is a solution for delivering high-quality data for analytics, AI and operational applications
- Three modules to know (Connect, Apache Spark Declarative Pipelines, and Jobs)

### LakeFlow Connect

- Just know that it allows you to pull data from files, cloud storage, databases, enterprise apps and streaming sources into the Lakehouse

### What is the Lakehouse?

- Lakehouse describes the architecture that Databricks provides
  - Solves the problem of having a data lake (i.e. messy unstructured data) that must be copied into a
  - Data warehouse → Place where data is properly stored, and business intelligence (analytics) can be run

What is Lakebase?

- Lakebase is the hub for managing frontend applications

### Control Plane

- Includes webUI, cluster management, workflows, notebooks

Compute and storage will always be in your customer cloud account (i.e. like the systems you work on at work)

*[Image in the Notion page, not copied]*

### Lakeflow

- Just know it handles data ingestion + transformation + orchestration. It is the business terminology for what Databricks does
- There are three key components to Lakeflow (i.e. the concept behind what Databricks does)

Recall there are three components

1.	Ingestion → Ingestion Connectors (i.e. Lakeflow Connect)

2.	ETL Development (how we extract, transform, and load data) → Spark Declarative Pipelines. I.e. you write a CREATE OR REFRESH STREAMING TABLE AS statement describing what you want and how to source it

3.	Orchestration → I.e. setting a Lakeflow job to automatically ingest and ETL data on a scheduled timeframe

## Connectors and Ingestion Types

### Managed Connectors

- Low-code/no-code fully managed by Databricks
  - SaaS connectors → Salesforce, ServiceNow, Workday
  - Database connectors → Use databases using Change Data Capture (CDC)

Predefined list of pipelines, ingest data directly into a streaming delta table

- All data movement happens in the data plane (on your controlled compute, not Databricks).

### Lakeflow Connect (Types of Connectors)

- Connectors are the actual methods data gets into the system
- So you can
  - Upload files directly
  - Use a standard connector (i.e. using batch, incremental batch, streaming)
- Managed Connectors
  - You can ingest data directly into the lakehouse
  - **Quick ref:** Managed (no-code) connectors = SaaS connectors (Salesforce, Workday, etc.) + Database connectors (CDC/query-based, e.g. SQL Server).
  - Auto Loader is a Standard connector, not managed — it needs streaming code. COPY INTO and CTAS are SQL commands for file ingestion, not connectors at all.

Let's go through the three types of connectors

### Batch Ingestion

- Recall there are two methods to doing this

1.	SQL Statement

1.	CREATE TABLE AS SELECT

2.	Python Method

1.	spark.read.load()

Key Note: Why do a batch ingestion?

If your data is a) small to moderate volume and b) not real-time, batch ingestion is fine. Easy to prepare in terms of time and complexity

#### CREATE TABLE AS (CTAS)

- Batch ingestion. Creates a delta table by default
  - Another good point
  - They automatically infer the schema (i.e. what type a piece of data is), instead of requiring the user to specify

```
SQL
CREATE TABLE payments (int payment_id, double amt)
 
VS DBSQL
 
CREATE TABLE paymentsdb AS
payment_id, amt
```

### Incremental Batch Ingestion

- If your data is still a) higher volume and b) doesn't require real-time processing, you can run an incremental batch ingestion
- Incremental just means only copying data that is new, as the method automatically detects new records and skips old, previously ingested, records.

Recall that there are three methods to doing this

1.	SQL → COPY INTO

2.	Python → spark.readStream (Auto Loader with a timed trigger)

3.	Declarative Pipelines

1.	CREATE OR REFRESH STREAMING TABLE

What type of connectors do

#### COPY INTO

- Batch Ingestion. COPY INTO's upgrade/recommended alternative is CREATE STREAMING TABLE SQL

#### AUTO LOADER

- (Spark Declarative Pipeline) → Spark Structured Streaming → Supports Python and SQL (via Declarative Pipelines), handles schema evolution
- If a column is added via Autoloader's schema evolution feature (i.e. down the line), any old records will record NULL for the value (as the new column didn't exist for them), any new records populate correctly
  - For instance, if a new column called discount is added to a table, AUTO LOADER will add this column to the table, with a caveat.
  - (No Backfilling) Any previous records that did not have info for discount (i.e. null) will be set to null; new records will have the null column completed
- Best Possible Description of Autoloader: Auto loader monitors a source location, in which files accumulate, to identify and ingest only new arriving files. Files already ingested in previous runs are skipped.

### Streaming Ingestion

- If your data is a) still high volume but b) requires real-time loading, use streaming
- Caveat: Sometimes micro-batching, or loading small batches at very short, frequent intervals is used in lieu of streaming ingestion
  - Micro-batching is used when you don't want to overtax your system; i.e. get more system efficiency in exchange for a higher latency

### Ingestion Methods at a Glance

Feature, CREATE TABLE AS (CTAS) + spark.read, COPY INTO, Auto Loader

#### Ingestion Methods

- Standard Connectors → Support multiple ingestion methods…
  - Batch
    - Reprocess all records with each run
    - CREATE TABLE AS SELECT (CTAS)
    - SPARK.READ
  - Incremental Batch
    - Only new data ingested
    - Previously loaded records are skipped
    - Faster and efficient ingestion
    - SQL COPY INTO
    - SPARK.readstream & autoloader
    - SDP:  Spark Declarative Pipeline
  - Streaming
    - Continuously load micro batches of data at very short, frequent intervals
    - Ideal for streaming sources such as
    - Kafka, Amazon Kinesis, Google Pub/Sub

#### Long story short

- If you have one-time, smaller, and ad-hoc request → Batch
- If you have a repeatable, larger dataset → Incremental Batch. Why? Because you can isolate only the new records, so its more efficient on your clusters
- If you have a repeatable, larger dataset that needs to be updated in near real-time, a streaming table

Ok that covers the three main methods (batch, incremental batch and streaming) of connectors. Now let's move on to how the Delta Table itself works

## Delta Lake

### Delta Table

1.	Data is stored in Delta tables

2.	Internally, these tables store data as files within a folder directory

Data is stored as parquet files in a directory, Delta Lake adds JSON-based delta logs alongside these files, and these delta logs track transactions/table versions

- **Quick ref:** Delta Lake = Parquet (data files) + JSON transaction log (`_delta_log/` folder, one JSON file per commit, periodic Parquet checkpoints). No XML anywhere in the format.

### Delta Table Key Features

- By making the "simple" addition of JSON metadata, you can suddenly do a lot more with a table
- You can "time travel" i.e. see historical versions of a table, and you solve the data concurrency issues
- ACID → Atomicity (writes cannot be half completed), consistency (must follow the schema of the datatable), isolation (two concurrent writes don't result in other users seeing half-finished work), and durability (if a write finishes, it is saved)

### Key Concept: Table Constraints

- Not NULL constraints
- CHECK constraint

You must ensure that no data violating the constraint is added

### Cloning Delta Lake Tables

- DEEP CLONE → Fully creates both data and metadata to target

```
CREATE TABLE table_2 
DEEP CLONE source_TABLE
//You can incrementally sync changes by re-running
```

- SHALLOW CLONE
- No data moves, just the metadata lists a second cloned table as existing

Cloning is made for testing code in development, to setup and test a new data table

### Views

- What is a view in Databricks → A virtual table with no physical table; it is just logical query against a data table

*[Image in the Notion page, not copied]*

This is where they sit on the catalog.schema.view tree

1.	Stored (classical) views

```
CREATE VIEW view_name
As query
```

- Only query is stored, but the view will persist even after the spark sessions is closed

1.	Temporary Views

```
CREATE TEMP VIEW view_name
AS query
```

- Only the query is stored, but the view disappears when the spark session (i.e. the compute is disattached). Regular temp views are attached to the notebook. Whereas Global Temp views are attached to a compute cluster.

New Spark Session Creation Times → What kills a temp view

- Opening a new notebook
- Detaching and reattaching compute cluster to notebook

#### Global Temporary Views

- Cluster-scoped view
- CREATE GLOBAL TEMP VIEW view_name AS query
- SELECT * FROM global_temp.view_name

Global Temps are attached to a compute cluster. So any notebook attached to that compute cluster can see the temporary view

- Cluster-scoped
- Dropped when cluster restarted

#### Materialized Views

- It is a stored delta table of an already-run query that can be refreshed by the user, but doesn’t have to

## Medallion Architecture

- Explains how data is taken from a raw state to usable

1.	Ingest data (ingest into a delta table using batch, incremental batch, or streaming) → Starting point for processing in Databricks

2.	Process and Improve Data Quality → You incrementally refine data to prepare it for each successive layer…

3.	Bronze Layer (Raw Data) → unprocessed, and oftentimes from multiple sources, this is raw data

4.	Silver Layer (Cleaned Data) → Transformed to the point that it is analysis ready

5.	Gold Layer (Business Ready Data) → Data used in BI, and advanced analytics

## Ingest Details

### How to Append Metadata Columns on Ingest

- As part of that ingestion, you can add specific metadata columns to each row in the table. These columns include info such as
  - Last modification time of the row
  - Source file name

#### Fields to Pull

- metadata.file_modification_time
- metadata.file_name

#### Conclusion

- Just know that hidden column exists called _metadata column, and that it is available for input file formats

### Working with the Rescued Data Column

- If a table listed peter and a cost of $100 (with the "$") being invalid as a numeric type,

| _c_users (STRING) | _c_cost (BIGINT) | _rescued_data |
|---|---|---|
| peter | null | {"cost":"$100","_file_path":"<file_path>"} |
| zebi | 300 | null |
| … | … | … |

- Notice how it returns a JSON-formatted string that contains what the cost column actually contained

#### What to remember

- If input data doesn't match an expected schema, just know that the values are captured in the hidden rescued_data column.
  - read_files(), spark.read or Autoloader can all feature the rescued_data column

### JSON Strings

- Just know you can use schema_of_json and from_json
- schema_of_json → automatically derive the schema from a sample JSON string
- from_json → parse a JSON string column into a STRUCT column using the derived schema
- VARIANT allows you to not need a fixed schema, while performing far better than storing the data as JSON string

### Data Ingestion with Partner Connect

- Just know you can connect directly via Databricks to their partners (Like Alteryx or Qlik)

## Writing to Tables

### INSERT OVERWRITE

```
INSERT OVERWRITE orders
SELECT * FROM parquet.`${dataset.bookstore}/orders`
```

Note that INSERT OVERWRITE, as far as means for writing to tables, is safer than the other methods, because it can only overwrite tables where the data matches the schema it is being written to

### MERGE INTO

```
MERGE INTO target_table target
USING source_table source
ON target.id = source.id 
WHEN MATCHED AND source.status = 'update' THEN 
    UPDATE SET
    target.email = source.email,
    target.status = source.status
WHEN MATCHED AND source.status = 'delete' THEN 
    DELETE
WHEN NOT MATCHED THEN
    INSERT (id, first_name, email, sign_up_date_status)
```

## Sidenotes

### Sidenote: What are clusters?

- They provide the compute power to run a notebook or job
- You configure how many workers (i.e. how much compute a cluster is given) and then the type of machine prior to running a job
- Clusters have two types → All-purpose clusters (for interactive notebook work) and job clusters (spun up just for a single job, then deactivated)

### Delta Tables vs External Unmanaged Tables

- DROP TABLE on an external table will only drop the table’s metadata, but the underlying data files are kept

### Managed Tables → Derar Hussein Videos →

- Video 16, 1:14 → You can use the command DESCRIBE EXTENDED managed_default and look in the Type column to see what kind of table it is

### Thing to understand about writing to tables

Basically, you have four options to write to tables

CREATE OR REPLACE TABLE

- Replace all data, but run risk of updating the schema too → Create or replace table
- Replace all data, but schema must match → INSERT OVERWRITE
- Add new rows, but don’t care if you accidentally make duplicate records → INSERT INTO
- Insert, update and delete records without creating duplicates (i.e. superior to INSERT INTO) → MERGE INTO

## 2.2 Writing to Tables

> 🤖 AI generated. Written by Claude from the lecture transcript and my 04OCT26 notebook comments. Not yet reviewed.

### Overview

- Delta tables give you ACID compliant updates, so writes either fully finish or don't happen at all
- Two things to know how to do here: overwrite a table (replace everything) and add or change records (append / merge)
- Demo uses the bookstore dataset. A helper notebook (Copy-Datasets) copies the data in first
- Setup → CTAS an orders delta table from the parquet files
  - Parquet has a well-defined schema, so the data comes out correctly without us specifying anything
  - Griffin Comment: Basically, overwrites

```
CREATE TABLE orders AS 
SELECT * FROM parquet.`${dataset.bookstore}/orders`
```

### Overwriting Tables

Why overwrite instead of deleting and recreating the table?

- The old version still exists → you can get the data back with Time Travel
- Much faster → doesn't need to list the directory recursively or delete any files
- Atomic → concurrent queries can still read the table while it is being overwritten
- ACID → if the overwrite fails, the table stays in its previous state

#### Method 1: CREATE OR REPLACE TABLE

- Also known as a CRAS statement
- Fully replaces the content of the table every time it runs

```
CREATE OR REPLACE TABLE orders AS
SELECT * FROM parquet.`${dataset.bookstore}/orders`
```

- Check the history with DESCRIBE HISTORY
  - Version 0 is the CREATE TABLE AS SELECT
  - The CREATE OR REPLACE generated a new table version

```
DESCRIBE HISTORY orders
```

#### Method 2: INSERT OVERWRITE

- Nearly identical output to CRAS → data in the target table is replaced by data from the query
- Differences
  - It can only overwrite an existing table, it can't create a new one (CREATE OR REPLACE can)
  - It can only overwrite with data that matches the current table schema
- Safer than the other method, as there is no risk of changing the table schema. You'd rather have it fail than see it rewrite a table with CREATE OR REPLACE

```
INSERT OVERWRITE orders
SELECT * FROM parquet.`${dataset.bookstore}/orders`
 
-- overwrites the records in the orders table with the records coming from the parquet files
-- the output reports the number of affected rows and inserted rows (2150 in the demo)
```

- In DESCRIBE HISTORY it shows up as a new version
  - Operation → WRITE (i.e. it is writing over the existing table)
  - Mode → Overwrite
- Try overwriting with a different schema (here, adding a current_timestamp column)

```
INSERT OVERWRITE orders
SELECT *, current_timestamp() FROM parquet.`${dataset.bookstore}/orders`
 
-- fails: INSERT OVERWRITE can't overwrite a table if the schema of the new data doesn't match
```

- Exception → schema mismatch detected when writing to the Delta table

**Quick ref:** The main difference between the two is schema enforcement on write. INSERT OVERWRITE enforces the table schema. CREATE OR REPLACE TABLE doesn't, it replaces the whole table including the schema.

|  | CREATE OR REPLACE TABLE | INSERT OVERWRITE |
|---|---|---|
| Can create a new table | Yes | No, table must already exist |
| Schema must match | No | Yes |
| Shows in history as | A new version (CREATE OR REPLACE) | A new version (WRITE, mode Overwrite) |

### Appending Data

#### INSERT INTO

- The easiest method for adding new records
- Here the input query reads the parquet files in the orders-new directory

```
INSERT INTO orders
SELECT * FROM parquet.`${dataset.bookstore}/orders-new`
```

- Added 700 new records, so the table now has 2850 orders

```
SELECT count(*) FROM orders
```

- Simple and efficient, but has no built-in guarantee against inserting the same records twice
  - Re-running the query writes the same records again → duplicates
- Fix for this → MERGE INTO

### Merging Data

- MERGE INTO lets you upsert data from a source table, view, or dataframe into a target table
  - i.e. you can insert, update and delete in one statement
- Inserts, updates and deletes are all completed in a single atomic transaction
- Great for avoiding duplicates when inserting records

#### Example 1: Customers

- Goal → update customers with new emails, and add new customers
- First create a temp view of the new customer data, then merge into customers on the customer_id key
- Two actions
  - WHEN MATCHED → update, but only if the current row has a null email and the new record does not. Also updates the last updated timestamp
  - WHEN NOT MATCHED → insert the new record (customer_id isn't in the table yet)

```
CREATE OR REPLACE TEMP VIEW customers_updates AS 
SELECT * FROM json.`${dataset.bookstore}/customers-json-new`;
 
MERGE INTO customers c
USING customers_updates u
ON c.customer_id = u.customer_id
WHEN MATCHED AND c.email IS NULL AND u.email IS NOT NULL THEN
  UPDATE SET email = u.email, updated = u.updated
WHEN NOT MATCHED THEN INSERT *
```

- Result → 100 records updated, 201 inserted, 0 deleted

#### Example 2: Books

- Goal → insert new books coming in as CSV files, but only the computer science ones
- Temp view over the CSV files first

```
CREATE OR REPLACE TEMP VIEW books_updates
   (book_id STRING, title STRING, author STRING, category STRING, price DOUBLE)
USING CSV
OPTIONS (
  path = "${dataset.bookstore}/books-csv-new",
  header = "true",
  delimiter = ";"
);
 
SELECT * FROM books_updates
 
-- creates or replaces a temporary view called books_updates
-- a temp view only saves the query, it does not store any data. the query re-runs every time you use the view
```

- Then MERGE INTO with only a NOT MATCHED condition
  - Only inserts a record if it does not already exist, based on the key (book_id and title)
  - Also checks the category is Computer Science

```
MERGE INTO books b
USING books_updates u
ON b.book_id = u.book_id AND b.title = u.title
WHEN NOT MATCHED AND u.category = 'Computer Science' THEN 
  INSERT *
```

- 5 new books in the view, only 3 inserted (the 3 computer science ones)
- Re-running the statement inserts 0 records, as they are already in the table → this is the main benefit of MERGE, no duplicates

### What to Remember

- Replace all data and the schema can change → CREATE OR REPLACE TABLE
- Replace all data, but the schema must match → INSERT OVERWRITE
- Add new rows (re-running creates duplicates) → INSERT INTO
- Insert, update and delete without creating duplicates → MERGE INTO

## 2.3 Advanced Transformations

### JSON strings: colon syntax

> "Spark SQL has built-in functionality to directly interact with JSON data stored as strings. We can simply use the colon syntax to traverse nested data structures."

In customers, `profile` is a plain string that holds JSON. DESCRIBE shows its type as `string`.

Use a colon to read a field inside a JSON string. Chain colons to go deeper.

```
%sql
SELECT customer_id, profile:first_name, profile:address:country
FROM customers
```

- Returns one row per customer, with the first name and country pulled out.
- No schema needed. Spark reads the text on the fly.

### from_json and schema_of_json

> "However, if we run this, it will fail. Because this function requires the schema of the JSON object. Fortunately, we can derive the schema from our current data."

`from_json` turns a JSON string into a struct. A struct is Spark's own nested type, with named sub-fields.

`from_json(profile)` alone fails. It needs a schema as the second argument.

Get the schema from a sample row. Pick one with no null fields, then pass it to `schema_of_json`.

> "For this we need a sample data of our JSON value with non null fields."

```
%sql
CREATE OR REPLACE TEMP VIEW parsed_customers AS
  SELECT customer_id,
    from_json(profile, schema_of_json('{"first_name":"Thomas","last_name":"Lane","gender":"Male","address":{"street":"06 Boulevard Victor Hugo","city":"Paris","country":"France"}}')) AS profile_struct
  FROM customers;
```

- DESCRIBE parsed_customers shows `profile_struct` as a struct. `address` inside it is also a struct.
- Why a no-null sample: a null field gives Spark no type to infer for it.

### Struct: dot syntax and star

> "With struct type, we can interact with the subfields using standard period or dot syntax instead of the colon syntax we use for the JSON string."

Once it is a struct, use dots, not colons.

```
%sql
SELECT customer_id, profile_struct.first_name, profile_struct.address.country
FROM parsed_customers
```

`.*` splits a struct into one column per field.

> "Once a json string is converted to a struct type, we can use the star operation to flatten fields into columns."

```
%sql
CREATE OR REPLACE TEMP VIEW customers_final AS
  SELECT customer_id, profile_struct.*
  FROM parsed_customers;
```

- Result columns: customer_id, first_name, last_name, gender, address.
- Only one level is flattened. `address` stays a struct.

### Arrays: explode

> "The most important one is the explode function that allows us to put each element of an array on its own row."

In orders, `books` is an array of structs. Each struct holds book_id, quantity, and subtotal.

`explode` puts each array element on its own row. The other columns repeat on every new row.

```
%sql
SELECT order_id, customer_id, explode(books) AS book
FROM orders
```

- An order with 2 books becomes 2 rows. In the video: "this order has now two rows instead of one."
- More rows out than in.

### collect_set, flatten, array_distinct

> "Another interesting function is the collect_set aggregation function that allows us to collect unique values for a field, including fields within arrays."

`collect_set` is an aggregate. It gathers the unique values of a column into one array per group.

```
%sql
SELECT customer_id,
  collect_set(order_id) AS orders_set,
  collect_set(books.book_id) AS books_set
FROM orders
GROUP BY customer_id
```

- `books.book_id` on an array returns an array of book IDs per order.
- So `books_set` is an array of arrays. In the video: "the books_set column is actually an array of array."

> "So instead of having the B08 twice after flatten the array, we will get only one unique value."

Fix it in two steps:

1.	`flatten` merges the inner arrays into one array.

2.	`array_distinct` removes repeats, like a second B08.

```
%sql
SELECT customer_id,
  collect_set(books.book_id) AS before_flatten,
  array_distinct(flatten(collect_set(books.book_id))) AS after_flatten
FROM orders
GROUP BY customer_id
```

### Joins

> "Here we are joining the result of the explode operation to the books lookup table in order to retrieve books information like book's title, and author's name."

Spark SQL supports inner, outer, left, right, anti, cross, and semi joins.

Here the exploded orders join the books table to add title, author, and category.

```
%sql
CREATE OR REPLACE VIEW orders_enriched AS
SELECT *
FROM (
  SELECT *, explode(books) AS book
  FROM orders) o
INNER JOIN books b
ON o.book.book_id = b.book_id;
```

- Explode first, so each row has one book_id to match on.
- `o.book.book_id` reads book_id from the exploded struct.
- This is a stored view, not a temp view. It lasts past the session.

### Set operations

> "The Intersect Command returns all rows found in both relations."

Both sides must have the same columns in the same order.

| Operation | Returns |
|---|---|
| UNION | Rows in either side, duplicates removed |
| UNION ALL | Rows in either side, duplicates kept |
| INTERSECT | Rows found in both sides |
| MINUS (same as EXCEPT) | Rows in the first side but not the second |

```
%sql
SELECT * FROM orders
INTERSECT
SELECT * FROM orders_updates
```

- INTERSECT returns 700 rows. In the video: "we have 700 records because we have already inserted these updates in the orders table in a previous notebook."
- So `orders MINUS orders_updates` returns orders without those 700.

### Pivot

> "Spark SQL also support pivot clause, which is used to change data perspective."

PIVOT turns the values of one column into new columns, with an aggregate in each cell.

It goes after the table name or subquery in FROM. In the video: "The pivot table can be specified after the table name or subquery."

```
%sql
CREATE OR REPLACE TABLE transactions AS
SELECT * FROM (
  SELECT customer_id, book.book_id AS book_id, book.quantity AS quantity
  FROM orders_enriched
) PIVOT (
  sum(quantity) FOR book_id IN (
    'B01','B02','B03','B04','B05','B06',
    'B07','B08','B09','B10','B11','B12'
  )
);
```

Read the PIVOT clause in three parts:

1.	`sum(quantity)`: the aggregate and the column it adds up.

2.	`FOR book_id`: the column whose values become new columns.

3.	`IN (...)`: the exact values to turn into columns.

- Result: one row per customer, one column per book, total quantity in each cell.
- A book the customer never bought shows null.
- Useful for dashboards and machine learning input.

> "Such a flatten data format can be useful for dashboarding, but also useful for applying machine learning."

### Serverless fixes

One cell breaks on your workspace. It uses `${dataset.bookstore}`, which serverless does not fill in.

Use the full volume path instead:

```
%sql
CREATE OR REPLACE TEMP VIEW orders_updates AS
SELECT * FROM parquet.`/Volumes/demoworkspace/default/bookstore_dataset/orders-new`;
```

- The `orders-new` folder name comes from the course. Not yet checked in your volume.
- Every other cell should run as written.

### Exam traps

| If you see | Remember |
|---|---|
| Colon vs dot | Colon is for JSON strings. Dot is for structs. |
| from_json with one argument | Fails. It needs a schema. |
| explode | Adds rows. One row per array element. |
| flatten | Does not add rows. Merges nested arrays into one array. |
| collect_set vs collect_list | collect_set drops repeats. collect_list keeps them. |
| UNION vs UNION ALL | UNION drops duplicates. UNION ALL keeps them. |
| MINUS | Same as EXCEPT. |
| PIVOT without IN list | The IN list names the new columns. |

## 2.4 Higher Order Functions and SQL UDFs

### Higher order functions

> "Higher order functions allow you to work directly with hierarchical data like arrays and map type objects."

In orders, `books` is an array of structs. To work inside the array without exploding it, use a higher order function.

A higher order function takes a lambda as input. A lambda is a small inline rule, written `x -> rule`.

### FILTER

> "One of the most common higher order functions is the filter function which filters an array using a given lambda function."

`FILTER` keeps only the array elements that pass the rule.

```
%sql
SELECT
  order_id,
  books,
  FILTER (books, i -> i.quantity >= 2) AS multiple_copies
FROM orders
```

- `i` stands for one book in the array.
- Returns one row per order. `multiple_copies` holds only books bought 2 or more times.
- Row count does not change. Orders with no match get an empty array.

> "However, as you can see, we are creating a lot of empty arrays in this new column."

### Removing empty arrays

> "We can accomplish that by using a subquery, which is a query within another query in order to apply the WHERE clause on the size of the returned column."

```
%sql
SELECT order_id, multiple_copies
FROM (
  SELECT
    order_id,
    FILTER (books, i -> i.quantity >= 2) AS multiple_copies
  FROM orders)
WHERE size(multiple_copies) > 0;
```

- `size` returns the number of elements in an array.
- Why a subquery: WHERE cannot use a column alias made in the same SELECT. The subquery creates `multiple_copies` first.

### TRANSFORM

> "Our second higher order function is the transform function that is used to apply a transformation on all the items in an array and extract the transformed value."

`TRANSFORM` runs the rule on every element and returns a new array of results.

```
%sql
SELECT
  order_id,
  books,
  TRANSFORM (
    books,
    b -> CAST(b.subtotal * 0.8 AS INT)
  ) AS subtotal_after_discount
FROM orders;
```

- Applies a 20% discount to each book's subtotal.
- The new array has the same length as `books`.
- FILTER can shrink an array. TRANSFORM never does.

### SQL UDFs

> "User defined functions or UDFs, which allow you to register a custom combination of SQL logic as function in a database, making these methods reusable in any SQL query."

> "At minimum, it requires a function name, optional parameters, the type to be returned, and some custom logic, of course."

```
%sql
CREATE OR REPLACE FUNCTION get_url(email STRING)
RETURNS STRING
RETURN concat("https://www.", split(email, "@")[1])
```

- `split(email, "@")` breaks the email into two parts.
- `[1]` takes the second part, the domain. In the video: "knowing that the split function is returning a zero-indexed list."
- `concat` adds `https://www.` in front.

```
%sql
SELECT email, get_url(email) domain
FROM customers
```

### UDFs are saved objects

> "User defined functions are permanent objects that are persisted to the database, so you can use them between different Spark sessions and notebooks."

```
%sql
DESCRIBE FUNCTION get_url
```

> "With Describe Function command, we can see where it was registered and basic information about expected inputs and the expected return type."

```
%sql
DESCRIBE FUNCTION EXTENDED get_url
```

> "For example, the Body field at the bottom shows the SQL logic used in the function itself."

- In the video, the function sits in the default database. On Unity Catalog it is saved in a catalog and schema, for example `demoworkspace.default`.

### UDF with CASE WHEN

> "Here we are applying the standard SQL Case When statements in order to evaluate multiple condition statements within our function."

```
%sql
CREATE FUNCTION site_type(email STRING)
RETURNS STRING
RETURN CASE
          WHEN email like "%.com" THEN "Commercial business"
          WHEN email like "%.org" THEN "Non-profits organization"
          WHEN email like "%.edu" THEN "Educational institution"
          ELSE concat("Unknow extenstion for domain: ", split(email, "@")[1])
       END;
```

```
%sql
SELECT email, site_type(email) as domain_category
FROM customers
```

- `like "%.com"` matches any email ending in .com. `%` means any characters.
- This uses `CREATE FUNCTION`, not `CREATE OR REPLACE`. Running it twice fails because the function already exists.

> "And remember, everything is evaluated natively in Spark. And so it's optimized for parallel execution."

### Clean up

```
%sql
DROP FUNCTION get_url;
DROP FUNCTION site_type;
```

### Your notebook: materialized view cell

Your copy has an extra materialized view in the FILTER cell. It will fail as written for two reasons:

1.	A second, unnamed `FILTER` line follows `multiple_copies` with no comma before it.

2.	Two statements share one cell. Give the view its own cell.

Fixed version:

```
%sql
CREATE OR REPLACE MATERIALIZED VIEW mv1 AS
SELECT
  order_id,
  books,
  FILTER (books, i -> i.quantity >= 2) AS multiple_copies
FROM orders
```

### Exam traps

| If you see | Remember |
|---|---|
| FILTER vs WHERE | FILTER removes array elements. WHERE removes rows. |
| FILTER vs TRANSFORM | FILTER keeps some elements. TRANSFORM changes every element. |
| explode vs FILTER | explode adds rows. FILTER keeps the same rows. |
| WHERE on a new alias | Not allowed in the same SELECT. Use a subquery. |
| split(...)[1] | Arrays start at 0. Index 1 is the second element. |
| UDF lifespan | Saved in the schema. Works across sessions and notebooks. |
| See a UDF's code | DESCRIBE FUNCTION EXTENDED, Body field. |
| Minimum UDF parts | Name, optional parameters, return type, logic. |

## 3.1 Structured Streaming

### Pyspark Syntax for A Streaming Table

This is the query that is used to create a streaming table

### Triggers (how the stream processes new data)

- processingTime = how often batches runs. e.g. (processingTime = '4 seconds'; runs a small batch every 4 seconds, continues until cancelled
- availableNow = processes all data, in several small batches, then stops on its own. Stream that runs once and stops.
- Default settings -> ProcessingTime is set to 0-0.5seconds

### outputMode (what gets written each batch)

- append -> only add new rows
- complete -> rewrites the whole result table each batch -> aggregates like the author counts

### checkpointLocation

- A folder where the stream saves its progress: which data it has processed, plus running totals. It is not a view.
- If the stream restarts, it resumes from this folder.
- Each stream needs its own folder.

```
query = (aggregated_df.writeStream
         .format("delta")  # write to a Delta table
         # no .trigger() -> default trigger, runs a batch every 0-0.5 seconds until cancelled
         .outputMode("complete")  # aggregated result, so rewrite the whole table each batch
         .option("checkpointLocation", checkpoint_path)  # folder where progress is saved; one folder per stream
         .toTable("main.streaming_examples.target_summary"))  # target table
```

---
[Back to contents](../README.md)
