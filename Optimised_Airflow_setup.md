# 🚀 Apache Airflow on Windows using WSL2 (Optimized for 8 GB RAM)

This guide explains **why** and **how** to run Apache Airflow on Windows using **WSL2**, how to **start it daily after restart**, and a **clear comparison of WSL2 vs Docker**.

This setup is:

* ✅ Stable
* ✅ Industry‑correct
* ✅ Lightweight for 8 GB RAM
* ✅ Closest to real production Linux environments

⚠️ **If anything goes wrong, always verify the *Important Notes* section below.**

---

# 💥 Why NOT Docker? 

Docker is powerful — but **powerful does not mean appropriate for beginners**.

### 🐳 Why Docker Looks Attractive (But Isn’t Ideal Initially)

Docker tutorials often promise:

* "One command setup"
* "Production-like environment"

In reality on Windows + 8 GB RAM:

* ❌ Containers compete heavily for memory
* ❌ Airflow + Docker + Windows = frequent crashes
* ❌ Debugging DAGs becomes harder
* ❌ File sync issues with volumes
* ❌ SQLite behaves poorly without careful volume tuning

You end up learning **Docker problems**, not **Airflow concepts**.

---

## 💥 Why WSL2 Wins for Learning Airflow

WSL2 gives you:

* ✅ Real Linux kernel (same as prod VMs)
* ✅ Native filesystem performance
* ✅ No container abstraction confusion
* ✅ Simple Python debugging
* ✅ Perfect for SQLite + SequentialExecutor

You focus on:

* DAGs
* Operators
* Scheduling
* Dependencies

Not on:

* Ports
* Volumes
* Containers

---

## 💥 Industry Reality (Important Truth)

* **Developers** often use Linux/WSL locally
* **Teams** use Docker only when collaboration is needed
* **Production** uses managed Airflow or Kubernetes

So the learning path should be:

```
WSL2 → Airflow Concepts → Docker → Kubernetes / MWAA
```

Skipping WSL2 and jumping straight to Docker is like:

> Learning Kubernetes before learning Linux 

---

### 🏁 Verdict (From My Perspective):

> **Docker is NOT wrong. It’s just NOT step one.**

* 🟢 WSL2 → Best for beginners, interviews, solo learning
* 🟡 Docker → Best after you understand Airflow deeply

This guide intentionally uses **WSL2 first**, because **clarity beats complexity** every single time.

---

# 💥 WSL2 vs Docker (CLEAR COMPARISON)

| Feature               | WSL2            | Docker       |
| --------------------- | --------------- | ------------ |
| Stability on Windows  | ⭐⭐⭐⭐⭐           | ⭐⭐⭐          |
| RAM usage             | Low             | High         |
| Setup complexity      | Simple          | Complex      |
| SQLite support        | Works perfectly | Needs tuning |
| Learning friendly     | ✅ Best          | ⚠️ Heavy     |
| Production similarity | High            | Very High    |

**Verdict:**

* **WSL2** → Best for learning, interviews, local dev
* **Docker** → Best for team setups & prod‑like orchestration

---


---

## 📌 Why WSL2 (and NOT Native Windows)

Apache Airflow is **Linux‑native**.

Running Airflow directly on Windows often fails due to:

* File‑locking issues (SQLite)
* NTFS permission conflicts
* Windows Defender / Antivirus interference
* Path and symlink problems

 **WSL2 provides a real Linux kernel**, so Airflow behaves exactly like it does on production servers.

---

# 💥 WSL Setup🛠️

## 📂 Architecture Overview

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

## 🧪 Prerequisites

* Windows 10 / 11
* Minimum **8 GB RAM**
* Internet access
* WSL2 enabled

---

## ✔ STEP 1 — Install WSL2 + Ubuntu

Open **PowerShell as Administrator**:

```bash
wsl --install
```

Restart if prompted.

After restart:

1. Open **Ubuntu** from Start Menu
2. Create a Linux user (lowercase only)

Example:

```
username: karanwsl
password: ******
```

---

## ✔ STEP 2 — Install Python 3.10 (Supported Version)

Inside Ubuntu terminal:

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

## ✔ STEP 3 — Create Airflow Project & Virtual Environment

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

## ✔ STEP 4 — Install Apache Airflow (Stable)

```bash
pip install apache-airflow==2.8.4 \
 --constraint https://raw.githubusercontent.com/apache/airflow/constraints-2.8.4/constraints-3.10.txt
```

---

## ✔ STEP 5 — Initialize Airflow

```bash
export AIRFLOW_HOME=~/airflow_home
airflow db init
```

Creates:

```
airflow.cfg
airflow.db
dags/
logs/
plugins/
```

---

## ✔ STEP 6 — Create Admin User

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

## ✔ STEP 7 — Start Airflow Services🚀

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

## 🌐 STEP 8 — Open Airflow UI

Open browser on Windows:

```
http://localhost:8080
```

Login:

```
admin / admin
```

---

# 💥 Daily Startup After PC Restart🔁

Every time you restart your PC:

### ✔ Open Ubuntu

```bash
wsl -d Ubuntu -u karanwsl
```

### ✔ Start Scheduler

```bash
cd ~/airflow
source venv/bin/activate
export AIRFLOW_HOME=~/airflow_home
airflow scheduler
```

### ✔ Start Webserver (New Terminal) 

```bash
cd ~/airflow
source venv/bin/activate
export AIRFLOW_HOME=~/airflow_home
airflow webserver -p 8080
```

### ✔ Open UI 

```
http://localhost:8080
```

---

## 💥 Make AIRFLOW_HOME Permanent (Recommended)

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

# 💥 The **Third Terminal** (Daily Developer Workflow)

One of the most common confusions for beginners is **which terminal is used for what**. This section clarifies the **third terminal**, its purpose, and why it is critical for learning and daily Airflow usage.

---

##  The Three-Terminal Mental Model (Industry Reality)

When working with Airflow locally, you should think in **three separate terminals**, each with a **single responsibility**.

###  ✔  Terminal 1 — Scheduler (Brain)

Purpose:

* Continuously scans the `dags/` folder
* Parses DAG files
* Decides *when* tasks should run

Command:

```bash
airflow scheduler
```

Rule:

> ❌ Never edit files here
> ❌ Never stop it unless needed

---

### ✔  Terminal 2 — Webserver (UI)

Purpose:

* Serves the Airflow UI (`localhost:8080`)
* Displays DAGs, task states, logs, graphs

Command:

```bash
airflow webserver -p 8080
```

Rule:

> ❌ Never write code here
> ❌ Restart only if UI glitches

---

### ✔  Terminal 3 — **Developer / Working Terminal** (MOST IMPORTANT)

Purpose:

* Create and edit DAG files
* Clean DAGs
* Run validations
* Perform learning & experimentation

This is the terminal you actively **type in all day**.

Typical commands used **today**:

```bash
cd $AIRFLOW_HOME/dags
nano first_dag.py
nano dependency_dag.py
ls
python dependency_dag.py
touch dependency_dag.py
```

Use cases:

* Writing DAG logic
* Debugging missing DAGs
* Fixing syntax errors
* Cleaning example DAG clutter

Golden rule:

>  **Scheduler reads. Webserver shows. Third terminal builds.**

---

# 💥 WHAT TO DO AFTER SETUP (VERY IMPORTANT)🧹

## ✔ Disable Example DAGs (MANDATORY)

```bash
nano $AIRFLOW_HOME/airflow.cfg
```

Set:

```ini
load_examples = False
```

Restart scheduler + webserver.

---

## ✔ Delete Extra DAGs

```bash
rm -f $AIRFLOW_HOME/dags/*.py
```

Clean UI = faster learning.

---

## ✔ Create Your First DAG

```bash
cd $AIRFLOW_HOME/dags
nano first_dag.py
```

```python
from airflow import DAG
from airflow.operators.bash import BashOperator
from datetime import datetime

with DAG(
    dag_id="first_dag",
    start_date=datetime(2024, 1, 1),
    schedule_interval=None,
    catchup=False
) as dag:

    hello = BashOperator(
        task_id="say_hello",
        bash_command="echo 'Hello Airflow 🚀'"
    )
```

Trigger it from UI and verify logs.

---

## ✔ Verify Correct Folder Usage

✅ Always use:

```
/home/<user>/airflow_home
```

❌ Never use:

```
/mnt/c/
```

---


## ⚠️ Important Notes (READ THIS)

* SQLite + SequentialExecutor = **DEV ONLY**
* Never run Airflow from `/mnt/c`
* Always activate virtual environment
* Scheduler **and** Webserver must both be running

---

## 🏁 Final Verdict

WSL2 is the **most reliable, clean, and industry‑correct** way to run Apache Airflow on a Windows laptop.

You now have a setup that:

✅ Matches real Linux production
✅ Avoids Windows filesystem bugs
✅ Runs smoothly on 8 GB RAM

---

## 🎯 What To Do Next

* Create multiple DAGs
* Learn PythonOperator
* Understand Scheduler vs Executor
* Learn XCom, Sensors, Triggers
* Build a mini ETL pipeline
* 
---

## 💥 CAN WE RUN AIRFLOW COMMANDS IN DETACHED MODE?

### ✅ **YES**, in multiple **Linux-native ways** inside WSL.

We have **4 correct options**.
I’ll rank them from **BEST → BASIC**.

---


## 💥 WHY DETACH MODE MATTERS FOR AIRFLOW⚠️

#### Airflow needs long-running processes:

* Scheduler
* Webserver

### You cannot sit and watch logs all day!!! 
So detach mode allows:
* ✔ Background execution
* ✔ Laptop sleep / resume
* ✔ Multiple terminals free

---

## 💥 OPTION 1 — `tmux` (BEST & MOST USED)🥇

This is what **senior engineers actually use**.

### Why `tmux`?

✔ Runs processes in background
✔ Survives terminal close
✔ Easy attach / detach
✔ Lightweight (perfect for WSL)

---

## ✔  Install tmux (one time)

```bash
sudo apt install -y tmux
```

---

###  **You CANNOT safely “attach” an already-running Airflow scheduler/webserver to tmux**

If they were started in a **normal terminal**.

Why?

* Those processes are **bound to that terminal’s TTY**
* tmux creates a **new virtual TTY**
* Linux does **not** support moving a live process between TTYs (by default)

⚠️ Tools like `reptyr` exist, but:

* require root
* unreliable
* NOT recommended
* NOT interview-expected

 **Professional practice is: stop → restart in tmux**.

This is NOT a limitation of you — it’s how Linux works.

---

## 💥 CORRECT & PROFESSIONAL WAY (USED EVERYWHERE)

### ✔  SCENARIO YOU ARE IN (If your terminals in running)

* Terminal 1 → `airflow scheduler` running
* Terminal 2 → `airflow webserver` running
* You want:

  * close terminals
  * keep Airflow running
  * see logs later

### ✔ The RIGHT solution:

 **Restart both inside tmux**

---

## 💥 STEP-BY-STEP: MOVE AIRFLOW INTO DETACHED MODE (tmux)

## ✔  STEP 1 — STOP CURRENT PROCESSES

In both terminals press:

```
CTRL + C
```

This stops:

* scheduler
* webserver

(Stopping is safe — no data loss.)

---

## ✔  STEP 2 — START tmux SESSION

```bash
tmux new -s airflow
```

You are now **inside tmux**.

<img width="1851" height="430" alt="Screenshot 2025-12-18 163842" src="https://github.com/user-attachments/assets/754e117f-fd68-4857-b3d9-5e17f9bc5a97" />

---

## ✔  STEP 3 — START SCHEDULER (INSIDE tmux)

```bash
cd ~/airflow
source venv/bin/activate
export AIRFLOW_HOME=~/airflow_home
airflow scheduler
```

<img width="1232" height="421" alt="Screenshot 2025-12-18 164041" src="https://github.com/user-attachments/assets/5c66d159-0826-4282-aa44-d1484dfee00a" />

---

## ✔  STEP 4 — DETACH (KEEP IT RUNNING)

Press:

```
CTRL + B  →  D
```

Scheduler is now running **in background** ✅

<img width="1218" height="54" alt="Screenshot 2025-12-18 164248" src="https://github.com/user-attachments/assets/35c4bcc4-b09e-4e49-b440-45cd02f6bf58" />

---

## ✔  STEP 5 — ADD WEBSERVER IN SAME tmux SESSION

Reattach:

```bash
tmux attach -t airflow
```

Create new window:

```
CTRL + B  →  C
```

Now run:

```bash
cd ~/airflow
source venv/bin/activate
export AIRFLOW_HOME=~/airflow_home
airflow webserver -p 8080
```

<img width="1086" height="173" alt="Screenshot 2025-12-18 164437" src="https://github.com/user-attachments/assets/5489e385-c113-4eac-914d-f15ea36b0faa" />

Detach again:

```
CTRL + B → D
```

<img width="1060" height="109" alt="Screenshot 2025-12-18 164452" src="https://github.com/user-attachments/assets/f6564c9c-4461-46a5-9830-1b31eb483927" />


---

###  ✔  RESULT (THIS IS WHAT YOU WANT)🎉

✔ You can close all terminals
✔ Airflow keeps running
✔ UI stays available at `localhost:8080`
✔ Logs are visible when you reattach

<img width="1919" height="979" alt="Screenshot 2025-12-18 164531" src="https://github.com/user-attachments/assets/3f4b0438-051d-4d7b-8f40-22bfe72ab858" />

---

## 💥 HOW TO SEE OUTPUTS LATER (VERY IMPORTANT)🔁

## ✔  Reattach anytime:

```bash
tmux attach -t airflow
```

Switch between:

* scheduler window
* webserver window

Using:

```
CTRL + B → N   (next)
CTRL + B → P   (previous)
```

---

## ✔  TERMINATE tmux COMPLETELY (STOP EVERYTHING)❌

If you are done for now and want to stop Airflow:

```
tmux kill-session -t airflow
```

Now verify:
```
tmux ls
```

Expected:
```
no server running on /tmp/tmux-...
```
---

## ✔  WHAT IF YOU REALLY DON’T WANT TO RESTART?

Only alternative (not recommended):

```bash
nohup airflow scheduler > scheduler.log 2>&1 &
nohup airflow webserver -p 8080 > webserver.log 2>&1 &
```

Then view logs:

```bash
tail -f scheduler.log
tail -f webserver.log
```

❌ No interaction
❌ Harder debugging
❌ Less professional

---

## 🎯 VERY IMPORTANT

If asked:

> Can you move a running process to detached mode?

Answer:

> **“No, a running process can’t be retroactively attached to tmux. Best practice is to restart long-running services inside tmux or nohup.”**


---


## 💥 OPTION 2 — `nohup` (SIMPLE & EFFECTIVE)🥈

Good if you don’t want tmux.

---

### Start scheduler

```bash
nohup airflow scheduler > scheduler.log 2>&1 &
```

### Start webserver

```bash
nohup airflow webserver -p 8080 > webserver.log 2>&1 &
```

Check running:

```bash
ps aux | grep airflow
```

Stop later:

```bash
pkill -f "airflow scheduler"
pkill -f "airflow webserver"
```

⚠️ Logs go into `.log` files.

---

## 💥 OPTION 3 — `&` (VERY BASIC)🥉

```bash
airflow scheduler &
airflow webserver -p 8080 &
```

⚠️ Process **dies if terminal closes**
❌ Not recommended

---

## 💥 OPTION 4 — systemd (NOT RECOMMENDED)🏅

Possible in WSL, but:

* complex
* overkill
* not needed for learning

🚫 Skip this.

---

# 🏆 RECOMMENDED 

### **tmux** ✅

Because:

* You’re learning
* You restart laptop
* You want control
* You want professional practice

---

# 🎯 QUICK DECISION GUIDE

| Tool   | Use case             |
| ------ | -------------------- |
| tmux   | ⭐ BEST (recommended) |
| nohup  | Simple background    |
| &      | Temporary            |
| Docker | Production           |

---

















