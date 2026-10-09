# Course notebooks by video

Source: course repo, **serverless-mode** branch (updated 2026-07-16). This branch is built for serverless and Unity Catalog volumes. It matches your dataset path `/Volumes/<catalog>/default/bookstore_dataset`. The `main` branch copy in `study/course-repo` is the old Hive/DBFS version.

Video numbers: only 37 and 38 are confirmed. The rest show `XX`, because the course has theory videos between the notebook videos, so numbers can't be counted from the repo. Tell me the numbers and I will rename the folders.

Upload the whole `videos` folder to your workspace as-is. Notebooks call `%run ../Includes/...`, so `Includes` must sit next to the video folders.

| Video | Section | Notebook | Serverless 17.3 note |
|---|---|---|---|
| XX | 1.1 | Notebook Basics | Runs. `%fs ls /databricks-datasets` may be empty on serverless. |
| XX | 1.2 | Understanding Delta Tables | Runs. `%fs` cells are commented out; use `DESCRIBE DETAIL` for the path. |
| XX | 1.3 | Advanced Delta Lake Features | Runs. Same `%fs` note as 1.2. |
| XX | 1.4 | Databases and Tables | Breaks as-is. `LOCATION 's3://<BUCKET>'` needs a real external location. Read only, or skip external-table cells. |
| XX | 1.5 | Views (1.5A, 1.5B) | Runs. Open 1.5B in a new session to see temp views drop. |
| XX | 2.1 | Querying Files | Uses widget `${dataset_bookstore}`. If you get "Missing cloud file system scheme", replace with the full `/Volumes/...` path. |
| XX | 2.2 | Writing to Tables | Same `${dataset_bookstore}` note as 2.1. |
| XX | 2.3 | Advanced Transformations | Same `${dataset_bookstore}` note (one cell). |
| XX | 2.4 | Higher Order Functions and SQL UDFs | Runs. |
| **37** | 2.5 | Data Transformations with PySpark | Runs. `spark.conf` broadcast cells are commented out (not allowed on serverless). |
| **38** | 3.1 | Structured Streaming | Runs. Uses `availableNow` trigger; `processingTime` is commented out. |
| XX | 3.2 | Auto Loader | Runs. `availableNow` trigger. |
| XX | 3.3 | Multi-Hop Architecture | One cell uses `input_file_name()`. If it errors, use `_metadata.file_path`. |
| XX | 4.1 | Bookstore Pipeline (DLT source files) | Not a notebook. Add these files to a Lakeflow pipeline and set config key `dataset_path`. `${dataset_path}` works here because the pipeline fills it in. |
| XX | 4.2 | Pipeline Results | Runs after the pipeline exists. |
| XX | 4.3 | Jobs (Land New Data) | Runs as a job task. |
| XX | 5.1 | Managing Permissions | Runs. Grants need real users or groups in your workspace. |
| XX | 5.2 | Fine-Grained Access Control | Runs. Row filters and column masks need Unity Catalog (you have it). |

## Labs

Each lab folder holds the lab and its solution. They use `%run ../Includes/Setup-Lab`, which is in `Includes`.

| Lab | Note |
|---|---|
| 1.1L Notebook Basics | `helper.py` is included for the `%run ./helper` step. |
| 1.2L Delta Lake | Runs. |
| 1.3L Databases and Tables | `%fs ls dbfs:/...` cells fail (no DBFS root). Use `DESCRIBE EXTENDED` instead. |
| 2.1L Querying Files, 2.2L Advanced ETL | Runs. |
| 3.1L Structured Streaming, 3.2L Multi-Hop | Runs. |
| 4.1L Delta Live Tables | Pipeline source; set `datasets_path` in pipeline config. |
| 4.2L Jobs, 4.3L Databricks SQL | Runs. 4.3L needs a SQL warehouse. |

Notes on "Runs": based on reading the code, not on running it in Databricks.
