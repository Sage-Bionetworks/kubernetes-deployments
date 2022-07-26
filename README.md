# kubernetes-deployments
Kubernetes deployments on `ibc` EKS cluster.  To set up the dependencies, we leverage anaconda:

```
conda env create -f environment.yml
```

## Deployment

- Deploying cronjobs on kubernetes: https://v1-21.docs.kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/

    ```
    kubectl get svc
    kubectl create secret generic tyu-syn-pat --from-literal=synapse_auth_token=....
    kubectl apply -f projectlive_nf_cronjob.yaml
    kubectl get cronjob projectlive-nf
    kubectl get jobs --watch
    kubectl get pods --selector=job-name=projectlive-nf-27164915
    kubectl logs projectlive-nf-27164915-6h75l
    kubectl delete cronjob projectlive-nf
    kubectl delete job nlp-participant-dashboard-27171840
    ```


* Delete all pending pods

```
kubectl get pods | grep Pending | awk '{print $1}' | xargs kubectl delete pod
```

## Shiny proxy

https://medium.com/@rohitsrmuniv/using-shinyproxy-with-kubernetes-eba5bd06230



## Installing airflow

1. Instructions here: https://airflow.apache.org/docs/helm-chart/stable/index.html

    ```
    helm repo add apache-airflow https://airflow.apache.org
    helm upgrade --install airflow apache-airflow/airflow --namespace airflow --create-namespace
    ```

1. Update airflow to enable github syncing

    ```
    helm upgrade --install airflow apache-airflow/airflow \
        --set dags.persistence.enabled=true \
        --set dags.gitSync.enabled=true \
        --namespace airflow
    ```

1. Follow parts of the production guide: https://airflow.apache.org/docs/helm-chart/stable/production-guide.html

    ```
    kubectl create secret generic my-webserver-secret --from-literal="webserver-secret-key=$(python3 -c 'import secrets; print(secrets.token_hex(16))')" --namespace airflow
    ```

1. Updating airflow. Create `override-values.yaml`
https://airflow.apache.org/docs/helm-chart/stable/manage-dags-files.html.

    ```
    dags:
      gitSync:
        enabled: true
        repo: https://github.com/apache/airflow/tree/main/airflow
        branch: main
        subPath: "example_dags"
    webserverSecretKeySecretName: my-webserver-secret
    ```

    ```
    helm upgrade --install airflow apache-airflow/airflow -f override-values.yaml --namespace airflow
    ```

1. Make sure to log into airflow and change the password. The password and site URL can be found in "Shared-IBC-DPE-k8".
