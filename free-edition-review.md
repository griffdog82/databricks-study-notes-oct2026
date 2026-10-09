# Free Edition vs Azure: hands-on review

Exam: Databricks Certified Data Engineer Associate, 2026-10-11.
Reviewed: 2026-10-07.
Library: `study/videos/` (serverless-mode branch of Derar's course), `study/archive/`, `study/context/`. `exam-master.md` and `quizlet/cards.txt` have no hands-on steps, so they are not rated.

How this was checked: I read every code cell and every lab instruction. I did not run anything in Databricks. Verdicts come from the exact lines in the notebooks, checked against these pages:

- Free Edition limits: https://docs.databricks.com/aws/en/getting-started/free-edition-limitations
- Serverless limits: https://docs.databricks.com/aws/en/compute/serverless/limitations
- Serverless Spark configs: https://docs.databricks.com/aws/en/spark/conf
- LIVE schema (legacy): https://docs.databricks.com/aws/en/ldp/live-schema
- Free Edition and S3 external locations: https://community.databricks.com/t5/data-engineering/azure-not-getting-listed-in-create-external-location/m-p/131747 and https://dev.classmethod.jp/articles/databricks-free-edition-dataload-from-s3/ (July 2025 walkthrough)

Key doc lines used below:

- Free Edition: "outbound internet access is restricted to a limited set of trusted domains." Unsupported: "Custom workspace storage locations." "No access to the account console or account-level APIs."
- Serverless: "The Spark UI is not available." "Spark logs are not available." "Global temp views are not supported." "Dataframe and SQL cache APIs are not supported." "Only Spark Connect APIs are supported. Spark RDD APIs are not supported." Compute policies, instance pools, init scripts, and compute-scoped libraries are not supported. Streaming triggers: only `AvailableNow` and `Once`.
- Serverless Spark configs: "Serverless compute does not support setting most Spark properties." Allowed list: `spark.databricks.execution.timeout`, `spark.sql.legacy.timeParserPolicy`, `spark.sql.session.timeZone`, `spark.sql.shuffle.partitions`, `spark.sql.ansi.enabled`, `spark.sql.files.maxPartitionBytes`.

---

## 1. Verdict table

Verdict key: **As-is** = runs on serverless as-is. **Modify** = runs with modification. **Azure** = needs an Azure cluster. **Unclear** = could not confirm; the row says what to check.

### Shared setup (used by most notebooks)

| # | Document and section | What it requires | Verdict | Reason (exact line or setting) | Workaround |
|---|---|---|---|---|---|
| S1 | `Includes/Copy-Datasets` (run by 2.1 to 5.2) | Copy the bookstore dataset from a public S3 bucket into a UC volume | Unclear | `data_source_uri = "s3://dalhussein-courses/datasets/bookstore/v1/"` then `dbutils.fs.cp(source_path, target_path, True)`. Free Edition limits outbound internet to trusted domains, and the docs do not list which. Also: if `SHOW CATALOGS` lists `hive_metastore`, the script switches to `dbfs:/mnt/demo-datasets/bookstore`, which will not work on serverless. | Check: run `dbutils.fs.ls("s3://dalhussein-courses/datasets/bookstore/v1/")` once in Free Edition. Your other workspace already has `/Volumes/demoworkspace/default/bookstore_dataset`, so the data exists. If the copy fails, download the files once and use "Upload to volume" in Catalog Explorer. |
| S2 | `Includes/Setup-Lab` (run by the labs) | Copy the school dataset from public S3 | Unclear | Same as S1: `data_source_uri = "s3://dalhussein-courses/datasets/school/v1/"`. Also `clean_up()` uses `checkpoint_path` and `dlt_path`, which are never set in the Unity Catalog branch. It only runs if the `clean` widget is 1. | Same check as S1. Do not set `clean=1`. |
| S3 | `${...}` placeholders in SQL cells (2.1, 2.2, 2.3, 2.1L, archive exercises) | Widget value substituted into a SQL path | Modify | Cells like ``SELECT * FROM json.`${dataset_bookstore}/customers-json` ``. In your workspace this gave "Missing cloud file system scheme". The serverless page says Hive variables are not supported. | Replace `${dataset_bookstore}` with the full path, for example `/Volumes/<catalog>/default/bookstore_dataset`. |

### Section 1: Platform and Delta Lake

| # | Document and section | What it requires | Verdict | Reason (exact line or setting) | Workaround |
|---|---|---|---|---|---|
| 1 | `Extra/1.0 - Creating Clusters` | Create an all-purpose cluster in the UI | Azure | "Under **All-purpose compute** tab, click **Create compute**." Settings: "Single node", "Databricks runtime version 13.3 LTS", "Node type of 4 cores", "auto termination ... 30 minutes". Free Edition is serverless only; custom compute is not supported. | Read only on Free Edition. Do it in Azure session A. |
| 2 | `Extra/1.0L` and `1.0L Solution - Creating Clusters` | Same cluster, as a lab | Azure | Same table of settings as row 1. | Azure session A. |
| 3 | `1.1 Notebook Basics`: print, `%sql`, markdown, `%run ../Includes/Setup`, `dbutils.help()` | Basic notebook features | As-is | No compute settings, no restricted APIs. | None needed. |
| 4 | `1.1 Notebook Basics`: `%fs ls '/databricks-datasets'` and `dbutils.fs.ls('/databricks-datasets')` | List the sample datasets folder | Unclear | Serverless page: "Access to DBFS is limited." The docs do not say whether `/databricks-datasets` is readable on Free Edition. | Check: run the cell. If it fails or is empty, run `dbutils.fs.ls` on your bookstore volume instead. Same skill. |
| 5 | `Lab 1.1L Notebook Basics` | Rename notebook, attach cluster, Python and SQL cells, markdown, `%run ./helper` | Modify | Q2: "Attach the cluster you created previously to this notebook." There is no cluster on Free Edition. | Pick **Serverless** in the compute dropdown. Everything else runs. |
| 6 | `1.2 Understanding Delta Tables` | CREATE, INSERT, UPDATE, DESCRIBE DETAIL, DESCRIBE HISTORY | As-is | The `%fs ls` cells are already commented out. The rest is plain SQL on a managed table. | None needed. |
| 7 | `1.2` and `1.3`: the commented `--%fs ls '/path/to/employees'` and `_delta_log` cells | Look at the table's data files and log files | Modify | `1.4` states: "It is not permitted to list the files of managed tables." This is a Unity Catalog rule, so a cluster does not fix it for UC managed tables. | Make an external Delta table on your own S3 external location (see row 12), then `LIST 's3://<bucket>/<path>/_delta_log'`. For the exam, `DESCRIBE DETAIL` and `DESCRIBE HISTORY` cover the same facts. |
| 8 | `1.3 Advanced Delta Lake Features`: time travel, `RESTORE`, `OPTIMIZE ... ZORDER BY`, `VACUUM` | Delta maintenance commands | As-is | The notebook already swaps the Spark config for `ALTER TABLE employees SET TBLPROPERTIES ('delta.deletedFileRetentionDuration'='interval 0 hours')`. Note from the notebook: "You may still see results due to a cached version of the table in the serverless compute environment." | None needed. |
| 9 | `1.3`: `SET spark.databricks.delta.retentionDurationCheck.enabled = false` | Turn off the VACUUM safety check with a Spark config | Azure | Notebook: "The retentionDurationCheck configuration is not available on Serverless compute." The property is not in the serverless allowed list. | Azure session A, one cell. This setting is a common exam question. |
| 10 | `Lab 1.2L Delta Lake` | CREATE OR REPLACE, INSERT, UPDATE, DELETE, DESCRIBE HISTORY, DESCRIBE DETAIL | As-is | Plain SQL. `USE CATALOG hive_metastore` is commented out. | None needed. |
| 11 | `1.4 Databases and Tables`: managed tables and new schema in the default location | Managed tables, `CREATE SCHEMA`, `DESCRIBE EXTENDED`, `DROP TABLE` | Modify | The first cell is `CREATE CATALOG IF NOT EXISTS demo_cat MANAGED LOCATION 's3://<BUCKET>'`. It needs an external location. Free Edition does not support "Custom workspace storage locations." | Skip that cell. Run the managed-table cells in your own catalog. |
| 12 | `1.4`: external tables, `LOCATION 's3://<BUCKET>/external_storage/...'`, `CREATE SCHEMA custom MANAGED LOCATION 's3://<BUCKET>/custom_schemas'`, `%fs ls 's3://<BUCKET>/...'` | External tables and schemas in a custom location | Modify | Each needs an external location. Free Edition supports S3 only for this (community answer). A July 2025 walkthrough created a storage credential, external location, and external table on Free Edition with the user's own S3 bucket. | Needs your own AWS account: an S3 bucket, an IAM role, a storage credential, and an external location. S3 cost for this is cents. If you do not want an AWS account, read only; this is not a cluster problem, so Azure compute does not help by itself. |
| 13 | `Lab 1.3L`: Q1, Q4, Q5 (managed table, drop, new schema) | Same as row 11 | Modify | Same `CREATE CATALOG IF NOT EXISTS demo_cat MANAGED LOCATION 's3://<BUCKET>'` first cell. | Skip that cell. Use your own catalog. |
| 14 | `Lab 1.3L`: Q2, Q3, Q6 (external table, custom-location schema) | Same as row 12 | Modify | Same S3 need. Also the solution uses `CREATE SCHEMA cinema_custom LOCATION 's3://<BUCKET>/custom_schemas'`. Video 1.4 uses `MANAGED LOCATION` for this. | Own S3 bucket as in row 12. Use `MANAGED LOCATION`, as in video 1.4. |
| 15 | `1.5A` and `1.5B Views`: stored views, temp views, dropping views | Views and session scope | As-is | Plain SQL. To see a temp view disappear, open 1.5B in a new session. | None needed. |
| 16 | `1.5A` and `1.5B`: `CREATE GLOBAL TEMP VIEW`, `SHOW TABLES IN global_temp` | Global temp views | Azure | Notebook: "GLOBAL TEMPORARY VIEW is not supported on serverless compute." Serverless page: "Global temp views are not supported." | Azure session A: run 1.5A, then 1.5B in a second notebook on the same cluster. |

### Section 2: ELT with SQL and PySpark

| # | Document and section | What it requires | Verdict | Reason (exact line or setting) | Workaround |
|---|---|---|---|---|---|
| 17 | `2.1 Querying Files`: json, text, binaryFile, csv, `_metadata.file_path`, CTAS, `read_files` | Query files directly and build tables | Modify | Every SQL cell uses `${dataset_bookstore}` (row S3). `input_file_name()` is already commented out. | Use the full volume path. |
| 18 | `2.1`: commented cells `CREATE TABLE books_csv ... USING CSV ... LOCATION "${dataset_bookstore}/books-csv"`, then append and `REFRESH TABLE books_csv` (same steps in `context/2_1 ... Runtime_17_3`) | Non-Delta external table and the stale-cache lesson | Unclear | Needs an external location (row 12). The lesson's "count stays the same until REFRESH" comes from Spark caching. Serverless: "Dataframe and SQL cache APIs are not supported." The docs do not say whether serverless shows the same stale count. | Check: if you set up S3 (row 12), run it and see if count 2 equals count 1. If not, the lesson only shows on a classic cluster. Optional add-on in Azure. |
| 19 | `2.2 Writing to Tables`: CTAS, CREATE OR REPLACE, INSERT OVERWRITE, INSERT INTO, MERGE | Write and merge data | Modify | `${dataset_bookstore}` in SQL (row S3). | Full volume path. |
| 20 | `2.3 Advanced Transformations`: `:` syntax, `from_json`, explode, collect_set, flatten, joins, set ops, PIVOT | Nested data and reshaping | Modify | One cell: ``parquet.`${dataset_bookstore}/orders-new` `` (row S3). | Full volume path. |
| 21 | `2.4 Higher Order Functions and SQL UDFs`: FILTER, TRANSFORM, `CREATE FUNCTION` | HOFs and SQL UDFs | As-is | SQL cells only use tables. No `${...}` in SQL. | None needed. |
| 22 | `2.5 Data Transformations with PySpark` (video 37), all active cells | select, withColumn, dedupe, nulls, joins, `broadcast()`, `saveAsTable` | As-is | All DataFrame API calls. `broadcast()` is a join hint, not a config. | None needed. |
| 23 | `2.5`: `#spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 20 * 1024 * 1024)` | Change the broadcast join size limit | Azure | Not in the serverless allowed config list. | Azure session A. On Free Edition you can still set `spark.sql.shuffle.partitions`, which is allowed. |
| 24 | `Lab 2.1L Querying Files` | Parquet and JSON CTAS, CSV with `read_files` | Modify | `${dataset_school}` in SQL (row S3). | Full volume path. |
| 25 | `Lab 2.2L Advanced ETL` | `:` syntax, FILTER, SQL UDF | As-is | SQL on tables from 2.1L. No `${...}`. | None needed. |

### Section 3: Incremental processing

| # | Document and section | What it requires | Verdict | Reason (exact line or setting) | Workaround |
|---|---|---|---|---|---|
| 26 | `3.1 Structured Streaming` (video 38), active cells | readStream, `display(..., checkpointLocation=...)`, writeStream with `trigger(availableNow=True)` | As-is | `availableNow` is a supported serverless trigger. The "Unsupported Operations" cell (`orderBy` on a stream) is meant to fail. | None needed. |
| 27 | `3.1`: commented `.trigger(processingTime='4 seconds')`; same comment in `3.2` and `3.3` | A stream that keeps running and picks up new rows | Azure | Notebook: "Trigger type ProcessingTime is not supported for Serverless compute." Serverless page lists only `AvailableNow` and `Once`. | Azure session B. On Free Edition, the notebook suggests a pipeline in continuous mode instead (not checked against the Free Edition pipeline quota). |
| 28 | `3.2 Auto Loader` | `cloudFiles` with `availableNow`, land new files, DESCRIBE HISTORY | As-is | Uses `trigger(availableNow=True)` and Python f-strings, not `${...}`. | None needed. |
| 29 | `3.3 Multi-Hop Architecture` | Bronze, silver, gold with Auto Loader and `availableNow` | Modify | Active cell: `SELECT *, current_timestamp() arrival_time, input_file_name() source_file`. Your answer key notes `input_file_name()` is not supported from Runtime 17.3 LTS or on Unity Catalog. | Replace with `_metadata.file_path source_file`. |
| 30 | `Lab 3.1L Spark Structured Streaming` | Auto Loader view, streaming aggregate, `availableNow` write | As-is | Same patterns as rows 26 and 28. | None needed. |
| 31 | `Lab 3.2L Multi-Hop Architecture` | Bronze, silver, gold with `availableNow` | As-is | Same patterns. | None needed. |

### Section 4: Pipelines, jobs, Databricks SQL

| # | Document and section | What it requires | Verdict | Reason (exact line or setting) | Workaround |
|---|---|---|---|---|---|
| 32 | `4.1 Bookstore Pipeline (DLT)`: SQL and Python source files | Lakeflow pipeline with streaming tables, materialized views, expectations | As-is | Modern syntax: `CREATE OR REFRESH STREAMING TABLE`, `STREAM read_files(...)`, `from pyspark import pipelines as dp`. Free Edition allows "One active pipeline per pipeline type." | Set config key `dataset_path` in pipeline settings. Run SQL and Python versions one at a time. |
| 33 | `Lab 4.1L Delta Live Tables` | Build a pipeline from the lab notebook | Modify | Legacy syntax: `CREATE OR REFRESH STREAMING LIVE TABLE`, `cloud_files(..., map("cloudFiles.schemaLocation", ...))`, `LIVE.` prefixes. Docs: the `LIVE` prefix "is silently ignored" in the default publishing mode. I could not confirm that `cloud_files` with a manual schema location works on a serverless pipeline. Setting "Cluster policy: Leave it None" does not apply to serverless. | Rewrite in the video 4.1 style: `STREAMING TABLE` and `STREAM read_files(...)`, no schema location. |
| 34 | `4.2 Pipeline Results` | Query the gold table | Modify | `SELECT * FROM <catalog_name>.bookstore_etl.cn_daily_customer_books` has a placeholder. | Put in your catalog and the pipeline's target schema. |
| 35 | `4.3 Land New Data Task` | Notebook that lands a file, run as a job task | As-is | `%run ../Includes/Copy-Datasets` then `load_new_json_data()`. Depends on S1 only. | None needed. |
| 36 | `Lab 4.2L Jobs` | Two-task job: notebook then pipeline | Modify | Task 1: "Cluster: Select your cluster from the dropdown, under **Existing All Purpose Clusters**." None exist on Free Edition. Two tasks is under the cap of 5 concurrent tasks. | Choose **Serverless** for task 1. |
| 37 | `Lab 4.3L Databricks SQL` | SQL editor queries, bar and line charts, dashboard | Modify | Queries use `<Catalog>.de_associate_school...` placeholders. Free Edition gives "One SQL warehouse, limited to a 2X-Small cluster size," which is enough for this. The "Add to Dashboard" steps describe the old dashboard flow. I did not confirm the current UI. | Fill in the catalog. If "Add to Dashboard" is missing, build an AI/BI dashboard and add the two queries as datasets. |

### Section 5: Governance

| # | Document and section | What it requires | Verdict | Reason (exact line or setting) | Workaround |
|---|---|---|---|---|---|
| 38 | `5.1 Managing Permissions` | Create schema, table, view; GRANT to a group and a user; SHOW GRANTS | Unclear | `GRANT SELECT, MODIFY, CREATE TABLE ON SCHEMA hr_db TO hr_team` and `GRANT SELECT ON VIEW ... TO adam@example.com`. Both principals must exist. Free Edition has "No access to the account console." I could not confirm that a group made in workspace settings can be used in a Unity Catalog GRANT. | Check: Settings > Identity and access > Groups > add `hr_team`, then run the GRANT. If it errors, grant to your own email or to `account users` instead. The SQL is the same. |
| 39 | `5.2 Fine-Grained Access Control`: dynamic views, column mask, row filter | `is_account_group_member('admins_demo')`, `SET MASK`, `SET ROW FILTER` | As-is | Unity Catalog SQL. Without an `admins_demo` group you see the "not a member" result, which is the result the demo explains. Needs `customers_details` from 2.5. | To see both sides, try the group check from row 38. |
| 40 | `5.2`: ABAC section (`SET TAGS`, `CREATE OR REPLACE POLICY ... TO account users EXCEPT admins_demo`) | Tag-based masking and filtering policies | Unclear | The policies hard-code `ON SCHEMA workspace.default` and name `admins_demo` as a principal. The ABAC doc lists some parts as Beta. I could not confirm ABAC availability on Free Edition or whether a missing principal errors. | Check: change `workspace` to your catalog, create the group as in row 38, run it. If it is refused, read only. |

### Archive and context exercises

| # | Document and section | What it requires | Verdict | Reason (exact line or setting) | Workaround |
|---|---|---|---|---|---|
| 41 | `archive/Ex_2_1` and `Ex_2_1_Worked_Version`: Parts 1, 2, 3, 5 | Query files, temp view `USING CSV`, CTAS, `read_files` | Modify | Cells use `${dataset.bookstore}`, which came from the old setup script and is not set anywhere now. Task 1.5 runs `input_file_name()` on purpose; the error is the expected answer. | Full volume path. |
| 42 | `archive/Ex_2_1` Part 4 (external CSV table and stale cache) | `dbutils.fs.cp` to `<EXTERNAL-URL>`, CSV external table, append, `REFRESH TABLE` | Unclear | Same as row 18: needs an external location, and serverless cache behavior is not documented for this case. | Same as row 18. |
| 43 | `archive/Ex_2_2_Writing_to_Tables` | CREATE OR REPLACE, INSERT OVERWRITE, INSERT INTO, MERGE | Modify | `${dataset.bookstore}` in SQL. | Full volume path. |
| 44 | `archive/Ex_2_3_Advanced_Transformations` | JSON, arrays, joins, set ops, PIVOT | Modify | `${dataset.bookstore}` in SQL. | Full volume path. |
| — | `context/2_1`, `2_3`, `2_4` | Same notebooks as 2.1, 2.3, 2.4 | Not rated again | Same verdicts as rows 17, 18, 20, 21, 41, 42. | — |

### Exam topics with no notebook in the library

These come from the May 2026 exam guide and your blocker list. No file in the library practices them.

| # | Exam topic | What practice would need | Verdict | Reason (exact line or setting) | Workaround |
|---|---|---|---|---|---|
| G1 | Compute types: create or edit a cluster, node type, autoscaling, Photon, access mode | Classic compute UI | Azure | Free Edition is serverless only. | Azure session A. |
| G2 | Cluster policies, pools, init scripts, cluster-scoped libraries | Classic compute admin settings | Azure | Serverless: compute policies, instance pools, init scripts, and compute-scoped libraries are not supported. | Set them up while the cluster is off, then use them in session A. |
| G3 | Job cluster vs all-purpose cluster for a job | A job task on new job compute | Azure | Free Edition jobs run on serverless only. | Azure session B. |
| G4 | Spark UI: skew, shuffles, stages, join strategy | Spark UI | Azure | "The Spark UI is not available." | Azure session A. On Free Edition, the query profile is the nearest tool; I did not check how much it shows. |
| G5 | Driver and worker logs, cluster start failures, out-of-memory errors | Cluster logs and event log | Azure | "Spark logs are not available." No cluster to fail on Free Edition. | Azure session B. |
| G6 | `cache()`, `persist()`, `CACHE TABLE` | Spark cache | Azure | "Dataframe and SQL cache APIs are not supported." Low exam weight. | 5 minutes in session A, with the Spark UI Storage tab. |
| G7 | RDD APIs and `sparkContext` | Classic Spark APIs | Azure | "Spark RDD APIs are not supported." No library file uses them. Low exam weight. | Optional, 5 minutes in session A. |
| G8 | Scala or R cells | Other languages | Azure | Free Edition: "R and Scala" unsupported. No library file uses them. Not on this exam. | Skip. |
| G9 | Liquid clustering (`CLUSTER BY`) | SQL on a managed Delta table | As-is | Plain SQL. No compute setting involved. Predictive optimization is set at the account level, and Free Edition has no account console, so you can read but likely not change it (not confirmed). | Practice on Free Edition. |
| G10 | Job control flow (if/else, for each) and triggers (schedule, file arrival, table update) | Jobs UI | As-is | Jobs work on serverless. Keep for-each concurrency at 5 or less because of "Max of 5 concurrent job tasks." | Practice on Free Edition. |
| G11 | CI/CD: Git folders, Databricks CLI, Asset Bundles | Git folder in the workspace; CLI login from your PC | Unclear | Not listed as unsupported on the Free Edition page, but I found no page that confirms them. | Check: add a Git folder, then run `databricks auth login` and `databricks bundle validate` against Free Edition. If refused, use Azure (no cluster needed). |
| G12 | Lakeflow Connect managed connectors | A connector to an outside app or database | Unclear | Outside connections are limited to trusted domains; the list is not published. | Check the Data ingestion page in Free Edition. Exam questions are mostly about when to choose it. |
| G13 | JDBC to an outside database | Network access to a database | Unclear | Same trusted-domain limit. No library file does this. | Learn the concept. Azure classic cluster could do it if needed. |
| — | Mounts | `dbfs:/mnt/...` | Not needed | Only used in the `hive_metastore` branch of S1 and S2. Mounts are legacy; volumes replace them. | — |

---

## 2. Counts

47 library rows (S1 to S3, 1 to 44) and 13 exam-topic rows (G1 to G13). The "not rated" lines are not counted.

| Verdict | Library rows | Exam-topic rows | Total |
|---|---|---|---|
| Runs on serverless as-is | 15 (3, 6, 8, 10, 15, 21, 22, 25, 26, 28, 30, 31, 32, 35, 39) | 2 (G9, G10) | 17 |
| Runs with modification | 19 (S3, 5, 7, 11, 12, 13, 14, 17, 19, 20, 24, 29, 33, 34, 36, 37, 41, 43, 44) | 0 | 19 |
| Needs Azure cluster | 6 (1, 2, 9, 16, 23, 27) | 8 (G1 to G8) | 14 |
| Unclear | 7 (S1, S2, 4, 18, 38, 40, 42) | 3 (G11, G12, G13) | 10 |
| **Total** | **47** | **13** | **60** |

Main point: 34 of 47 library rows run on Free Edition as-is or with small edits. Only 6 need a cluster, and all 6 are short demos. Most edits are the same one: replace `${...}` with the full volume path.

---

## 3. Exam objectives with no serverless-compatible practice

From the May 2026 guide sections:

1. **Platform (6%)**: compute types, configuration, and cost. Creating and editing clusters, policies, pools, job clusters (rows 1, 2, G1 to G3).
2. **Troubleshooting and optimization (10%)**: Spark UI for skew and shuffles (G4); cluster start failures and memory errors, driver logs (G5).
3. **Transformation (22%), tuning part**: changing `spark.sql.autoBroadcastJoinThreshold` (row 23). `spark.sql.shuffle.partitions` can still be set on serverless.
4. **Ingestion (21%), streaming part**: Structured Streaming with `processingTime` or continuous triggers (row 27). `availableNow` works.
5. **Delta maintenance**: the `retentionDurationCheck` Spark config (row 9).
6. **Views**: global temp views (row 16).

Not confirmed either way, check on Free Edition first: CI/CD with Git folders, CLI, and bundles (G11); Lakeflow Connect (G12); group-based grants and ABAC (rows 38, 40).

---

## 4. Azure session plan

Assumption: your rate of about $0.70 per hour of single-node cluster time ($20 for 25 to 30 hours). Actual price depends on the VM size and pricing tier you pick.

### Before any session (cluster off, no compute cost)

Config-only steps can be set up while the cluster is off. Nothing here bills compute.

1. Create the Azure Databricks workspace. Pick the Premium tier if you want cluster policies; check this on the pricing tier screen, since I did not confirm which tier includes them.
2. Set a $20 budget alert in Azure Cost Management.
3. Import the course notebooks (Git folder or import).
4. Write a **cluster policy** JSON that fixes single node, one small node type, and auto-termination of 15 minutes.
5. Create an **instance pool** with min idle instances set to 0, so it holds no paid VMs.
6. Upload an **init script** to workspace files (for example, one that echoes a line to the log). Make a second, broken copy that ends in `exit 1` for the failure demo.
7. Write the **Spark configs** into the cluster form under Advanced options: `spark.databricks.delta.retentionDurationCheck.enabled false` and `spark.sql.autoBroadcastJoinThreshold 20971520`.
8. Build the job definition for session B (task: `4.3 - Land New Data Task`, compute: new job cluster from the pool).
9. Check: when you click **Create compute**, the cluster starts right away. Create it at the start of session A, not before. Editing a terminated cluster does not start it.
10. Check: if this workspace has `hive_metastore`, Copy-Datasets uses `dbfs:/mnt/demo-datasets/bookstore`. Confirm DBFS root is usable in the new workspace before the session.

### Session A: compute and Spark internals (about 1.5 hours)

Start the single-node cluster with the policy, init script, and Spark configs.

| Order | Item | Rows | Time |
|---|---|---|---|
| 1 | Cluster creation steps from 1.0 and 1.0L. Look at policy, node type, runtime, Photon, auto-termination | 1, 2, G1, G2 | 15 min |
| 2 | Run Copy-Datasets once | S1 | 5 min |
| 3 | 2.5 broadcast cells with the config. Open the Spark UI: compare broadcast join vs sort-merge join, read shuffle read and write | 23, G4 | 25 min |
| 4 | Make a skewed join (one key with most rows). Find the slow task in the Spark UI stage view | G4 | 15 min |
| 5 | 1.3 `SET spark.databricks.delta.retentionDurationCheck.enabled = false`, then `VACUUM RETAIN 0 HOURS` | 9 | 5 min |
| 6 | 1.5A global temp view, then 1.5B in a second notebook on the same cluster | 16 | 10 min |
| 7 | `df.cache()`, `CACHE TABLE`, Spark UI Storage tab. Optional `spark.sparkContext` and `.rdd` | G6, G7 | 10 min |
| 8 | Driver logs and the init script log line | G2, G5 | 5 min |

### Session B: streaming, jobs, failures (about 1 hour)

| Order | Item | Rows | Time |
|---|---|---|---|
| 1 | 3.1 with `trigger(processingTime='4 seconds')`. Insert rows, watch the table update. Stop the stream | 27 | 20 min |
| 2 | Run the job on a new job cluster from the pool. Compare to all-purpose compute in the run details | G3 | 20 min |
| 3 | Switch the cluster to the broken init script. Start it, read the start failure in the event log | G5 | 15 min |
| 4 | Terminate everything. Check that no cluster or pool VM is running | — | 5 min |

### Optional add-on (about 0.5 hours, only if the Free Edition checks fail)

- Rows 18 and 42: the non-Delta CSV table stale-cache lesson, on a classic cluster.
- G11: CLI and bundles, if Free Edition refuses them. This needs no cluster time.

### Total

About **2.5 hours** of cluster time, **3 hours** with the add-on. At $0.70 per hour that is about $2 to $3, well under $20. Auto-termination at 15 minutes limits the cost of a forgotten cluster.
