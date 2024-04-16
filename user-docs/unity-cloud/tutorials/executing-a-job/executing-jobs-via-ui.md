# Executing jobs via UI

## Using Airflow to Execute and Monitor jobs

### Airflow

<figure><img src="../../../../.gitbook/assets/Screenshot 2024-04-16 at 10.19.36 AM.png" alt=""><figcaption><p>The screen showing all workflows and thier recent runs and status</p></figcaption></figure>

The UI used to submit and execute a job is currently based on the [Apache Airflow](https://airflow.apache.org/) system. Airflow allows for the developing, execution, and monitoring of workflows through an artifact called a Directed Acyclic Graph (DAG). The DAG is the "thing" that is executed, and can be though of as one or more steps to execute in a workflow.

The entry most users will be interested in is the "cwl-dag" item, which is a generic workflow for executing **any** CWL document. Here we are interested in the CWL that is bundled with an application package, and the input parameters for that application package.

### Executing a job

<figure><img src="../../../../.gitbook/assets/Screenshot 2024-04-16 at 10.24.10 AM.png" alt=""><figcaption><p>The inputs to the cwl-dag are very primitive, but work for any CWL file.</p></figcaption></figure>

Clicking the triangle (which looks like a 'play' button on the `cwl-dag` entry will present you with the above, simple form. Simply paste the link to your application package workflow.cwl file in the top line, and the parameters in the `CWL workflow parameters` box. This box can take a link to a json or yaml file, or can take a  json string to define the inputs. &#x20;

Once defined, hit trigger and your job will be queued for execution within the system. The amount of time it will take to start, execute and clean up is dependent on many variables, including the number of instances set up in the cluster, the number of current jobs, and the resources/complexity of the algorithm.

### Monitoring a job

Airflow provides a rich environment for monitoring jobs, including timings, concurrent runs, and logs for a given execution. Please refer to the [Apache Airflow user guide](https://airflow.apache.org/docs/apache-airflow/stable/index.html) for more information on this, as well as [screenshots on how to use the UI](https://airflow.apache.org/docs/apache-airflow/stable/ui.html).
