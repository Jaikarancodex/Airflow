# Airflow

---

## 1️⃣ Introduction to Apache Airflow

**Interview Definition:**
Apache Airflow is an **open-source workflow orchestration tool** used to **schedule, monitor, and manage data pipelines** using code.

⚠️ **Important (Very Common Trap):**

* ❌ Airflow is **NOT** an ETL tool
* ✅ Airflow **orchestrates** tasks, it does not process data

**One-liner for interviews:**

> “Airflow orchestrates *when* and *in what order* tasks run.”

---

## 1.1 Core Concepts

---

### 1.1.1 DAG Definition

**Interview Question:** What is a DAG in Airflow?

**Answer:**
A DAG (Directed Acyclic Graph) is a **Python-defined workflow** that represents tasks and their execution order **without loops**.

**Key points interviewers expect:**

* Directed → tasks have order
* Acyclic → no infinite loops
* Graph → dependencies between tasks
* Defined using Python

**Example:**

```python
from airflow import DAG
from datetime import datetime

with DAG(
    dag_id="daily_sales_pipeline",
    start_date=datetime(2024, 1, 1),
    schedule="@daily",
    catchup=False
):
    pass
```

**Real-world analogy:**
A cooking recipe — steps must follow a fixed order.

---

### 1.1.2 Operators

**Interview Question:** What are Operators in Airflow?

**Answer:**
Operators define **what action** a task performs (e.g., run a script, execute Python code, trigger another DAG).

**Example:**

```python
from airflow.operators.bash import BashOperator

BashOperator(
    task_id="print_date",
    bash_command="date"
)
```

⚠️ **Interview Gold:**

* Operator is a **template**
* Task is an **instance of an operator**

---

### 1.1.3 Tasks

**Interview Question:** What is a Task?

**Answer:**
A task is a **single, atomic unit of execution** created by instantiating an operator inside a DAG.

**Example:**

```python
task1 = BashOperator(
    task_id="extract",
    bash_command="echo extracting"
)

task2 = BashOperator(
    task_id="load",
    bash_command="echo loading"
)

task1 >> task2
```

**Meaning:**

```
extract → load
```

**Interview phrase:**

> “Tasks should be independent, atomic, and idempotent.”

---

### 1.1.4 DAG to DAG Trigger

**Interview Question:** Can one DAG trigger another DAG?

✅ Yes, using `TriggerDagRunOperator`

**Example:**

```python
from airflow.operators.trigger_dagrun import TriggerDagRunOperator

TriggerDagRunOperator(
    task_id="trigger_reporting_dag",
    trigger_dag_id="reporting_dag"
)
```

**Real-world use case:**

* Ingestion DAG finishes
* Validation DAG starts
* Reporting DAG starts

**Interview line:**

> “DAG-to-DAG triggering enables modular and loosely coupled workflows.”

---

## 1.2 Scheduler

**Interview Question:** What is the Scheduler?

**Answer:**
The scheduler continuously monitors DAGs and decides **when tasks should run** based on schedules and dependencies.

**Scheduler DOES:**

* Parses DAG files
* Creates task instances
* Queues tasks

**Scheduler DOES NOT:**

* Execute tasks

**Interview one-liner:**

> “Scheduler decides *when* tasks run, not *how* they run.”

---

## 1.3 Executor

**Interview Question:** What is an Executor?

**Answer:**
The executor defines **how and where tasks are executed**.

**Common Executors:**

| Executor           | Usage                  |
| ------------------ | ---------------------- |
| SequentialExecutor | Testing only           |
| LocalExecutor      | Single machine         |
| CeleryExecutor     | Distributed production |
| KubernetesExecutor | Cloud-native           |

⚠️ **Important distinction:**

* Scheduler schedules tasks
* Executor executes tasks

---

## 1.4 Operators (Types)

**Interview Question:** What are the types of operators?

### Action Operators

* BashOperator
* PythonOperator
* SparkSubmitOperator

### Transfer Operators

* S3ToGCSOperator
* AzureBlobStorageToADLSGen2Operator

### Control Flow Operators

* BranchPythonOperator
* ShortCircuitOperator
* TriggerDagRunOperator

**Best Practice:**

> One task should perform **one responsibility only**.

---

## 🧠 Final Interview Summary (Memorize This)

> “Airflow uses DAGs written in Python to define workflows.
> The scheduler determines when tasks should run.
> Tasks are created from operators.
> The executor defines how tasks are executed.
> This architecture enables scalable workflow orchestration.”

---

## ⚔️ Rapid-Fire Interview Q&A

* Is Airflow an ETL tool? → ❌ No
* Does scheduler execute tasks? → ❌ No
* Can DAGs trigger other DAGs? → ✅ Yes
* Can tasks share large data? → ❌ No (only metadata via XComs)
* Is Airflow streaming? → ❌ Batch orchestration only

---

✅ **This document is interview-ready and printable.**
