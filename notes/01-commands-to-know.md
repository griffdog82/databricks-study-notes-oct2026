# Commands to Know

*Quick-reference page (no order, status or type set in Notion)*

## Commands to Memorize — Databricks Data Engineer Associate

### Convert External Table to Managed Table

ALTER TABLE catalog_name.schema_name.my_external_table SET MANAGED;

### Streaming Tables (Lakeflow / Spark Declarative Pipelines)

**Create a streaming table — use** `REFRESH`**, not** `REPLACE`**:**

```
CREATE OR REFRESH STREAMING TABLE catalog.schema.table_name
AS
SELECT * FROM STREAM(source_table);
```

`REPLACE TABLE` is not supported on streaming tables. This is a common trap answer.

**Reading with** `read_files`** (a function, not a table name):**

```
FROM STREAM read_files(
  '/Volumes/catalog/schema/volume/',
  format => 'CSV'
)
```

No parens around `read_files` itself when using `STREAM` this way.

**Reading an existing table (both forms valid):**

```
FROM STREAM(table_name)
FROM STREAM table_name
```

**Block a full refresh:**

```
TBLPROPERTIES ("pipelines.reset.allowed" = "false")
```

### Constraints (Data Quality)

```
CREATE OR REFRESH STREAMING TABLE table_name
(
  CONSTRAINT check_name EXPECT (condition)
  -- optional: ON VIOLATION DROP ROW | ON VIOLATION FAIL UPDATE
)
AS SELECT ...
```

No `ON VIOLATION` = bad rows kept, just logged.

`DROP ROW` = bad rows removed.

`FAIL UPDATE` = whole pipeline run stops.

### Parameters vs Config

```
-- Pipeline Configuration value (has $)
WHERE region = '${region_param}'
 
-- Named parameter marker (no $, just colon)
WHERE region = :region_param
```

`:param` doesn't work everywhere yet (e.g. blocked in `CREATE VIEW`).

### Null-Safe Comparison

```
ON a.id <=> b.id
```

Regular `=` never matches two NULLs. `<=>` does.

### Safe Casting

```
SELECT TRY_CAST('100$' AS INT);  -- returns NULL, not an error
```

`TRY_` prefix = fail safely, return NULL, instead of throwing an error.

### File Compaction

```
OPTIMIZE table_name;
OPTIMIZE table_name ZORDER BY (column_name);
```

Merges small files into larger ones. `ZORDER` also sorts by a frequently-queried column.

### PySpark Deduplication

```
df.dropDuplicates(["column1", "column2"])
```

Must be a list, even for one column. `df.dropDuplicates("col")` errors.

### Databricks Asset Bundles (DABs) CLI

```
databricks bundle init      # scaffold a new bundle project
databricks bundle validate  # check config before deploying
databricks bundle deploy    # push bundle to a workspace
databricks bundle run       # run a job/pipeline in the bundle
databricks bundle destroy   # tear down what was deployed
```

### Auto Loader — Cost/Latency Setting

```
.option("cloudFiles.useNotifications", "true")
```

Event-based file detection instead of repeated directory listing. Cuts storage API calls at high file-arrival volume.

### Row Filters (Row-Level Security)

```
CREATE FUNCTION us_filter(region STRING)
RETURN IF(IS_MEMBER('admin'), true, region = 'US');
 
ALTER TABLE sales SET ROW FILTER us_filter ON (region);
```

The function returns a **boolean** — `true` keeps the row, `false` hides it.

`IS_MEMBER('group')` checks if the current user belongs to that group — used to give admins an unrestricted view while others get filtered.

One filter function per table, attached with `SET ROW FILTER`.

### Column Masks (Column-Level Masking)

```
CREATE FUNCTION ssn_mask(ssn STRING)
RETURN CASE WHEN IS_MEMBER('admin') THEN ssn
            ELSE '*****'
       END;
 
ALTER TABLE users ALTER COLUMN ssn SET MASK ssn_mask;
```

Masking swaps the column's displayed value, not the stored value — non-admins see redacted output on every query.

Attached with `ALTER COLUMN ... SET MASK`, not `SET ROW FILTER` (that's the table-level command for row filters).

**Row filters vs. column masks vs. Dynamic Views** — all three can produce the same restricted result, but:

- Dynamic Views need a new view per access pattern.
- Row filters and column masks are UDFs attached directly to the base table — no extra views to maintain.

### Alter Table

```
ALTER TABLE infra_catalog.ops_db.device_logs SET MANAGED;
```

This command converts an existing external Unity Catalog table into a managed table while preserving:

- The table history (allowing for continuous time travel), and
- The same table configurations, including table name, Unity Catalog permissions, tags, properties, and associated views. So, it avoids the manual overhead of rebuilding the table metadata.

### Test Question - Granting Access to a user group

```
GRANT SELECT ON TABLE bank_catalog.fraud_schema.alert_cases TO analyst_group;
```

Say the user group analyst_group still returns “insufficent permissions” after attempting to query the table

Two causes

- analyst_group must be added via USE SCHEMA privilege on fraud_schema
- analyst)group does not have the USE CATALOG privilege on bank_catalog

### Pyspark Question: Dropping Duplicates

> ❓ A data engineer is working with a PySpark DataFrame df that contains online store visits. Each row represents a customer viewing a product page, with several columns including: user_id, product_id, and timestamp.

> They want to ensure uniqueness at the product-view level, meaning each user should only have one record per product.

> Which of the following PySpark code correctly implements this logic?

Answer: **df.dropDuplicates(["user_id", "product_id"])**

Why: dropDuplicates/distinct (in SQL) will only consider rows to be duplicates if they have identical values in the specified columns. It keeps the first occurrence it encounters, then keeps the rest

――――――――――――――――――――

Metastores

- catalog > schema >table/view/volume
- Generally, catalog data is the place to begin, uh, access controls.

Volumes

- Can store any file type - structured, semi-structured, and unstructured data
- Data in a volume cannot be registered as a table or queried like one - they are simply files
- Managed volumes - Unity Catalog handles teh storage location
- External - registeered against an existing directory/external location you control

Physically Separating Data

---
[Back to contents](../README.md)
