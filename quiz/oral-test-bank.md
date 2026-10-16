# Oral Test Bank: Databricks Data Engineer Associate

## Instructions for the quizzing assistant (read this first)

You are quizzing the user out loud. This file is your question bank.

1. Ask ONE question at a time. Read the question, then read every option slowly: "Option A: ... Option B: ..."
2. STOP and wait for the user's spoken answer. Do not hint. Do not read the answer or the reason before they answer.
3. Then say whether they were right. Give the one-line reason. If they were wrong, also give the "Trap" line (why the tempting answer fails).
4. If the user explains their thinking, compare it to the reason. Point out the exact gap in their logic.
5. Ask them to retry the same question later if they miss it. Keep a running score. Give it every 10 questions.
6. Part 1 comes first. These are topics the user got wrong before. Then Part 2 (real Udemy questions), then Part 3.
7. "Pick two" questions: the user must name both letters.
8. Code is spoken in plain words. Spoken symbols are shown in brackets where needed.
9. Keep your own talk short. No long lectures. Move on after the reason.

Names: Lakeflow Jobs = old Databricks Jobs. Lakeflow Spark Declarative Pipelines = old Delta Live Tables (DLT). Declarative Automation Bundles = old Databricks Asset Bundles. The exam may use either name.

---

# PART 1: Previously missed (drill these)

### M1. Array with empty and null values
A table has id 1 with tags a and b, id 2 with an empty array, and id 3 with a null array. You want one row per tag, and you also want ids 2 and 3 to appear with a null tag. Which function?
Option A: explode. Option B: flatten. Option C: collect_list. Option D: explode_outer.
**Answer: D, explode_outer.**
Reason: explode_outer keeps rows whose array is empty or null and gives them a null value.
Trap: plain explode silently drops ids 2 and 3. Flatten is for nested arrays (array of arrays), not for making rows.

### M2. Delete target rows that are missing from the source (MERGE)
You run a MERGE that updates matched rows and inserts new ones. You also want to delete target rows that do not exist in the source. Which clause adds that?
Option A: WHEN NOT MATCHED BY SOURCE THEN DELETE. Option B: WHEN NOT MATCHED THEN DELETE. Option C: WHEN NOT MATCHED BY TARGET THEN DELETE. Option D: WHEN MATCHED BY SOURCE THEN DELETE.
**Answer: A.**
Reason: "Not matched by source" means target rows with no source match.
Trap: plain "WHEN NOT MATCHED" means "by target", which is source rows not in the target. That one can only insert.

### M3. Row filter syntax
Which statement applies a row filter function to a table on the region column?
Option A: ALTER TABLE t ADD ROW FILTER f ON region. Option B: ALTER TABLE t SET ROW FILTER f ON region in brackets. Option C: CREATE ROW FILTER f ON TABLE t. Option D: ALTER TABLE t ALTER COLUMN region SET ROW FILTER f.
**Answer: B.** Say it as: alter table, set row filter, function name, on, the column in brackets.
Reason: a row filter belongs to the table, so it uses ALTER TABLE SET ROW FILTER with ON (column).
Trap: ADD ROW FILTER is not real. ALTER COLUMN ... SET MASK is for column masks, not row filters.

### M4. Column mask syntax
Which statement applies a mask function to one column?
Option A: ALTER TABLE t SET MASK f. Option B: ALTER TABLE t ALTER COLUMN c SET MASK f. Option C: SET MASK f ON TABLE t TO COLUMN c. Option D: ALTER TABLE t SET MASK f ON (c).
**Answer: B.**
Reason: a mask attaches to a column, so it goes through ALTER COLUMN.
Trap: the "ON (column)" form belongs to row filters, so option D is a trap.

### M5. Managed vs external: which statement is NOT true about managed tables?
Option A: Databricks decides where the files are stored. Option B: You must give a LOCATION clause. Option C: DROP also removes the data files. Option D: Predictive optimization can run OPTIMIZE and VACUUM for it.
**Answer: B.**
Reason: a table with a LOCATION clause is external. A managed table has no LOCATION.
Trap: people mix up the two. Managed: drop removes metadata and files. External: drop removes metadata only.

### M6. Dot syntax on JSON
A column holds JSON as a plain string. You write column dot field and get an error. Why?
Option A: Dot syntax needs a struct, not a JSON string. Option B: Dot syntax only works on maps. Option C: JSON strings need explode first. Option D: You must use flatten.
**Answer: A.**
Reason: dot syntax reads fields of a struct. For a JSON string, use a colon (column colon field), or convert it with from_json.
Trap: a string that looks like JSON is still just a string.

### M7. schema_of_json
You use schema_of_json on one sample record, then use that schema with from_json on all your data. Some records have a field that was not in the sample. What happens to that field?
Option A: It is added automatically. Option B: It is dropped. Option C: The query fails. Option D: It goes to _rescued_data.
**Answer: B.**
Reason: the schema comes only from the sample you gave. from_json keeps just those fields.
Trap: from_json does not evolve. _rescued_data is an Auto Loader feature.

---

# PART 2: Real Udemy questions

### U1. Which join gives this result?
Students are Adam, Sarah, John. Enrollments exist for Adam (two courses) and Sarah (one). The result has four rows: Adam twice, Sarah once, and John with a null course. Which join?
Option A: Inner join. Option B: Anti join. Option C: Left join. Option D: Right join.
**Answer: C, left join.**
Reason: it keeps every left row and fills null when there is no match.
Trap: inner and right joins drop John. Anti join returns only John and no course column.

### U2. Cut cost on a fixed 15-worker job cluster
A job cluster is fixed at 15 workers. The load is usually lower, except at peak. Best way to cut cost?
Option A: Smaller instance types, same fixed size. Option B: Auto-termination after idle. Option C: Autoscaling with min and max workers. Option D: Use an all-purpose cluster.
**Answer: C.**
Reason: autoscaling adds workers at peak and removes them when load is light.
Trap: job clusters already shut down when the job ends. All-purpose clusters cost more per unit.

### U3. Seasonal spikes, fixed deadline, small team
Data spikes seasonally. Batch jobs must finish on time. Few people manage infrastructure. They want automatic scaling and optimization. Which option?
Option A: Job clusters with max resources. Option B: All-purpose clusters with autoscaling. Option C: Serverless compute. Option D: Dedicated clusters with Photon.
**Answer: C, serverless.**
Reason: Databricks manages and scales it. No tuning, no paying for idle peak capacity.
Trap: autoscaling alone still needs cluster management. Photon is a faster engine but does not remove management.

### U4. A few huge partitions in the Spark UI
Most partitions are about 100 MB. A few are several GB, and those tasks run much longer. Main bottleneck?
Option A: Data skew. Option B: Executor memory leak. Option C: Small file problem. Option D: Network timeout.
**Answer: A, data skew.**
Reason: a few keys hold most of the rows, so a few slow tasks hold up the stage.
Trap: small files is the opposite problem (too many tiny pieces). Fixes: AQE skew handling, salting keys, repartitioning.

### U5. Hide PII but keep the table queryable
A Unity Catalog table has emails and phone numbers. People must query it, but PII must be hidden from those without access. Most efficient method?
Option A: Revoke table privileges on PII columns. Option B: A dynamic view. Option C: Column masks. Option D: Row filters by region.
**Answer: C, column masks.**
Reason: a mask function is attached to the column and shows real or redacted values based on who asks.
Trap: privileges work on whole tables. Row filters hide rows, not columns. Dynamic views work but need extra maintenance.

### U6. Micro-batches, then stop
A streaming write must process all available data in multiple micro-batches and then stop. Which trigger?
Option A: once equals true. Option B: processing time of zero seconds. Option C: micro-batches equals true. Option D: available now equals true.
**Answer: D, availableNow.**
Reason: it processes everything in several batches and then stops.
Trap: once does it all in ONE batch. Processing time keeps running forever. Option C is not real.

### U7. Which TWO compute types can read Unity Catalog data? Pick two.
Option A: Classic compute on a runtime below 11.3 LTS. Option B: Classic compute in "no isolation shared" mode. Option C: Classic compute below 10.4 LTS. Option D: Classic compute in standard or dedicated access mode. Option E: SQL warehouse.
**Answer: D and E.**
Reason: Unity Catalog needs user isolation and runtime 11.3 LTS or newer. SQL warehouses support it by default.
Trap: "no isolation shared" cannot enforce permissions.

### U8. Never fail on schema change, but keep new columns
An Auto Loader stream must never fail on schema changes, but must capture new columns for later. Which schema evolution mode?
Option A: none. Option B: addNewColumns. Option C: failOnNewColumns. Option D: rescue.
**Answer: D, rescue.**
Reason: the schema stays the same, the stream keeps running, and new columns go into the _rescued_data column.
Trap: addNewColumns fails once, then picks up the columns on restart. None ignores new columns. failOnNewColumns fails until you fix it.

### U9. Where is a notebook stored?
Option A: In Unity Catalog. Option B: In the compute plane, on the cluster. Option C: In Delta tables in the data plane. Option D: In the control plane, as an encrypted workspace object.
**Answer: D.**
Reason: the control plane holds the UI, jobs config, and notebooks.
Trap: Unity Catalog governs data, not notebooks. Cluster disk is wiped when the cluster stops.

### U10. Where do you change a table's owner?
Option A: Permissions tab of the table page. Option B: Owner field on the table page. Option C: Permissions tab of the schema page. Option D: Owner field on the schema page.
**Answer: B.**
Reason: ownership is its own field, set per object. In SQL: ALTER TABLE, OWNER TO.
Trap: the Permissions tab is for grants, and ownership is not inherited from the schema.

### U11. Drop bad rows and report them
Complete: CONSTRAINT valid_id EXPECT (id IS NOT NULL) blank. Which clause drops violating rows and reports them?
Option A: ON VIOLATION FAIL UPDATE. Option B: Nothing, the default drops them. Option C: ON VIOLATION DELETE ROW. Option D: ON VIOLATION DROP ROW.
**Answer: D.**
Reason: DROP ROW removes the bad rows and logs the counts.
Trap: the default only warns: it keeps the rows. FAIL UPDATE stops the pipeline. DELETE ROW is fake.

### U12. What does df.na.fill with a dictionary do?
The call is: fill source with "unknown".
Option A: Nulls in the source column become "unknown". Option B: Rows equal to "unknown" are filtered out. Option C: Rows equal to "unknown" become null. Option D: Every value in source becomes "unknown".
**Answer: A.**
Reason: fill replaces nulls only.
Trap: drop removes rows with nulls. Replace swaps one value for another.

### U13. Make two columns NOT NULL on an existing table
Option A: ALTER TABLE, ALTER COLUMNS, both columns in brackets, SET NOT NULL. Option B: ALTER TABLE, ALTER COLUMN one, SET NOT NULL, then repeat for the other. Option C: ALTER TABLE, CHECK both columns not null. Option D: ALTER TABLE, CHECK, one per column.
**Answer: B.**
Reason: one ALTER COLUMN statement per column.
Trap: "ALTER COLUMNS" is not real. A CHECK needs ADD CONSTRAINT with a name first.

### U14. Save Git folder changes to the remote repo
Option A: Commit and Pull. Option B: Merge and Push. Option C: Commit and Push. Option D: Merge and Pull.
**Answer: C.**
Reason: commit saves locally, push uploads to the remote.
Trap: pull goes the wrong direction.

### U15. Name the feature: runtime statistics, switches join strategies, handles skew
Option A: Liquid clustering. Option B: Predictive optimization. Option C: Adaptive Query Execution. Option D: Photon.
**Answer: C, AQE.**
Reason: Spark re-plans while the query runs. It merges small shuffle partitions, switches to broadcast joins, and splits skewed partitions.
Trap: liquid clustering is table layout. Predictive optimization is automatic maintenance. Photon is a faster engine.

### U16. Job failed at task 12 of 15
The cause was a missing config file. After fixing it, how do you resume best?
Option A: Run task 12's notebook by hand. Option B: Wait for the next scheduled run. Option C: Repair run. Option D: Restart the job.
**Answer: C, repair run.**
Reason: it re-runs only the failed task and tasks that depend on it.
Trap: restart runs all 15 tasks again. Running the notebook by hand leaves the job run failed.

### U17. Open-source storage format that makes data lakes reliable
Option A: Unity Catalog. Option B: Delta Live Tables. Option C: Delta Lake. Option D: Apache Spark.
**Answer: C.**
Reason: Delta Lake is Parquet files plus a transaction log, giving ACID, time travel, and schema enforcement.
Trap: Unity Catalog is governance. Spark is the engine.

### U18. Streaming IoT job, lowest latency, runs forever
Option A: Scheduled trigger. Option B: Table update trigger. Option C: Continuous trigger. Option D: File arrival trigger.
**Answer: C.**
Reason: it starts a new run as soon as the last one ends or fails.
Trap: the others are timed or event-based, so latency is higher.

### U19. What is the targets section of a bundle file for?
Option A: User roles and access. Option B: External libraries. Option C: Deployment environments and their settings. Option D: Runtime version.
**Answer: C.**
Reason: targets are environments like dev and prod. Resources say what to deploy.
Trap: access goes under permissions.

### U20. Drop deletes metadata AND data files. Why?
Option A: The table is external. Option B: It is a shallow clone. Option C: Files are older than retention. Option D: The table is managed.
**Answer: D.**
Reason: Databricks owns both the metadata and the files for a managed table.
Trap: external tables lose only metadata. Retention matters for VACUUM, not DROP.

### U21. Benefits of SET MANAGED on an external table. Pick two.
Option A: Copies only the latest version to minimize downtime. Option B: Keeps table history and can roll back to external. Option C: Immediately deletes the old external location. Option D: Keeps name, settings, permissions, and views. Option E: Syncs changes incrementally each run.
**Answer: B and D.**
Reason: it is an in-place conversion that preserves history and permissions, and it can be undone.
Trap: old files stay for a safety window, about 14 days. It is one-time, not a sync.

### U22. What does dropna with a subset of two columns do?
Option A: Drops rows where both columns are null. Option B: Drops rows where either column is null. Option C: Drops both columns. Option D: Fills nulls with empty strings.
**Answer: B.**
Reason: the default is how equals any, so one null in the subset drops the row.
Trap: both-null needs how equals all.

### U23. Which is NOT true about Lakeflow Connect managed connectors?
Option A: They ingest from enterprise databases and SaaS apps. Option B: They are governed by Unity Catalog and run on serverless. Option C: They read and write incrementally. Option D: They need custom Python for auth, update logic, and requests.
**Answer: D.**
Reason: managed connectors are set up by UI or simple config, and Databricks handles auth and incremental logic.

### U24. Least privilege to read one table
A data scientist needs read-only on one table in schema analytics, catalog bi. Which grants?
Option A: Select on table plus use schema. Option B: Select on table plus use schema plus use catalog. Option C: Select plus use catalog. Option D: Select only.
**Answer: B.**
Reason: the user needs a "use" on both parents to reach the table.
Trap: all-privileges or modify break least privilege.

### U25. Apply a mask function to an existing column
This is the same as M4. Ask it again from memory: what is the exact statement shape?
**Answer: ALTER TABLE, ALTER COLUMN, column name, SET MASK, function name.**
Reason: row filters use ALTER TABLE SET ROW FILTER ON (column). Masks use ALTER COLUMN.

### U26. Which command purges stale data files of a Delta table?
Option A: DELETE. Option B: OPTIMIZE. Option C: VACUUM. Option D: CLEAN.
**Answer: C.**
Reason: VACUUM deletes unreferenced files older than the retention period, default 7 days.
Trap: DELETE only removes rows logically. OPTIMIZE compacts but leaves old files. CLEAN is fake.

### U27. Nightly load from on-prem PostgreSQL into a UC managed table with lineage
Option A: CREATE TABLE AS SELECT from a postgres host path. Option B: A streaming read with format postgresql. Option C: COPY INTO from the database. Option D: JDBC read, then saveAsTable with a three-level name.
**Answer: D.**
Reason: JDBC is the standard way to read a database. saveAsTable with catalog.schema.table writes a managed table, and lineage is automatic.
Trap: COPY INTO and Auto Loader work on files only. Nightly means batch, not streaming.

### U28. Which key sets who can manage or view a bundle job?
Option A: job_settings. Option B: roles. Option C: permissions. Option D: access_control.
**Answer: C, permissions.**
Reason: it holds the list of users or groups with levels such as CAN_VIEW and CAN_MANAGE.
Trap: access_control_list is the Jobs REST API name, not a bundle key.

### U29. Pass a value from the Bronze task to the Silver task in the same run
Option A: Temp view in Bronze. Option B: Cluster environment variable. Option C: Task values: set in Bronze, reference in Silver. Option D: Widgets and dbutils notebook run.
**Answer: C.**
Reason: task values pass small values between tasks in one run. Set with dbutils jobs taskValues set. Read with dbutils jobs taskValues get, or with a double-brace reference to tasks, task name, values, key.
Trap: temp views die with the session. Environment variables are fixed at cluster start.

### U30. Job "succeeded with failures": task B failed, task C still ran
Task C depends on A and B. A succeeded, B failed. C ran. Which setting explains it?
Option A: Task C has run-if "at least one failed". Option B: Task A has run-if "at least one succeeded". Option C: All tasks have run-if "all done". Option D: Task B has run-if "at least one failed".
**Answer: A.**
Reason: run-if only matters on tasks with upstream dependencies. B failed, so "at least one failed" is met.
Trap: A and B have no upstream tasks. Run-if options: all succeeded (default), at least one succeeded, none failed, all done, at least one failed, all failed.

### U31. Join two tables for this session only, store no data
Option A: Managed table. Option B: Temporary view. Option C: View. Option D: Global temporary view.
**Answer: B.**
Reason: a temp view stores no data and disappears when the session ends.
Trap: a global temp view lasts as long as the cluster, in the global_temp schema.

### U32. Which is NOT a Lakeflow Connect managed connector?
Option A: Workday Reports. Option B: Auto Loader. Option C: PostgreSQL. Option D: Salesforce.
**Answer: B.**
Reason: Auto Loader is a code-based standard connector for files. Managed connectors are no-code, for SaaS apps and databases.

### U33. Which service orchestrates tasks?
Option A: Databricks Jobs. Option B: Databricks Connect. Option C: Unity Catalog lineage. Option D: Delta Live Tables.
**Answer: A.**
Reason: Jobs runs tasks as a DAG with schedules, triggers, and retries.
Trap: a pipeline can be one task inside a job.

### U34. Only doctors who have appointments, columns from both
Option A: Join on doctor_id, inner. Option B: Left. Option C: Full. Option D: Cross.
**Answer: A, inner.**
Reason: "only X that have Y" means inner.
Trap: left keeps doctors with no appointments. "Only X with NO Y" is left anti.

### U35. What does cloudFiles.inferColumnTypes true do?
Option A: Infers keys. Option B: Infers precise types like int and timestamp. Option C: Turns on schema evolution. Option D: Forces all columns to string.
**Answer: B.**
Reason: by default JSON and CSV columns are all strings. This option samples the files and picks real types.
Trap: schema evolution is controlled by schemaEvolutionMode.

### U36. Simplest incremental ingest from ADLS, no event services
Option A: COPY INTO with a cron job each minute. Option B: Streaming with manual state. Option C: Auto Loader with file notifications. Option D: Auto Loader in directory listing mode.
**Answer: D.**
Reason: directory listing is the default and needs no extra cloud setup.
Trap: file notification needs event and queue services.

### U37. Email the team when a job fails
Option A: Configure email notifications on the job page. Option B: Only the owner can be notified. Option C: Not possible. Option D: Use the Jobs API to send emails.
**Answer: A.**
Reason: the built-in setting exists, and any email address can be added.

### U38. collect on a large DataFrame causes driver out of memory. Why?
Option A: memory fraction too high. Option B: broadcast threshold too high. Option C: executor cores too low. Option D: driver memory too small for the collected data.
**Answer: D.**
Reason: collect and toPandas pull all partitions to the driver.
Fix: use take, limit, display, or write to a table.

### U39. What does CAST of '100$' as INT return?
Option A: 100. Option B: An error. Option C: Null. Option D: $100.
**Answer: B.**
Reason: Databricks uses ANSI mode, so a bad cast errors.
Trap: TRY_CAST returns null instead.

### U40. A small table copied to all nodes, no shuffle
Option A: OPTIMIZE. Option B: Delta caching. Option C: Cross join. Option D: Broadcast join.
**Answer: D.**
Reason: Spark sends a full copy of the small table to every executor. The default size limit is 10 MB.

### U41. Which CLI command checks a bundle for errors before deploy?
Option A: bundle init. Option B: bundle validate. Option C: bundle check. Option D: bundle verify.
**Answer: B.**
Reason: the order is init, validate, deploy, run, destroy.
Trap: init creates a bundle from a template. Check and verify are fake.

### U42. Pipeline object for incremental ingest with Auto Loader
Option A: Materialized view. Option B: Streaming view. Option C: Temporary view. Option D: Streaming table.
**Answer: D.**
Reason: a streaming table processes only new rows each update.
Trap: materialized views are for batch-style aggregates, such as gold.

### U43. Key benefit of liquid clustering
Option A: Encrypts fields. Option B: Prevents duplicates. Option C: Reduces data scanned at query time. Option D: Real-time streaming.
**Answer: C.**
Reason: rows with similar keys are grouped, so filters skip whole files.
Also know: CLUSTER BY replaces partitioning and ZORDER. Keys can be changed without a full rewrite. It cannot be combined with PARTITIONED BY.

### U44. What does this ABAC policy do?
A policy on schema bank.safebox: column mask mask_card, to financial_analysts except admins, for tables, matching columns with tag value pii equals credit_card.
Option A: Masks credit card columns in the schema for analysts. Admins see real values. Option B: Same, but analysts unmasked and admins masked. Option C: Masks any column with the pii tag. Option D: Row filter.
**Answer: A.**
Reason: TO is who is restricted. EXCEPT is who is exempt. hasTagValue needs key and value.
Trap: hasTag matches the key only.

### U45. Format of Delta transaction log files
Option A: Delta. Option B: JSON. Option C: XML. Option D: Parquet.
**Answer: B, JSON.**
Reason: data files are Parquet. The _delta_log has one JSON file per commit, and a Parquet checkpoint every 10 commits.

### U46. Which claim about the Silver layer is incorrect?
Option A: Integrates other sources for enrichment. Option B: Stores raw data with source file details and ingestion timestamps. Option C: Cleanses and filters. Option D: Handles de-duplication.
**Answer: B.**
Reason: B describes Bronze. Silver is clean and validated. Gold is business aggregates.

### U47. Why use Git folders over built-in notebook versioning?
Option A: Branch creation and management. Option B: Automatic conflict resolution. Option C: Source code in Unity Catalog. Option D: Real-time auto sync to remote.
**Answer: A.**
Reason: built-in versioning is only a revision history of one notebook.
Trap: conflicts are resolved by hand. You commit and push yourself.

### U48. Refresh Gold every 15 minutes at the lowest cost
Option A: Table update trigger. Option B: Continuous. Option C: File arrival. Option D: Scheduled.
**Answer: D.**
Reason: compute only runs during each job, then shuts down.
Trap: continuous is always on and always billing.

### U49. Create a table over CSV files
Complete: CREATE TABLE with two string columns, blank, OPTIONS header true and delimiter semicolon, LOCATION path.
Option A: USING DELTA. Option B: AS CSV. Option C: FROM CSV. Option D: USING CSV.
**Answer: D.**
Reason: USING sets the data source format. With LOCATION, it is an external table that is not Delta.
Trap: a non-Delta table has no time travel and may need REFRESH TABLE.

### U50. What happens to rows failing expect_or_drop?
A silver table reads a bronze stream and has two expect_or_drop rules.
Option A: Deleted from both tables. Option B: Deleted from bronze. Option C: Streamed into silver anyway. Option D: Filtered out. Only valid rows are written to silver.
**Answer: D.**
Reason: the source is never changed. A row must pass every expectation.
Also know: expect keeps and logs. expect_or_fail stops the pipeline.

### U51. Query the events table as a streaming source in Python
Option A: spark.readStream() then table events. Option B: spark.read.table events. Option C: spark.readStream.table events. Option D: spark.readStream called with events.
**Answer: C.**
Reason: readStream is a property, with no brackets. Then the table method follows.
Trap: spark.read is a batch read.

### U52. Abort deploy if bundle jobs or pipelines are running
Option A: stop-if-running flag. Option B: fail-on-active-runs flag. Option C: force flag. Option D: check-executions flag.
**Answer: B, --fail-on-active-runs.**
Reason: it fails the deploy if any job or pipeline in the bundle has an active run.
Trap: force skips the Git branch check in production mode.

### U53. Which is NOT a valid Jobs task type?
Option A: SQL query. Option B: REST API call. Option C: Python wheel. Option D: If/else condition.
**Answer: B.**
Reason: real types include notebook, Python script, wheel, JAR, SQL, pipeline, dbt, run job, if/else, and for each.

### U54. Query a JSON file in a Unity Catalog volume
Catalog analytics, schema events, volume source_files, file transactions.json. Which path?
**Answer: slash Volumes, slash analytics, slash events, slash source_files, slash transactions.json.**
Reason: the order is catalog, schema, volume, file. It matches catalog dot schema dot table.
Trap: a dbfs path is legacy. A cloud path bypasses the volume. A reversed order fails.

### U55. Upload a CSV and make every column STRING
Option A: Nothing, CSV is string by default. Option B: In advanced attributes, turn off "automatically detect column types". Option C: Change each column one by one. Option D: Not possible.
**Answer: B.**
Reason: one setting changes all columns. The upload UI infers types by default.

---

# PART 3: Extra practice (Claude-written, same formats)

### E1. Anti join
Customers A, B, C. Orders for A, B, and an unknown D. You want only customer C. Which join type goes in a left blank join?
Option A: Semi. Option B: Anti. Option C: Outer. Option D: Inner.
**Answer: B, left anti.**
Reason: it keeps left rows with no match. Semi keeps left rows that DO match (A and B).

### E2. dropDuplicates on one column
What does dropDuplicates on customer_id do?
Option A: Keeps one row per customer_id. Option B: Removes only rows identical in every column. Option C: Removes every row whose customer_id repeats. Option D: Returns just the distinct ids.
**Answer: A.**
Trap: option B describes distinct with no argument.

### E3. COPY INTO syntax
Which keywords do you use for COPY INTO a Delta table from CSV files in a volume?
Option A: FILEFORMAT equals CSV with FORMAT_OPTIONS. Option B: FORMAT equals CSV with OPTIONS. Option C: USING CSV LOCATION. Option D: COPY FILES INTO.
**Answer: A.**
Reason: COPY INTO is idempotent. It skips files already loaded.

### E4. Auto Loader: stop once, then include new columns
Which schemaEvolutionMode does that?
Option A: rescue. Option B: failOnNewColumns. Option C: addNewColumns. Option D: none.
**Answer: C, addNewColumns (the default).**
Reason: it fails once, updates the schema, and picks up the new columns on restart.

### E5. Run-if for a cleanup task
Cleanup task D must run after A, B, and C finish, whether they succeeded or failed.
Option A: All succeeded. Option B: At least one succeeded. Option C: None failed. Option D: All done.
**Answer: D.**
Trap: "none failed" skips the task if any failed.

### E6. File arrival trigger
A partner drops files into a volume at random times. The job must start soon after a file lands, with no timer. Which trigger?
Option A: Scheduled. Option B: Continuous. Option C: File arrival. Option D: Manual with a polling loop.
**Answer: C.**
Reason: it reacts to new files. Continuous runs all the time and costs more.

### E7. Delta Sharing
You must share live tables with a partner on a different platform with no copying. The partner has no Databricks workspace.
Option A: Lakehouse Federation. Option B: Delta Sharing. Option C: Clean Rooms. Option D: Lineage.
**Answer: B.**
Trap: Federation queries outside databases from Databricks. Clean Rooms is joint analysis where neither side sees raw data.

### E8. Select privilege is not enough. Pick two more.
A user has select on main.hr.employees but cannot query it. Which TWO are also needed?
Option A: Use catalog on main. Option B: Use schema on main.hr. Option C: Browse on main. Option D: Modify on the table. Option E: All privileges on the schema.
**Answer: A and B.**
Trap: browse only shows metadata.

### E9. Run a bundle job
Run the job nightly_job from a bundle in the dev target.
Option A: bundle run, target dev, nightly_job. Option B: jobs run nightly_job with bundle dev. Option C: bundle start. Option D: bundle execute.
**Answer: A.**

### E10. 48,000 files, 2 GB, slow reads
DESCRIBE DETAIL shows 48,000 files and 2 GB in total. Most likely cause?
Option A: Data skew. Option B: Small file problem. Option C: Driver out of memory. Option D: Too few shuffle partitions.
**Answer: B.**
Reason: about 42 KB per file. Fix with OPTIMIZE, predictive optimization, or liquid clustering.

### E11. Input file name under Unity Catalog standard access
You need the source file name for each row.
Option A: _metadata.file_name. Option B: input_file_name(). Option C: _source_file(). Option D: INPUT_FILE_PATH().
**Answer: A.**
Reason: input_file_name is deprecated and unsupported on UC standard access mode.

### E12. Retry on short network errors
A task fails on short network errors but works on rerun. Fix it with no manual work.
Option A: Repair run. Option B: Run-if all done. Option C: Continuous trigger. Option D: Retry policy on the task.
**Answer: D.**
Trap: repair run is manual.

### E13. One notebook per region
Run one notebook for each of 20 regions, passing the region in.
Option A: If/else. Option B: Run job. Option C: For each. Option D: 20 chained tasks.
**Answer: C, for each task.**
Trap: Run job runs another job once.

### E14. Spilling with even task times
200 tasks. Median 3 minutes, max 3.4 minutes. Every task spills several GB to disk.
Option A: Data skew. Option B: Partitions too large for executor memory. Option C: Small files. Option D: Driver out of memory.
**Answer: B.**
Reason: even task times rule out skew. Spill means each partition is too big for memory.
