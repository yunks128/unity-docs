# Executing a Job

There are two methods for executing jobs within the system.

1. [Via Airflow UI](executing-jobs-via-ui.md). Allows for a user to use the underlying airflow implementation to execute application packages or airflow custom workflows (airflow refers to these as DAGS).
2. [Via Web processing service (WPS)](executing-jobs-via-wps.md). Using WPS abstracts the call to execute a job from the underlying implementaiton, and will work with future implementations of the processing system.

