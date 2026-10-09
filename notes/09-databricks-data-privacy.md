# Databricks Data Privacy

*Order: 7 · Status: Not Started · Type: Module*

## Data Security Model

*[Image in the Notion page, not copied]*

Privileges

- USE CATALOG → Remember these have to be set to allow usage of individual tables/volumes underneath
- USE SCHEMA → Remember these have to be set to allow usage of individual tables/volumes underneath
- CREATE TABLE
- SELECT
- MODIFY →
- EXECUTE
- **Quick ref:** Privileges follow a strict traversal hierarchy: Catalog → Schema → Object. SELECT on a table isn't enough — the principal also needs USE CATALOG and USE SCHEMA on every parent container, or they hit INSUFFICIENT_PERMISSIONS even with the table-level grant in place.

Securable

1.	SQL

1.	GRANT SELECT ON TABLE t TO analysts

2.	REVOKE SELECT ON TABLE t FROM analysts

2.	Catalog Explorer

3.	Programmatically

Automated Lineage for All Workloads

- Auto-capture runtime data lineage on a Databricks cluster or SQL warehouse
- Leverage

Built in search and discovery

- Unified UI to search for data assets

## Fine Grained Access Control

- Limit Access to Columns
  - Say you need to limit access for specific users or groups. When querying the view, targeted users will not see values for protected columns
- Limit Access to Rows
  - Records can be limited only to ones that didn’t get caught by filiter criteria
- Data Masking (obsecuring data)
  - Targeted users only see a transformed version of the data for protected columns
  - Such as the SSN column only showing the last four digits

How to do this? Dynamic Views

Dynamic Views allow you to prevent users from actually needing direct access to a table or set of tables

- This, in combination, with the specialized SQL functions that Databricks provides, enables patterns that make it easy to implement three common use cases (ones above)

## Row Level Security and Column Level Masking

Provide differential fine grained access to file based datasets and foreign tables

- Only show specific rows

```
CREATE FUNCTION us_filiter(region STRING)
RETURN IF (IS_MEMBER('admin'), true, region = "US");
AALTER TABLE sales SET ROW
```

## Revoke all privileges on schema

```
REVOKE ALL PRIVILEGES ON SCHEMA main.hr_schema from hr_group;
```

Things to understand about Revoke All

- It is safe to run a REVOKE statement in Unity Catalog. It will succed even if the group didn’t ahve certain privileges to begin with
- It functions as a clean slate ensuring those explict privileges are completely absent
- SHOW GRANTS = just shows the permissions set for a given group
- REVOKE ALL PRIVILEGES → Removes all explictily granted capabilities (such as SELECT, MODIFY, CREATE) on target object for that specific group

---
[Back to contents](../README.md)
