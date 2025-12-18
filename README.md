# Airflow

---

## 1️ Introduction to Apache Airflow

**Definition:**
Apache Airflow is an **open-source workflow orchestration tool** used to **schedule, monitor, and manage data pipelines** using code.

**Important:**

* ❌ Airflow is **NOT** an ETL tool
* ✅ Airflow **orchestrates** tasks, it does not process data

**One-liner**

> “Airflow orchestrates *when* and *in what order* tasks run.”

---

## 1.1 Core Concepts

---

### 1.1.1 DAG Definition

**Question:** What is a DAG in Airflow?

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

**Question:** What are Operators in Airflow?

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

**Gold Point:**

* Operator is a **template**
* Task is an **instance of an operator**

---

### 1.1.3 Tasks

**Question:** What is a Task?

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

**One Line phrase:**

> “Tasks should be independent, atomic, and idempotent.”

---

### 1.1.4 DAG to DAG Trigger

**Question:** Can one DAG trigger another DAG?

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

**One line:**

> “DAG-to-DAG triggering enables modular and loosely coupled workflows.”

---

## 1.2 Scheduler

**Question:** What is the Scheduler?

**Answer:**
The scheduler continuously monitors DAGs and decides **when tasks should run** based on schedules and dependencies.

**Scheduler DOES:**

* Parses DAG files
* Creates task instances
* Queues tasks

**Scheduler DOES NOT:**

* Execute tasks

**one-liner:**

> “Scheduler decides *when* tasks run, not *how* they run.”

---

## 1.3 Executor

**Question:** What is an Executor?

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

**Question:** What are the types of operators?

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

## Summary 

> “Airflow uses DAGs written in Python to define workflows.
> The scheduler determines when tasks should run.
> Tasks are created from operators.
> The executor defines how tasks are executed.
> This architecture enables scalable workflow orchestration.”

---

## ⚔️ Rapid-Fire Q&A

* Is Airflow an ETL tool? → ❌ No
* Does scheduler execute tasks? → ❌ No
* Can DAGs trigger other DAGs? → ✅ Yes
* Can tasks share large data? → ❌ No (only metadata via XComs)
* Is Airflow streaming? → ❌ Batch orchestration only

---

# Apache Airflow Operators – Interview Focused Guide ⚙️🎯

This document explains **commonly asked Airflow operators** with **interview-ready definitions**, **clear examples**, **real-world use cases**, and **common traps**.

---

## 1️ PythonOperator

### Question:

**What is PythonOperator in Airflow?**

### Answer:

The `PythonOperator` is used to execute a **Python callable function** as a task inside a DAG.

> It is mainly used for **lightweight logic**, API calls, validations, and orchestration logic.

### Example:

```python
from airflow.operators.python import PythonOperator

def greet():
    print("Hello from Airflow")

PythonOperator(
    task_id="greet_task",
    python_callable=greet
)
```

### Real-world use cases:

* Calling REST APIs
* Validating data
* Triggering external services
* Preparing parameters for downstream tasks

### Q traps ❌:

* ❌ Not for heavy data processing
* ❌ Not a replacement for Spark jobs

###  one-liner 🏆:

> “PythonOperator runs Python logic, not data processing workloads.”

---

## 2️ Sensor

### Question:

**What is a Sensor in Airflow?**

### Answer:

A Sensor is a **special type of operator** that **waits for a condition to be met** before allowing downstream tasks to run.

### Example (FileSensor):

```python
from airflow.sensors.filesystem import FileSensor

FileSensor(
    task_id="wait_for_file",
    filepath="/data/input.csv",
    poke_interval=30,
    timeout=600
)
```

### Real-world use cases:

* Wait for a file arrival
* Wait for table creation
* Wait for external DAG completion

### Sensor modes:

* `poke` → keeps checking (resource heavy)
* `reschedule` → frees worker (recommended ✅)

### one-liner 🏆:

> “Sensors pause workflow execution until an external condition is satisfied.”

---

## 3️ SubDagOperator (IMPORTANT ⚠️)

### Question:

**What is SubDagOperator? Is it recommended?**

### Answer:

`SubDagOperator` allows running a **DAG inside another DAG**.

⚠️ **IMPORTANT TRUTH:**

* ❌ SubDagOperator is **deprecated / discouraged**
* ❌ Causes scheduler performance issues

### Example (Conceptual):

```python
from airflow.operators.subdag import SubDagOperator

SubDagOperator(
    task_id="subdag_task",
    subdag=subdag_object
)
```

### Why it is discouraged:

* Shares scheduler resources
* Difficult to scale
* Hard to monitor

### Recommended replacement ✅:

* `TriggerDagRunOperator`

### GOLD answer 🏆:

> “SubDagOperator exists but is discouraged; TriggerDagRunOperator is the preferred approach.”

---

## 4️ TriggerDagRunOperator

### Question:

**How do you trigger one DAG from another DAG?**

### Answer:

`TriggerDagRunOperator` is used to **trigger another DAG** from the current DAG.

### Example:

```python
from airflow.operators.trigger_dagrun import TriggerDagRunOperator

TriggerDagRunOperator(
    task_id="trigger_reporting",
    trigger_dag_id="reporting_dag"
)
```

### Real-world use cases:

* Ingestion DAG → Reporting DAG
* Modular pipelines
* Decoupled workflows

### one-liner 🏆:

> “TriggerDagRunOperator enables loosely coupled and modular DAG orchestration.”

---

## 5️ BashOperator

### Question:

**What is BashOperator used for?**

### Answer:

`BashOperator` is used to execute **shell commands** inside Airflow tasks.

### Example:

```python
from airflow.operators.bash import BashOperator

BashOperator(
    task_id="list_files",
    bash_command="ls -l"
)
```

### Real-world use cases:

* Running shell scripts
* Triggering CLI tools
* Calling Spark-submit, dbt, or system commands

### Q traps ❌:

* ❌ Not portable across OS
* ❌ Avoid embedding complex logic

### one-liner 🏆:

> “BashOperator is best for simple command execution and CLI-based integrations.”

---

##  FINAL COMPARISON TABLE 

| Operator              | Purpose             | Key Note               |
| --------------------- | ------------------- | ---------------------- |
| PythonOperator        | Run Python code     | Lightweight logic only |
| Sensor                | Wait for condition  | Use reschedule mode    |
| SubDagOperator        | Nested DAG          | Deprecated / avoid     |
| TriggerDagRunOperator | Trigger another DAG | Best practice          |
| BashOperator          | Run shell commands  | Simple commands only   |

---

## 🎯 Summary

> “Airflow provides different operators for different responsibilities. PythonOperator handles logic, Sensors handle waiting, TriggerDagRunOperator enables DAG orchestration, and BashOperator runs shell commands. SubDagOperator exists but is discouraged in favor of DAG-to-DAG triggering.”

---




