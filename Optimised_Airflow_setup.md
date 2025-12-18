## 🚀 Ultra‑Light Apache Airflow (Optimized for 8 GB RAM)

This setup is **clean, fast, laptop‑friendly**, and **perfect for Airflow basics → intermediate practice**.
We intentionally **remove Celery, Redis, Triggerer, Flower** and use **LocalExecutor**.

---

## 🎯 What this optimized setup gives you

✅ Only **3 containers** (Postgres, Scheduler, Webserver)
✅ No Redis, no Celery workers
✅ Stable for learning & interviews
✅ No laptop freezing 🔥

---

## 📦 Folder structure (expected)

```
airflow-docker/
├── dags/
├── logs/
├── plugins/
├── config/
├── docker-compose.yaml
└── .env
```

---

## 🔑 .env (MUST BE EXACT)

```env
AIRFLOW_UID=50000
AIRFLOW_IMAGE_NAME=apache/airflow:2.8.4
```

---

## 🐳 OPTIMIZED docker-compose.yaml

```yaml
version: "3.8"

x-airflow-common: &airflow-common
  image: ${AIRFLOW_IMAGE_NAME}
  env_file:
    - .env
  environment:
    AIRFLOW__CORE__EXECUTOR: LocalExecutor
    AIRFLOW__DATABASE__SQL_ALCHEMY_CONN: postgresql+psycopg2://airflow:airflow@postgres/airflow
    AIRFLOW__CORE__LOAD_EXAMPLES: "true"
    AIRFLOW__CORE__DAGS_ARE_PAUSED_AT_CREATION: "true"
    AIRFLOW__CORE__FERNET_KEY: ""
    AIRFLOW__SCHEDULER__ENABLE_HEALTH_CHECK: "true"
  volumes:
    - ./dags:/opt/airflow/dags
    - ./logs:/opt/airflow/logs
    - ./plugins:/opt/airflow/plugins
    - ./config:/opt/airflow/config
  user: "${AIRFLOW_UID}:0"

services:

  postgres:
    image: postgres:16
    environment:
      POSTGRES_USER: airflow
      POSTGRES_PASSWORD: airflow
      POSTGRES_DB: airflow
    volumes:
      - postgres-db-volume:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "airflow"]
      interval: 10s
      retries: 5
    restart: always

  airflow-webserver:
    <<: *airflow-common
    command: webserver
    ports:
      - "8080:8080"
    depends_on:
      postgres:
        condition: service_healthy
    deploy:
      resources:
        limits:
          memory: 1g
          cpus: "1.0"

  airflow-scheduler:
    <<: *airflow-common
    command: scheduler
    depends_on:
      postgres:
        condition: service_healthy
    deploy:
      resources:
        limits:
          memory: 1g
          cpus: "1.0"

volumes:
  postgres-db-volume:
```

---

## ▶️ How to start (CLEAN WAY)

```powershell
docker compose down -v
docker system prune -f
docker compose up -d
```

Open UI 👉 **[http://localhost:8080](http://localhost:8080)**
Login: `airflow / airflow`

---

# 🧠 PRACTICE PLAN: BASIC → INTERMEDIATE (INTERVIEW‑READY)

## 🟢 LEVEL 1 — Core Basics

✔ What is DAG, Task, Operator
✔ dag_id, start_date, schedule, catchup
✔ BashOperator, PythonOperator

### Practice

* Create a DAG that prints today’s date
* Run manually vs scheduled
* Pause & unpause DAG

---

## 🟡 LEVEL 2 — Dependencies & Control Flow

✔ task1 >> task2
✔ BranchPythonOperator
✔ Trigger rules (`all_success`, `one_failed`)

### Practice

* Branch DAG: weekday vs weekend
* Fail a task and observe downstream behavior

---

## 🟡 LEVEL 3 — Scheduling & Backfills

✔ cron vs timedelta
✔ catchup = true / false
✔ backfill command

### Practice

* DAG that runs daily from Jan 1
* Enable catchup and observe runs

---

## 🟠 LEVEL 4 — XComs & Variables

✔ XCom push / pull
✔ Airflow Variables
✔ Connections (UI)

### Practice

* Pass value from Task A → Task B
* Store env name in Variable

---

## 🟠 LEVEL 5 — Sensors & External Triggers

✔ FileSensor
✔ TimeSensor
✔ TriggerDagRunOperator

### Practice

* DAG waits for file → then runs
* Trigger DAG‑B from DAG‑A

---

## 🔵 LEVEL 6 — Failure Handling & Retry

✔ retries, retry_delay
✔ email_on_failure
✔ SLA miss

### Practice

* Task fails first 2 times, passes 3rd

---

## 🔵 LEVEL 7 — Real‑World Mini Projects

### 🔥 Project 1: File‑Driven Pipeline

* Sensor waits for CSV
* Python task validates data
* Bash task moves file

### 🔥 Project 2: Multi‑DAG Orchestration

* Parent DAG triggers child DAG
* Child DAG returns status

### 🔥 Project 3: Parameterized DAG

* DAG takes runtime params
* Same DAG runs for dev/prod

---

##  GOLD LINES 

> “For local development I use **Airflow with LocalExecutor via Docker Compose**, and switch to **CeleryExecutor in production**.”

> “I’ve handled scheduling, sensors, XComs, retries, and DAG‑to‑DAG orchestration.”

---

## 🏁 Final Verdict

✅ Optimized
✅ Stable
✅ Laptop‑friendly
✅ Interview‑ready

---

👉 Next steps available:

* Ready‑made **practice DAGs**
* **Interview Q&A** based on your setup
* **ADF vs Airflow comparison**

Say the word 🚀
