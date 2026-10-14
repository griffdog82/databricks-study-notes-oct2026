# Databricks Data Engineer Associate — Study Notes

Exam: 2026-10-11, 11am. Notes exported from Notion on 2026-10-09. Images were not copied.

## Notes (from Notion)

1. [Commands to Know](notes/01-commands-to-know.md)
   - Commands to Memorize — Databricks Data Engineer Associate
2. [Data Ingestion with Lakeflow Connect](notes/02-data-ingestion-with-lakeflow-connect.md)
   - Lakeflow Overview
   - Connectors and Ingestion Types
   - Delta Lake
   - Medallion Architecture
   - Ingest Details
   - Writing to Tables
   - Sidenotes
   - 2.2 Writing to Tables
   - 2.3 Advanced Transformations
   - 2.4 Higher Order Functions and SQL UDFs
   - 3.1 Structured Streaming
3. [Deploy Workloads with Lakeflow Jobs](notes/03-deploy-workloads-with-lakeflow-jobs.md)
   - What is a Job?
   - Ergo, what is a Task?
   - Task Orchestration
4. [Build Data Pipelines with Lakeflow Spark Declarative Pipelines](notes/04-build-data-pipelines-with-lakeflow-spark-declarative-pipelin.md)
   - Demo: Developing a Simple Pipeline
   - Ensure Data Quality with Expectations
   - Demo 3: Adding Data Quality Expectations
   - Demo 5 - Deploying a Pipeline to Production
   - Change Data Capture (CDC)
5. [Automated Deployment with Databricks Asset Bundles](notes/05-automated-deployment-with-databricks-asset-bundles.md)
   - A) What makes up a databricks project?
   - Just to Hammer it in
   - Simple Project Structure Overview
   - Top-Level Mappings
6. [Databricks Performance Optimization](notes/06-databricks-performance-optimization.md)
   - Cluster Architecture
   - Methods of Optimizing
   - Fine-Tuning: Chosing the Right Cluster
   - Predictive Optimization
7. [Exam Information: Databricks Certified Associate Data Engineer](notes/07-exam-information-databricks-certified-associate-data-enginee.md)
8. [DevOps Essentials for Data Engineering](notes/08-devops-essentials-for-data-engineering.md)
9. [Databricks Data Privacy](notes/09-databricks-data-privacy.md)
   - Data Security Model
   - Fine Grained Access Control
   - Row Level Security and Column Level Masking
   - Revoke all privileges on schema
10. [Exam Information: Databricks Certified Professional Data Engineer](notes/10-exam-information-databricks-certified-professional-data-engi.md)
11. [Preparing For Databricks Certification Exams](notes/11-preparing-for-databricks-certification-exams.md)
   - Practice Exam Resources
12. [Certification Overview Course (Databricks Certified Data Engineer Professional Exam)](notes/12-certification-overview-course-databricks-certified-data-engi.md)

## Other study files

- [Exam master sheet](exam-master.md)
- [Free Edition vs Azure review](free-edition-review.md)
- [Quizlet cards](quizlet/cards.txt)
- [GitHub Copilot Pilot — Key Questions](notes/13-github-copilot-pilot-key-questions.md) (not Databricks)
- [Course notebooks](course-notebooks/) (one folder per video, plus labs)

  - Extra: [1.0 - Creating Clusters.py](course-notebooks/Extra/1.0%20-%20Creating%20Clusters.py), [1.0L - Creating Clusters.py](course-notebooks/Extra/1.0L%20-%20Creating%20Clusters.py), [1.0L Solution - Creating Clusters.py](course-notebooks/Extra/1.0L%20Solution%20-%20Creating%20Clusters.py)
  - Lab 1.1L - Notebook Basics: [1.1L - Notebook Basics.py](course-notebooks/Lab%201.1L%20-%20Notebook%20Basics/1.1L%20-%20Notebook%20Basics.py), [1.1L Solution - Notebook Basics.py](course-notebooks/Lab%201.1L%20-%20Notebook%20Basics/1.1L%20Solution%20-%20Notebook%20Basics.py), [helper.py](course-notebooks/Lab%201.1L%20-%20Notebook%20Basics/helper.py)
  - Lab 1.2L - Delta Lake: [1.2L - Delta Lake.sql](course-notebooks/Lab%201.2L%20-%20Delta%20Lake/1.2L%20-%20Delta%20Lake.sql), [1.2L Solution - Delta Lake.sql](course-notebooks/Lab%201.2L%20-%20Delta%20Lake/1.2L%20Solution%20-%20Delta%20Lake.sql)
  - Lab 1.3L - Databases and Tables on Databricks: [1.3L - Databases and Tables on Databricks.sql](course-notebooks/Lab%201.3L%20-%20Databases%20and%20Tables%20on%20Databricks/1.3L%20-%20Databases%20and%20Tables%20on%20Databricks.sql), [1.3L Solution - Databases and Tables on Databricks.sql](course-notebooks/Lab%201.3L%20-%20Databases%20and%20Tables%20on%20Databricks/1.3L%20Solution%20-%20Databases%20and%20Tables%20on%20Databricks.sql)
  - Lab 2.1L - Querying Files: [2.1L - Querying Files.sql](course-notebooks/Lab%202.1L%20-%20Querying%20Files/2.1L%20-%20Querying%20Files.sql), [2.1L Solution - Querying Files.sql](course-notebooks/Lab%202.1L%20-%20Querying%20Files/2.1L%20Solution%20-%20Querying%20Files.sql)
  - Lab 2.2L - Advanced ETL: [2.2L - Advanced ETL.sql](course-notebooks/Lab%202.2L%20-%20Advanced%20ETL/2.2L%20-%20Advanced%20ETL.sql), [2.2L Solution - Advanced ETL.sql](course-notebooks/Lab%202.2L%20-%20Advanced%20ETL/2.2L%20Solution%20-%20Advanced%20ETL.sql)
  - Lab 3.1L - Spark Structured Streaming: [3.1L - Spark Structured Streaming.py](course-notebooks/Lab%203.1L%20-%20Spark%20Structured%20Streaming/3.1L%20-%20Spark%20Structured%20Streaming.py), [3.1L Solution - Spark Structured Streaming.py](course-notebooks/Lab%203.1L%20-%20Spark%20Structured%20Streaming/3.1L%20Solution%20-%20Spark%20Structured%20Streaming.py)
  - Lab 3.2L - Multi-Hop Architecture: [3.2L - Multi-Hop Architecture.py](course-notebooks/Lab%203.2L%20-%20Multi-Hop%20Architecture/3.2L%20-%20Multi-Hop%20Architecture.py), [3.2L Solution - Multi-Hop Architecture.py](course-notebooks/Lab%203.2L%20-%20Multi-Hop%20Architecture/3.2L%20Solution%20-%20Multi-Hop%20Architecture.py)
  - Lab 4.1L - Delta Live Tables: [4.1L - Delta Live Tables.sql](course-notebooks/Lab%204.1L%20-%20Delta%20Live%20Tables/4.1L%20-%20Delta%20Live%20Tables.sql), [4.1L Solution - Delta Live Tables.sql](course-notebooks/Lab%204.1L%20-%20Delta%20Live%20Tables/4.1L%20Solution%20-%20Delta%20Live%20Tables.sql)
  - Lab 4.2L - Jobs - Land New Data: [4.2L - Jobs - Land New Data.py](course-notebooks/Lab%204.2L%20-%20Jobs%20-%20Land%20New%20Data/4.2L%20-%20Jobs%20-%20Land%20New%20Data.py), [4.2L Solution - Jobs - Land New Data.py](course-notebooks/Lab%204.2L%20-%20Jobs%20-%20Land%20New%20Data/4.2L%20Solution%20-%20Jobs%20-%20Land%20New%20Data.py)
  - Lab 4.3L - Databricks SQL: [4.3L - Databricks SQL.sql](course-notebooks/Lab%204.3L%20-%20Databricks%20SQL/4.3L%20-%20Databricks%20SQL.sql), [4.3L Solution - Databricks SQL.sql](course-notebooks/Lab%204.3L%20-%20Databricks%20SQL/4.3L%20Solution%20-%20Databricks%20SQL.sql)
  - Video 37 - 2.5 Data Transformations with PySpark: [2.5 - Data Transformations with PySpark.py](course-notebooks/Video%2037%20-%202.5%20Data%20Transformations%20with%20PySpark/2.5%20-%20Data%20Transformations%20with%20PySpark.py)
  - Video 38 - 3.1 Structured Streaming: [3.1 - Structured Streaming.py](course-notebooks/Video%2038%20-%203.1%20Structured%20Streaming/3.1%20-%20Structured%20Streaming.py)
  - Video XX - 1.1 Notebook Basics: [1.1 - Notebook Basics.py](course-notebooks/Video%20XX%20-%201.1%20Notebook%20Basics/1.1%20-%20Notebook%20Basics.py)
  - Video XX - 1.2 Understanding Delta Tables: [1.2 - Understanding Delta Tables.sql](course-notebooks/Video%20XX%20-%201.2%20Understanding%20Delta%20Tables/1.2%20-%20Understanding%20Delta%20Tables.sql)
  - Video XX - 1.3 Advanced Delta Lake Features: [1.3 - Advanced Delta Lake Features.sql](course-notebooks/Video%20XX%20-%201.3%20Advanced%20Delta%20Lake%20Features/1.3%20-%20Advanced%20Delta%20Lake%20Features.sql)
  - Video XX - 1.4 Databases and Tables: [1.4 - Databases and Tables on Databricks.sql](course-notebooks/Video%20XX%20-%201.4%20Databases%20and%20Tables/1.4%20-%20Databases%20and%20Tables%20on%20Databricks.sql)
  - Video XX - 1.5 Views: [1.5A - Views.sql](course-notebooks/Video%20XX%20-%201.5%20Views/1.5A%20-%20Views.sql), [1.5B - Views (Session 2).sql](course-notebooks/Video%20XX%20-%201.5%20Views/1.5B%20-%20Views%20%28Session%202%29.sql)
  - Video XX - 2.1 Querying Files: [2.1 - Querying Files.sql](course-notebooks/Video%20XX%20-%202.1%20Querying%20Files/2.1%20-%20Querying%20Files.sql)
  - Video XX - 2.2 Writing to Tables: [2.2 - Writing to Tables.sql](course-notebooks/Video%20XX%20-%202.2%20Writing%20to%20Tables/2.2%20-%20Writing%20to%20Tables.sql)
  - Video XX - 2.3 Advanced Transformations: [2.3 - Advanced Transformations.sql](course-notebooks/Video%20XX%20-%202.3%20Advanced%20Transformations/2.3%20-%20Advanced%20Transformations.sql)
  - Video XX - 2.4 Higher Order Functions and SQL UDFs: [2.4 - Higher Order Functions and SQL UDFs.sql](course-notebooks/Video%20XX%20-%202.4%20Higher%20Order%20Functions%20and%20SQL%20UDFs/2.4%20-%20Higher%20Order%20Functions%20and%20SQL%20UDFs.sql)
  - Video XX - 3.2 Auto Loader: [3.2 - Auto Loader.py](course-notebooks/Video%20XX%20-%203.2%20Auto%20Loader/3.2%20-%20Auto%20Loader.py)
  - Video XX - 3.3 Multi-Hop Architecture: [3.3 - Multi-Hop Architecture.py](course-notebooks/Video%20XX%20-%203.3%20Multi-Hop%20Architecture/3.3%20-%20Multi-Hop%20Architecture.py)
  - Video XX - 4.1 Bookstore Pipeline (DLT): [1_bronze.py](course-notebooks/Video%20XX%20-%204.1%20Bookstore%20Pipeline%20%28DLT%29/python_transformations/1_bronze.py), [2_silver.py](course-notebooks/Video%20XX%20-%204.1%20Bookstore%20Pipeline%20%28DLT%29/python_transformations/2_silver.py), [3_gold.py](course-notebooks/Video%20XX%20-%204.1%20Bookstore%20Pipeline%20%28DLT%29/python_transformations/3_gold.py), [1_bronze.sql](course-notebooks/Video%20XX%20-%204.1%20Bookstore%20Pipeline%20%28DLT%29/sql_transformations/1_bronze.sql), [2_silver.sql](course-notebooks/Video%20XX%20-%204.1%20Bookstore%20Pipeline%20%28DLT%29/sql_transformations/2_silver.sql), [3_gold.sql](course-notebooks/Video%20XX%20-%204.1%20Bookstore%20Pipeline%20%28DLT%29/sql_transformations/3_gold.sql)
  - Video XX - 4.2 Pipeline Results: [4.2 - Pipeline Results.py](course-notebooks/Video%20XX%20-%204.2%20Pipeline%20Results/4.2%20-%20Pipeline%20Results.py)
  - Video XX - 4.3 Jobs (Land New Data): [4.3 - Land New Data Task.py](course-notebooks/Video%20XX%20-%204.3%20Jobs%20%28Land%20New%20Data%29/4.3%20-%20Land%20New%20Data%20Task.py)
  - Video XX - 5.1 Managing Permissions: [5.1 - Managing Permissions.sql](course-notebooks/Video%20XX%20-%205.1%20Managing%20Permissions/5.1%20-%20Managing%20Permissions.sql)
  - Video XX - 5.2 Fine-Grained Access Control: [5.2 - Fine-Grained Access Control.sql](course-notebooks/Video%20XX%20-%205.2%20Fine-Grained%20Access%20Control/5.2%20-%20Fine-Grained%20Access%20Control.sql)
