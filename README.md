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

Instructions here: https://airflow.apache.org/docs/helm-chart/stable/index.html

```
helm repo add apache-airflow https://airflow.apache.org
helm upgrade --install airflow apache-airflow/airflow --namespace airflow --create-namespace
```

Update airflow to enable github syncing

```
helm upgrade --install airflow apache-airflow/airflow \
    --set dags.persistence.enabled=true \
    --set dags.gitSync.enabled=true \
    --namespace airflow
```


Create `override-values.yaml`
https://airflow.apache.org/docs/helm-chart/stable/manage-dags-files.html
```
dags:
  gitSync:
    enabled: true
    repo: https://github.com/apache/airflow/tree/main/airflow
    branch: main
    subPath: "example_dags"
```

```
helm upgrade --install airflow apache-airflow/airflow -f override-values.yaml --namespace airflow
```


## Add ELK stack

```
helm repo add elastic https://Helm.elastic.co
helm upgrade --install elastic elastic/elasticsearch --namespace elastic --create-namespace
kubectl expose deployment hello-world --type=LoadBalancer --name=my-service
```
