# Apache-Airflow
We will see required Installation and implementation of Apache-Airflow setup.

Prerequisites:
    1. Python3+
    2. Any IDE
    3. VirtualBox(the latest version)
    4. Virtual Machine(AirflowVM.ova)
    5. Airflow

**Apache Airflow:**
    Apache Airflow is an open-source platform to programmatically author, schedule and monitor workflows.

Core Components:
    1. Web Server: Flask server with gunicorn serving the UI
    2. Scheduler: Daemon in change of scheduling workflows
    3. Metastore: Database where Metadata are stored
    4. Executor: class defining how your tasks should be executed
    5. Worker: Process/Sub process executing your task

DAG:
    In DAG there is no loop occurs and DAG in Airflow is nothing but data-pipelines. 
    Mentioned or given tasks will get executed by priority wise.

Operator:
    It is used to run the task

Types of Operators:
    1. Action Operators
        Execution function or commands.
        ex. Bash operator and python operator to run python functions.
    2. Transfer Operator
        Transferring data between source to destinations.
    3. Sensor Operator:
        Automatically handles conditions like if lines of code needs to be executed  upon some conditions 
        it will search for inputs based on provided parameters if there is present.

Task and Task Instance:
    It is in queue and ready to run(not yet started for execution).
    when the task is ready to run its task instance is created.


**Note:**
Airflow 2.1.2 doesn't support Celery 5 yet.
    Long story short: just downgrade Celery provider:

    > $ pip install --upgrade apache-airflow-providers-celery==2.0.0
    It will resolve the flower UI service issue and worker also.