# kubernetes_deployments
Kubernetes deployments on ibc EKS cluster

## Deployment

```
kubectl get svc
kubectl create secret generic tyu-syn-pat --from-literal=synapse_auth_token=....
kubectl create -f projectlive_nf_cronjob.yaml
kubectl get cronjob projectlive-nf
```