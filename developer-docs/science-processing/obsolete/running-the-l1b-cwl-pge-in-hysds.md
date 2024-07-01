# Running the L1B CWL PGE in HySDS

Follow the steps below to run the L1B CWL PGE in HySDS:

#### 1. Clone the Repository

* Ensure you have the repository `unity-sps-workflows` available.
*   Fetch the `l1b_cwl_dind` branch.

    > **Note:** You can also use the `main` branch once the changes have been merged.

#### 2. Update Configuration

* Navigate to the file named `ssips_L1b_workflow.yml`.
* Provide your AWS credentials including fields such as `aws_access_key_id`, `aws_session_token`, etc.

#### 3. Copy Directory to the Verdi Pod

* Use the following command to copy the `unity-sps-workflows` directory to the verdi pod:
  * `$ kubectl cp /path/to/unity-sps-workflows verdi-668859cbf5-xjmkv:/tmp/unity-sps-workflows -c Verdi`

> **Note:** The path might vary based on your configuration.

#### 4. Access the Verdi Pod

* Execute the following command to get shell access into the verdi pod:
  * `$ kubectl exec -it verdi-668859cbf5-xjmkv -c verdi -- bash`

> **Note:** The pod name might vary based on your setup.

#### 5. Build the HySDS Job

* Run the `build_container.py` script to create the HySDS job using the following command:
  * `$ python /tmp/unity-sps-workflows/docker/build_container.py --image unity-sps-workflows:develop -f /tmp/unity-sps-workflows`

#### 6. Execute the Job

* Head over to the HySDS UI and initiate the job.
* Ensure you select the `verdi-job_worker` queue for execution.

#### 7. Review Logs

* Once the job is complete, you can review the logs.
* They will be located in the path `/tmp/data/work/jobs/2022/` and can be identified by the file `_stderr.txt`.
