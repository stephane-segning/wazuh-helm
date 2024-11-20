## OWASP ZAP Tekton Pipeline Helm Chart
### Description

This Helm Chart automatically deploys a Tekton pipeline to perform security scans with OWASP ZAP and integrate the results with Wazuh. The pipeline runs a ZAP scan on the specified application and stores the results in a persistent volume.

### Prerequisites

- Kubernetes cluster
- Helm 
- Tekton Pipelines installed
- Persistent Volume Claim (PVC) for storing reports

### Chart Structure

- `templates/`: Contains template files for Kubernetes objects

   - `pipeline.yaml`: Tekton pipeline defining the tasks
   - `pipelinerun.yaml`: Pipeline run
   - `pvc.yaml`: Persistent volume to store reports
   - `role.yaml`: RBAC role for managing pipeline runs
   - `rolebinding.yaml`: Role binding to bind the role to a service account - `store-report.yaml`: Task to store scan reports
   - `zap-scan.yaml`: Task to run the ZAP scan
   - `zap-pipeline-cronjob.yaml`: CronJob to run the pipeline at regular intervals

### Installation

1. **Deploy the Helm Chart**

    ```sh
    helm upgrade -i owasp-zap . -n owasp --create-namespace --values values.yaml 
    ```

    You can change the namespace by modifying the `namespace` value in `values.yaml`.

2. **Storage Class for Local Deployment**

    If deploying locally with Minikube or another local Kubernetes cluster, change the `storageClass` value in `values.yaml` from `gps` to `standard`. For deployment on EKS, keep it as `gps`.

### Usage

1. **Tekton Pipeline**

    The pipeline defined by this chart executes the following steps:
    - Runs an OWASP ZAP scan on the specified application
    - Stores the scan results in a persistent volume

2. **CronJob**

    A CronJob is configured to run the pipeline at regular intervals. The schedule can be adjusted by modifying the `schedule` field in `cronjob.yaml`.

3. **Accessing Reports**

    - A pod named `access-report-pod` runs continuously, storing all scan reports in the `/mnt/output` directory.
    - Other pods created to execute tasks during the PipelineRun are automatically deleted after execution.


### How It Works

1. **Initialization**

    Helm installs all necessary components in the specified namespace, creating RBAC roles, role bindings, and secrets.

2. **Tekton Pipeline**

    The pipeline defined in `pipeline.yaml` is triggered according to the schedule defined in `cronjob.yaml`.

3. **Storing Reports**

    The reports generated by OWASP ZAP are stored in a PVC, allowing retrieval and analysis by tools like Wazuh.

### Viewing Reports and Evolution

In addition to storing the scan reports in a PVC, you can also view the reports and track the evolution of your security scans through the Tekton Dashboard. The Tekton Dashboard provides a user-friendly interface to monitor and manage your Tekton pipelines.

To access the Tekton Dashboard, follow these steps:

1. **Access the Dashboard**

      Open your web browser and navigate to `https://dashboard-tekton.wazuh.adorsys.team/`. You should see the Tekton Dashboard interface.
For local deployment, you can enable Port-Forward.

2. **View Reports**

    In the Tekton Dashboard, you can navigate to the specific pipeline run and view the scan reports generated by OWASP ZAP. This allows you to easily analyze the results and track the security status of your applications.

By using the Tekton Dashboard, you can have a centralized view of your security scans and easily monitor the evolution of your application's security over time.



### Conclusion

This Helm Chart automates the deployment of a Tekton pipeline to run OWASP ZAP security scans, with results stored for easy integration with Wazuh. This ensures continuous and automated security monitoring of applications.