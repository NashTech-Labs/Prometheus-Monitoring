# Prometheus-Monitoring

Create a K8s cluster.
1. After creating your cluster, you need to get authentication credentials to interact with the cluster

```
gcloud container clusters get-credentials argocd
```
This command configures kubectl to use the cluster you created.

2. Link of application

```
link: https://raw.githubusercontent.com/GoogleCloudPlatform/prometheus-engine/v0.4.3-gke.0/examples/example-app.yaml

```
3. Apply menifest file for deployment.

**Command**
```
kubectl apply -f apps.yaml
```
 Check the deployment using command: 
```
kubectl get deployment
```
4. Deploy helm chart(prometheus-community/kube-prometheus-stack) in a different namespace 

Get Helm Repository Info
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

```
helm repo update
```
Install Helm Chart

```
helm install prometheus prometheus-community/kube-prometheus-stack -n=prometheus-operator --create-namespace
```
Check the status of the namespace

```
kubectl get namespace
```
5. Port forwarding to 5000 to access the metrics 
**Command**

```
 kubectl port-forward deployment/prom-example 5000:1234
```
Open a new tab on your browser and type 

```
http://localhost:5000/metrics
```

6. Create service file for application 

```
kubectl apply -f applicationservice.yaml

```

7. Create cluster role, role binding, and service account 

```
kubectl apply -f rbac.yaml

```
Check if the role is created and bound to the ServiceAccount

```
kubectl describe clusterrolebinding prometheus

```

8. Create service monitor resource/pod monitor resource

```
kubectl apply -f service-monitor.yaml

```

9. Create a file prometheus.yaml 

```
kubectl apply -f prometheus.yaml

```

## Grafana Dashboard
* Forwarding the port to access the grafana dashboard using command

```
kubectl port-forward svc/prometheus-grafana 9000:80 --namespace=prometheus-operator

```
* Go to your browser and type 
 **127.0.0.1:9000**

* For username and password type these commands on your terminal
 ```
 kubectl get secrets -n prometheus-operator
 ```
 ```
 kubectl get secrets prometheus-grafana --namespace=prometheus-operator -o yaml
 ```
 * Now decode the username and password using command
 ```
 echo cHJvbS1vcGVyYXRvcg== | base64 --decode
 ```
 * Open your dashboard of grafana 
  1. Settings
  2. Click on "Data Sources
  3. Click on "Add data source"
  4. Name : prometheus
  5. URL : Set the appropriate Prometheus server URL (ex : http://prometheus-operated.default.svc.cluster.local:9090 )
  6. Adjust other data source settings as desired (for example, choosing the right Access method).
     Click "Save & Test" to save the new data source. 

