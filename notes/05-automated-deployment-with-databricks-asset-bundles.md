# Automated Deployment with Databricks Asset Bundles

*Order: 4 · Status: In Progress · Type: Module*

## A) What makes up a databricks project?

- Code, Resources, Execution Enviroment

Code is the logic that produces data products, think notebooks, python, JAR, SQL etc.

Resources are the databricks objects your code runs on

- Lakeflow Jobs/Workflows, MLflow tracking server & registry, Spark Declarative Pipelines (SDP)

Execution Environment

- Databricks Workspace, Unity Catalog, Compute configurations

The goal of DABs (Declarative Automation Bundles) is to write code once, then deploy to multiple enviorments easily. Basically you can type out instead of having to manually deploy Databricks implementations

## Just to Hammer it in

DABs in Three Questions?

- What are they?
  - They are collections of YAML (yet another markup language) files that specify artifcats, resources, and configurations of a Databricks project
- HOw do they work?
  - Generally, through the command line interface of Databricks, using the Declarative Automation Bundle using a bundle YAML file
- Where are they used?
  - Extremely useful during development and CI/CD processes to deploy databricks assets to specified enviorments

CI/CD with DABs Overview

## Simple Project Structure Overview

my_project/

- resources/ → Additional YAML configuration files fo ryour declartive automation bundles
- src/→ source files (notebooks, python files, etc) needed for the data pipeline)
- tests/ → Contains unit and integration tests for the data pipeline

## Top-Level Mappings

- Bundle → Identity of the bundle - declares the required bundle name

```
bundle:
  name: demo01_bundle
```

- resources → Databricks objects the bundle manages (jobs, pipeplines, MLflow)
  - It describes the Lakeflow Jobs, Spark Declarative Pipelines, MLflow used in the project. It uses teh corresponding Databricks REST API

```
resources: 
    jobs:
```

```
l1_simple_dab: #This is a "job key" and must be unique
    name: my_job_name_l1_simple_dab #actual name of the job
    tasks:
      - task_key: create_bronze_table #remember that a 

      #task must know what notebook to run, hence a notebook_path exists
        notebook_task:
          notebook_path: ./src/create_bronze_table.py
          source: WORKSPACE
```

- targets

```
targets: #Sets specific enviroments and their configuration overrides
  development:
    mode: development #development and default: true
    default: true
    workspace:
      host: https://dev.cloud.databricks.com/
  production: 
    mode: production
    workspace:
      host: https://prod.cloud.databricks.com/
```

The targets section tells the configuration which environments to deploy on

---
[Back to contents](../README.md)
