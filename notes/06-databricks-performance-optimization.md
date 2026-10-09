# Databricks Performance Optimization

*Order: 5 · Status: Not Started · Type: Module*

## Cluster Architecture

Driver → Drives/Controls/Manages Workers

Workers → Contain Executors

Executors → Processes that run on workers. The executor holds data in memory and runs tasks

Cores → Slots inside an executor. These can run one task at once. If an executor has four cores, it can run four tasks in parallel.

## Methods of Optimizing

Liquid Clustering → Replacement for partitioning a table

```
CREATE TABLE
CLUSTER BY
AS SELECT
```

VACCUM → Retains delta table historical records 7 days prior after running vaccum. I.e. these won’t be touched

```
OPTMOIZE employees
ZORDER BY ID
```

To-Do: Add the correct/optimal way to

Shuffle

- essentially, it uses

```
spark.conf.unset("spark.sql.autoBroadcastJoinThreshold")
spark.conf.unset("spark.databricks.adaptive.autoBroadJOinThreshold")
```

- A broadcast join just allows each executor to receive a copy of a table. Recall that in databricks, each executor is like a table with students, and each student is a core.

SparkUI is the table that you get when you select View on the Spark Jobs button on the bottom of a run cell in Databricks

*[Image in the Notion page, not copied]*

Data Skew

- This is when too much data is sotred in a single partition or node, causing that executor to run out of memory

Spill

- This is the concept that a cluster only has so much memory/RAM assigned to it
- At some point, it may rely on the plentiful yet slow disk memory to begin completing operations. This is to avoid the “dreaded” OOM (out of memory) error

Serialization

- This a lesson on why UDF (user defined functions) are generally a tool of last resort when compared to using a Spark function
- Built-in functions, SQL UDFs, Pandas UDFs, Python UDFs
- Python UDFs cannot be seen by catalyzer (Spark’s optimizer)

## Fine-Tuning: Chosing the Right Cluster

Cluster Types

All-Purpose Compute

- Designed to handle interactive workloads, streaming workloads

Jobs Compute

- Run on clusters that are spun up and down as needed
- Good for repeat workloads, lower cost

SQL Warehouse

- High concurrency (multiple users), ad-hoc SQL analytics and BI (business intelligence) serving

Spot Instances

- Great for ad-hoc/shared clusters
- Basically, you can use these for SLA (service level agreement) mission critical jobs, provided you set a fallback on-demand

Autoscaling

- Just know that you must set a min and max number of workers
- To set this figure some experimentation is needed. At first, you will set a higher limit, as you don’t truly know the volume of a given process. As time progresses, you will (hopefully) learn the actual volume

## Predictive Optimization

Advantages include

- Automatic Maintenance
  - Automates the execution of background maintenance tasks on Delta tables
- Set and Forget Apporach
  - No need to have manual supervision of common maintenance tasks
- Query Performance Improvement Through Collection of Statistics Automatically

---
[Back to contents](../README.md)
