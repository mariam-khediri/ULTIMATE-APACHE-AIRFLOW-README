# **📖 ULTIMATE APACHE AIRFLOW README**  
**From Zero to Workflow Orchestration Hero**  

---

## **🔍 1. What is Apache Airflow?**  
### **Definition**  
Apache Airflow is an **open-source workflow automation platform** for:  
- **Scheduling** complex data pipelines  
- **Monitoring** workflows via rich UI  
- **Orchestrating** ETL, ML, and reporting tasks  

### **Key Features**  
| Feature               | Benefit                                                                 |
|-----------------------|-------------------------------------------------------------------------|
| **DAGs (Directed Acyclic Graphs)** | Define workflows as code in Python.               |
| **Rich CLI & UI**     | Monitor/logs/retry failed tasks.                                       |
| **Extensible**        | 1000+ integrations (AWS, Snowflake, Docker).                          |
| **Scalable**         | Kubernetes-native (Airflow 2.0+).                                     |

---

## **🛠 2. Installation & Setup**  

### **Option 1: Local Setup (Docker)**
```bash
# Clone official Docker-compose file
curl -LfO 'https://airflow.apache.org/docs/apache-airflow/2.6.2/docker-compose.yaml'

# Initialize
docker-compose up airflow-init
docker-compose up
```
Access UI at `http://localhost:8080` (login: `airflow/airflow`)

### **Option 2: Python PIP**
```bash
pip install apache-airflow
airflow db init
airflow webserver --port 8080
airflow scheduler
```

---

## **📊 3. Core Concepts**  
### **1. DAGs (Workflows)**  
Example `dag.py`:  
```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime

def say_hello():
    print("Hello Airflow!")

with DAG(
    dag_id="hello_world",
    start_date=datetime(2023, 1, 1),
    schedule_interval="@daily"
) as dag:
    task1 = PythonOperator(
        task_id="print_hello",
        python_callable=say_hello
    )
```

### **2. Operators (Tasks)**  
| Operator           | Purpose                          | Example                      |
|--------------------|----------------------------------|------------------------------|
| `PythonOperator`   | Run Python functions            | `python_callable=clean_data` |
| `BashOperator`     | Execute shell commands          | `bash_command="echo Hi"`     |
| `SnowflakeOperator`| Run Snowflake queries           | `sql="SELECT * FROM table"`  |

### **3. Task Dependencies**  
```python
task1 >> task2  # task1 runs before task2
task1 >> [task2, task3]  # Fan-out
```

---

## **⚡ 4. Intermediate Skills**  
### **Variables & Connections**  
1. **Store credentials**:  
   ```python
   from airflow.models import Variable
   Variable.set("snowflake_user", "admin")
   ```
2. **DB connections**:  
   ```bash
   airflow connections add --conn-type snowflake --conn-login user --conn-password pass
   ```

### **Error Handling**  
```python
from airflow.exceptions import AirflowFailException

def validate():
    if error:
        raise AirflowFailException("Data validation failed!")
```

### **Dynamic DAGs**  
```python
for client in ["clientA", "clientB"]:
    with DAG(f"process_{client}", ...):
        # Client-specific tasks
```

---

## **🚀 5. Advanced Techniques**  
### **Kubernetes Executor**  
1. Install **Airflow on K8s**:  
   ```bash
   helm repo add airflow https://airflow.apache.org
   helm install airflow airflow/airflow
   ```
2. Set executor in `airflow.cfg`:  
   ```ini
   executor = KubernetesExecutor
   ```

### **Data-aware Scheduling**  
```python
with DAG(
    ...
    schedule=Dataset("s3://data/raw.csv")
):
    # Triggers when file updates
```

### **Custom Plugins**  
1. Create `plugins/` folder:  
   ```python
   from airflow.plugins_manager import AirflowPlugin

   class MyPlugin(AirflowPlugin):
       name = "my_plugin"
       operators = [CustomOperator]
   ```

---

## **📚 6. Learning Resources**  
### **Free**  
- [Official Documentation](https://airflow.apache.org/docs/)  
- [Astronomer Webinars](https://www.astronomer.io/events/webinars/)  

### **Paid**  
- **Book**: *Data Pipelines with Apache Airflow* (Manning)  
- **Course**: [Udemy Airflow Bootcamp](https://www.udemy.com/course/the-ultimate-hands-on-course-to-master-apache-airflow/)  

---

## **❓ FAQ**  
**Q: How to backfill data?**  
```bash
airflow dags backfill --start-date 2023-01-01 hello_world
```

**Q: Airflow vs. Luigi?**  
→ Airflow has **better UI/monitoring** and **richer operator ecosystem**.  

**Q: Debugging failed tasks?**  
1. Check logs in UI → **Task Instance Details**.  
2. Test locally:  
   ```python
   from airflow.models import TaskInstance
   ti = TaskInstance(task, execution_date)
   task.execute(ti.get_template_context())
   ```

---

## **🎯 Production Best Practices**  
✅ **Use `@task` decorators** (Airflow 2.0+):  
   ```python
   @task
   def process_data(): pass
   ```

✅ **Monitor via metrics**:  
   - Prometheus endpoint: `http://localhost:8080/metrics`  

✅ **Secure secrets**:  
   ```bash
   airflow config set-value core secret_backend airflow.providers.hashicorp.secrets.vault
   ```

---
