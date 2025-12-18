# 🚀 Apache Airflow on Windows using WSL2 (Optimized for 8 GB RAM)

This guide explains **why and how to run Apache Airflow on Windows using WSL2**, how to **start it daily after restart**, and a **clear comparison of WSL2 vs Docker**.

This setup is:

* ✅ Stable
* ✅ Industry‑correct
* ✅ Lightweight for **8 GB RAM**
* ✅ Closest to real production Linux environments

---

## 📌 Why WSL2 (and not native Windows)

Apache Airflow is **Linux‑native**.

Running Airflow directly on Windows often fails due to:

* File‑locking issues (SQLite)
* NTFS permissions
* Antivirus / Defender interference
* Path & symlink problems

👉 **WSL2 provides a real Linux kernel**, so Airflow behaves exactly as intended.

---

## 🧠 Architecture Overview

```
Windows
  └── WSL2 (Ubuntu – Linux Kernel)
        ├── Python 3.10 (venv)
        ├── Apache Airflow
        ├── SQLite (metadata DB – dev only)
        ├── Scheduler
        └── Webserver (UI :8080)
```

---

## 🧩 Prerequisites

* Windows 10 / 11
* 8 GB RAM (minimum)
* Internet access
* WSL2 enabled

---

## 🛠️ STEP 1 — Install WSL2 + Ubuntu

Open **PowerShell as Administrator**:

```powershell
wsl --install
```

Restart if prompted.

After restart:

* Open **Ubuntu** from Start Menu
* Create a Linux user (lowercase only)

Example:

```
username: karanwsl
password: ******
```

---

## 🛠️ STEP 2 — Install Python 3.10 (Supported Version)

Inside **Ubuntu terminal**:

```bash
sudo apt update
sudo apt install -y software-properties-common
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt update
sudo apt install -y python3.10 python3.10-venv python3-pip
```

Verify:

```bash
python3.10 --version
```

---

## 🛠️ STEP 3 — Create Airflow Project & Virtual Environment

```bash
cd ~
mkdir airflow
cd airflow
python3.10 -m venv venv
source venv/bin/activate
```

You should see:

```
(venv) user@DESKTOP:~/airflow$
```

---

## 🛠️ STEP 4 — Install Apache Airflow (Stable)

```bash
pip install apache-airflow==2.8.4 \
 --constraint https://raw.githubusercontent.com/apache/airflow/constraints-2.8.4/constraints-3.10.txt
```

---

## 🛠️ STEP 5 — Initialize Airflow

```bash
export AIRFLOW_HOME=~/airflow_home
airflow db init
```

Creates:

* airflow.cfg
* airflow.db
* dags/
* logs/

---

## 🛠️ STEP 6 — Create Admin User

```bash
airflow users create \
  --username admin \
  --password admin \
  --firstname Admin \
  --lastname User \
  --role Admin \
  --email admin@test.com
```

---

## ▶️ STEP 7 — Start Airflow (UI)

### Terminal 1 — Scheduler

```bash
cd ~/airflow
source venv/bin/activate
export AIRFLOW_HOME=~/airflow_home
airflow scheduler
```

### Terminal 2 — Webserver

```bash
cd ~/airflow
source venv/bin/activate
export AIRFLOW_HOME=~/airflow_home
airflow webserver -p 8080
```

---

## 🌐 STEP 8 — Open UI

Open browser (Windows):

```
http://localhost:8080
```

Login:

```
admin / admin
```

---

## 🔁 Daily Startup After PC Restart

Every time you restart your PC:

### 1️⃣ Open Ubuntu

```powershell
wsl -d Ubuntu -u karanwsl
```

### 2️⃣ Start Scheduler

```bash
cd ~/airflow
source venv/bin/activate
export AIRFLOW_HOME=~/airflow_home
airflow scheduler
```

### 3️⃣ Start Webserver (New Terminal)

```bash
cd ~/airflow
source venv/bin/activate
export AIRFLOW_HOME=~/airflow_home
airflow webserver -p 8080
```

### 4️⃣ Open UI

```
http://localhost:8080
```

---

## ⚡ Make AIRFLOW_HOME Permanent (Optional)

```bash
nano ~/.bashrc
```

Add:

```bash
export AIRFLOW_HOME=$HOME/airflow_home
```

Apply:

```bash
source ~/.bashrc
```

---

## 🆚 WSL2 vs Docker (CLEAR DIFFERENCE)

| Feature               | WSL2            | Docker              |
| --------------------- | --------------- | ------------------- |
| Stability on Windows  | ⭐⭐⭐⭐⭐           | ⭐⭐⭐                 |
| RAM usage             | Low             | High                |
| Setup complexity      | Simple          | Complex             |
| SQLite support        | Works perfectly | Needs volume tuning |
| Learning friendly     | ✅ Best          | ⚠️ Heavy            |
| Production similarity | High            | Very High           |

### Verdict:

* **WSL2** → Best for learning, interviews, local dev
* **Docker** → Best for team setups & prod-like orchestration

---

## ⚠️ Important Notes

* SQLite + SequentialExecutor = **DEV ONLY**
* Never run Airflow from `/mnt/c`
* Always activate venv
* Scheduler + Webserver must both be running

---

## 🏁 Final Verdict

> **WSL2 is the most reliable, clean, and industry‑correct way to run Apache Airflow on a Windows laptop.**

You now have a setup that:

* Matches real Linux production
* Avoids Windows filesystem bugs
* Is stable on 8 GB RAM

---

🎯 Next steps (optional):

* Create your first DAG
* Understand Scheduler vs Executor
* Sensors, Triggers, DAG‑to‑DAG
* Interview‑ready questions
