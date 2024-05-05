## Apache Airflow: Orchestrating Workflows on Docker

This project leverages Apache Airflow, an open-source platform for managing workflows. Airflow excels at scheduling, authoring, and monitoring workflows defined as Python-coded Directed Acyclic Graphs (DAGs). 

### Key Components

* **DAG (Directed Acyclic Graph):** A Python script defining the workflow structure and task dependencies.
* **Web Server:** A user interface (built with Flask) for monitoring DAG execution and triggering workflows.
* **Metadata Database:** Stores workflow execution data, enabling read/write operations.
* **Scheduler:** Schedules DAG execution, retrieving and updating task statuses within the database.

### Prerequisites

* Docker installed on your system.
* Docker Compose installed on your system.

### Setting Up Airflow with Docker Compose

1. **Download the docker-compose.yaml file:**

   ```bash
   curl -LfO 'https://airflow.apache.org/docs/apache-airflow/2.1.1/docker-compose.yaml'
   ```

2. **Initialize the Environment:**

   - Create necessary directories: `dags`, `logs`, and `plugins`.
   - Set environment variables using a `.env` file:

     ```
     AIRFLOW_UID=$(id -u)
     AIRFLOW_GID=0
     ```

   - Initialize the database and create the first user account:

     ```bash
     docker-compose up airflow-init
     ```

3. **Start Airflow Services:**

   ```bash
   docker-compose up
   ```

### Accessing the Airflow Web Interface

Once the cluster starts, access the web interface at http://localhost:8080. Default credentials are username: `airflow` and password: `airflow`.

### Defining Your DAG

1. **Create a Python DAG file** within the `dags` directory.

2. **Import necessary libraries:**

   ```python
   from datetime import timedelta

   from airflow import DAG

   from airflow.operators.bash import BashOperator
   from airflow.utils.dates import days_ago
   ```

3. **Define DAG Arguments:**

   Set arguments (owner, schedule, email, retries, etc.) for your workflow using a dictionary.

4. **Define the DAG:**

   ```python
   dag = DAG(
       dag_id='your_dag_id',
       default_args=args,
       start_date=datetime.now() - timedelta(minutes=15),
       schedule_interval='your_schedule_interval',  # Example: '0 0 * * *' (daily at midnight)
       dagrun_timeout=timedelta(minutes=60),
       tags=[]
   )
   ```

5. **Define Tasks:**

   Use operator objects (e.g., `BashOperator`, `PythonOperator`) to create tasks within your DAG. These operators specify the actions to be performed.

   ```python
   t1 = BashOperator(
       task_id='print_date',
       bash_command='date',
       dag=dag,
   )

   t2 = BashOperator(
       task_id='sleep',
       depends_on_past=False,
       bash_command='sleep 5',
       dag=dag,
   )
   ```

   You can chain tasks to define dependencies between them.

**Refer to the `airflow/dags/` directory for additional DAG creation examples.**
