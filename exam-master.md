# Databricks Data Engineer Associate: Master Study File

Last updated: 2026-10-04.
Exam: Sunday 2026-10-11 at 11am.
Owner: G-Man.

This file is the main reference for exam prep. Other notes and chats should use it as the source of truth.

---

## 1. Exam facts (checked on the official Databricks site, 2026-10-04)

- 45 scored questions.
- 90 minutes. That is 2 minutes per question.
- All questions are multiple choice.
- The exam may also include a few unscored test questions. You cannot tell which ones they are.
- The current exam guide took effect on May 4, 2026.
- Databricks does not publish the passing score on the exam page. Many sources say 70%, but this is **not verified**. At 70%, you need about 32 of 45 correct.

Sources:
- Exam page: https://www.databricks.com/learn/certification/data-engineer-associate
- Exam guide PDF: https://www.databricks.com/sites/default/files/2026-05/databricks-certified-data-engineer-associate-exam-guide-may-2026-000.pdf

---

## 2. The math

How the numbers work:

1. **Expected questions per section** = section weight × 45.
2. Each section lists several goals in the exam guide. The guide does not say how questions split between goals. So this file **assumes an even split**. Expected questions per goal = section questions ÷ number of goals.
3. Each goal gets a status based on your current notes:
   - **Covered**: your flashcards or exercises already explain it.
   - **Partial**: your notes touch it, but miss key parts.
   - **Missing**: nothing in your notes.
4. **Questions at risk** = expected questions × gap. Gap is 0 for Covered, 0.5 for Partial, 1 for Missing.

Important: "Covered" means it is in your notes. It does not mean you know it yet. You have not watched course sections 3, 4, and 5.

### Section table (sorted by weight)

| Rank | Exam section | Weight | Expected questions | Goals in guide | Questions per goal | Questions at risk |
|---|---|---|---|---|---|---|
| 1 | Transformation and modeling | 22% | 9.9 | 7 | 1.41 | 3.5 |
| 2 | Ingestion and loading | 21% | 9.5 | 7 | 1.35 | 4.7 |
| 3 | Lakeflow Jobs | 16% | 7.2 | 4 | 1.80 | 3.6 |
| 4 | Governance and security | 15% | 6.8 | 4 | 1.69 | 1.7 |
| 5 | CI/CD | 10% | 4.5 | 4 | 1.13 | 3.9 |
| 6 | Troubleshooting and optimization | 10% | 4.5 | 5 | 0.90 | 3.6 |
| 7 | Platform | 6% | 2.7 | 2 | 1.35 | 0.7 |
| | **Total** | 100% | 45 | 33 | | **21.8** |

### What the totals mean

- Covered goals: about 13.5 questions.
- Partial goals: about 19.5 questions.
- Missing goals: about 12 questions.
- Rough worst case: you get every Covered question right, half of the Partial ones, and guess on the Missing ones (1 in 4 chance). That is about 13.5 + 9.8 + 3 = **26 of 45, or 58%**. That is below the likely pass line of 32.
- Conclusion: finishing the videos is not enough on its own. You also need to close the Missing and Partial gaps.

---

## 3. Topics to learn fully (sorted by expected questions)

Learn these well. They carry the most questions. "Course section" says where in Derar's course the topic is taught. "Not in course" means you need another source.

| Rank | Topic (exam goal) | Exam section | Expected questions | Your notes | Course section |
|---|---|---|---|---|---|
| 1 | Control flow tasks (if/else, for each) | Jobs | 1.80 | Missing | Not in course notebooks |
| 2 | Task dependencies (DAG) | Jobs | 1.80 | Covered | 4 |
| 3 | Job schedules and trigger types | Jobs | 1.80 | Partial | 4 |
| 4 | Time-based vs data-driven triggers | Jobs | 1.80 | Partial | 4 (partly) |
| 5 | Managed vs external tables | Governance | 1.69 | Covered | 1.4 |
| 6 | GRANT, REVOKE, DENY | Governance | 1.69 | Partial | 5.1 |
| 7 | Column masks and row filters | Governance | 1.69 | Covered | 5.2 |
| 8 | ABAC policies | Governance | 1.69 | Partial | 5.2 |
| 9 | Data cleaning in PySpark and SQL | Transformation | 1.41 | Partial | 2.3, 2.5 |
| 10 | Join types | Transformation | 1.41 | Partial | 2.3, 2.5 |
| 11 | Changing columns, rows, and tables | Transformation | 1.41 | Covered | 2.2 to 2.5 |
| 12 | Deduplication and aggregation | Transformation | 1.41 | Partial | 2.5 |
| 13 | Basic tuning and measuring performance | Transformation | 1.41 | Missing | Not in course notebooks |
| 14 | Gold layer: materialized views, views, streaming tables | Transformation | 1.41 | Covered | 4 |
| 15 | Data quality checks (expectations) | Transformation | 1.41 | Covered | 4 |
| 16 | Batch, streaming, and incremental loading | Ingestion | 1.35 | Covered | 3.1, 3.2 |
| 17 | COPY INTO | Ingestion | 1.35 | Covered | 2.2 |
| 18 | Auto Loader schema enforcement and evolution | Ingestion | 1.35 | Partial | 3.2 |
| 19 | Lakeflow Connect | Ingestion | 1.35 | Missing | Not in course notebooks |
| 20 | JDBC/ODBC or REST clients | Ingestion | 1.35 | Missing | Not in course notebooks |
| 21 | Choosing an ingestion method | Ingestion | 1.35 | Partial | 3.2 (Auto Loader vs COPY INTO) |
| 22 | Semi-structured and unstructured data | Ingestion | 1.35 | Partial | 2.1, 2.3 |
| 23 | Core parts: architecture, Delta Lake, Unity Catalog | Platform | 1.35 | Covered | 1 |
| 24 | Compute types, limits, and costs | Platform | 1.35 | Partial | 1.0 |
| 25 | Git folders | CI/CD | 1.13 | Partial | 1.1 (maybe) |
| 26 | Bundle variables per environment | CI/CD | 1.13 | Missing | Not in course notebooks |
| 27 | Deploying bundles across environments | CI/CD | 1.13 | Missing | Not in course notebooks |
| 28 | Databricks CLI for bundles | CI/CD | 1.13 | Missing | Not in course notebooks |
| 29 | Run history trends | Troubleshooting | 0.90 | Missing | Not in course notebooks |
| 30 | Monitoring in the Jobs UI | Troubleshooting | 0.90 | Partial | 4 (partly) |
| 31 | Spark UI: data skew and shuffles | Troubleshooting | 0.90 | Missing | Not in course notebooks |
| 32 | Liquid clustering and predictive optimization | Troubleshooting | 0.90 | Partial | Not in course notebooks |
| 33 | Cluster startup failures and memory errors | Troubleshooting | 0.90 | Missing | Not in course notebooks |

Note: "Not in course notebooks" means the course's code files do not cover it. The course videos might still mention it. This was not checked.

---

## 4. Gaps in your current notes (sorted by questions at risk)

Your current notes are:
- 115 flashcards in `study/quizlet/cards.txt`.
- Exercises and an answer key for course notebooks 2.1 to 2.3, in `study/context/`.
- Your own runs of course notebooks 2.1 to 2.4.

Your Notion notes could not be checked. The Notion connection failed in this session.

### Missing: nothing in your notes (about 12 questions)

| Topic | Questions at risk | What to add to your notes |
|---|---|---|
| Control flow tasks (if/else, for each) | 1.80 | What an if/else task does. What a for each task does. When to use each. |
| Basic tuning and measuring performance | 1.41 | Which settings speed up a query. Examples: Photon (a faster query engine) and the shuffle partitions setting. How to see how long a query took. |
| Lakeflow Connect | 1.35 | What it is: built-in connectors that pull data from apps and databases (for example Salesforce or SQL Server) into Databricks. When to pick it over Auto Loader. |
| JDBC/ODBC or REST clients | 1.35 | JDBC and ODBC are standard ways for outside tools to connect to a database. REST means sending web requests to an API. Know when each is used. |
| Bundle variables per environment | 1.13 | How a bundle file sets different values for dev, test, and prod. |
| Deploying bundles across environments | 1.13 | What a bundle is: one config file that defines jobs and pipelines as code. How you deploy it to dev, then prod. |
| Databricks CLI for bundles | 1.13 | The main commands: `databricks bundle validate`, `deploy`, and `run`. |
| Run history trends | 0.90 | Where to see past job runs. How to spot a job getting slower over time. |
| Spark UI: data skew and shuffles | 0.90 | Skew: one worker gets much more data than the others, so it runs much longer. Shuffle: data moving between workers, often from joins or group by. How each shows up in the Spark UI. |
| Cluster startup failures and memory errors | 0.90 | Common reasons a cluster fails to start. What an out-of-memory error looks like, and simple fixes. |

### Partial: notes exist but miss key parts (about 9.8 questions at risk)

| Topic | Questions at risk | What is missing from your notes |
|---|---|---|
| Job schedules and trigger types | 0.90 | You have one card on schedules. Add the full list of trigger types: scheduled, file arrival, table update, continuous. |
| Time-based vs data-driven triggers | 0.90 | When to use a time-based trigger and when to use a data-driven one. Table update trigger is not in your notes. |
| GRANT, REVOKE, DENY | 0.84 | DENY is not in your notes. **Check this:** older Unity Catalog did not support DENY, but the new exam guide lists it. |
| ABAC policies | 0.84 | No card yet. ABAC means access based on tags, such as "hide every column tagged PII". Course notebook 5.2 covers it. |
| Data cleaning in PySpark and SQL | 0.71 | Your SQL notes are strong. PySpark notes are thin. You are on video 37 (2.5 PySpark) now. |
| Join types | 0.71 | No card lists join types. Add inner, left, right, full, semi, anti, and cross, with one line each. |
| Deduplication and aggregation | 0.71 | No card for DISTINCT, dropDuplicates, or GROUP BY. |
| Compute types, limits, and costs | 0.68 | Cards cover cluster types. Missing: what each one cannot do, and which costs more. |
| Auto Loader schema enforcement and evolution | 0.67 | You have the schema location card. Missing: schema evolution modes, and the rescued data column (where rows that do not fit the schema go). |
| Choosing an ingestion method | 0.67 | You have Auto Loader vs COPY INTO. Missing: where Lakeflow Connect and JDBC fit. |
| Semi-structured and unstructured data | 0.67 | JSON and binaryFile are covered. Missing: loading unstructured files through connectors. |
| Git folders | 0.56 | One card. Add: what you can and cannot do in Git folders from the UI. |
| Monitoring in the Jobs UI | 0.45 | You have repair run and notifications. Missing: how to read run status and pipeline health. |
| Liquid clustering and predictive optimization | 0.45 | Liquid clustering card exists. Predictive optimization is missing. It means Databricks runs OPTIMIZE and VACUUM for you on managed tables. |

---

## 5. What this means for the week

1. Finish the course videos first, as planned: 2.5, then sections 3, 4, and 5.
2. While watching, add cards for the Partial items above that the course teaches.
3. After the videos, study the Missing topics. Most are not in the course. Use Databricks docs or the Databricks Academy courses named in the exam guide.
4. Then move to practice questions.

The biggest groups of uncovered questions are:
- CI/CD bundles (about 3.4 questions, none in the course).
- Troubleshooting (about 3.6 questions, mostly not in the course).
- Lakeflow Jobs control flow and triggers (about 3.6 questions).

---

## 6. Notes for any assistant reading this file

- Keep answers short and plain. The user does not want jargon or flattery.
- The course was built for Databricks Runtime 13.3. The user's workspace is newer and runs on serverless compute. Point out where the course is out of date.
- DLT is now called Lakeflow Declarative Pipelines. Workflows are now called Lakeflow Jobs.
- Say when a claim has not been verified.
