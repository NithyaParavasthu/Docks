# Monitoring For Local cluster

## SetUp

Let's Create a Namespace to set up all the monitoring stuff.
```
kubectl create ns monitoring
```

Now, Let's install Grafana With Helm charts

```
helm repo add grafana https://grafana.github.io/helm-charts
helm install grafana grafana/grafana -n monitoring 
```

Now. Let's install Prometheus with a helm chart

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm install prometheus prometheus-community/prometheus -n monitoring
```

Now, Let's install Loki with the helm chart
```
helm install loki grafana/loki-stack  -n monitoring
```

Now access the Grafana dashboard using port-forward or load balancer or Ingress.
Ingress file
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: grafana
spec:
  ingressClassName: nginx
  rules:
  - host: grafana.<node IP>.nip.io
    http:
      paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: grafana
              port:
                number: 80
```
Now apply this ingress with the following command
```
kubectl apply -f grafana-ingress.yaml -n monitoring
```

To log in to Grafana, `USER`:`admin` 
We need the password. To get the password use the following command.
```
kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

## Configure

Now In the Grafana dashboard, Let's Configure Both Loki and Prometheus.

Go to `Datasorce` -> `Add Datasorce`. -> Selete `prometheus`
  -  URL: `http://prometheus-server:80`
  -  save and Test
  
Go to Datasorce -> Add Datasorce.-> Selete Loki
  -  URL: `http://loki:3100`
  -  save and Test

Now Let's create Dashboards using Prometheus and Loki.

### Prometheus:

In Dashboard create a folder of your choice or use the default folder.
In that folder import a new dashboard.
`Import via grafana.com`: 15175 And click load
 - folder: <name of the folder that you created>
 - Prometheus: Select the `Prometheus` data source that you configured before in the drop-down.

Quers for the dashboard:
- Cluster memory usage: 
```
   sum (container_memory_working_set_bytes{id="/",kubernetes_io_hostname=~"^$Node$"}) / sum (machine_memory_bytes{kubernetes_io_hostname=~"^$Node$"}) * 100
```
- Cluster CPU usage 
```
 sum (rate (container_cpu_usage_seconds_total{id="/",kubernetes_io_hostname=~"^$Node$"}[15m])) / sum (machine_cpu_cores{kubernetes_io_hostname=~"^$Node$"}) * 100 
```
- Cluster filesystem usage
```
sum (container_fs_usage_bytes{id="/",kubernetes_io_hostname=~"^$Node$"}) / sum (container_fs_limit_bytes{id="/",kubernetes_io_hostname=~"^$Node$"}) * 100
```
```
sum (container_fs_usage_bytes{device=~"^/dev/[sv]d[a-z][1-9]$",id="/",kubernetes_io_hostname=~"^$Node$"}) / sum (container_fs_limit_bytes{device=~"^/dev/[sv]d[a-z][1-9]$",id="/",kubernetes_io_hostname=~"^$Node$"}) * 100
```

### Loki:

In that folder import a new dashboard.
`Import via grafana.com`: 15141 And click load
 - folder: <name of the folder that you created>
 - Loki: Select the `Loki` data source that you configured before in the drop-down.
For custom logs
 In the dashboard, select New Dashboard, select Panel, and run the  following query.

Quers for the dashboard:
 - Error-logs
 visualization: logs
```
{namespace="<your ns>"} |= `error`
```
 
- Cluster-creation-logs
visualisation: logs
```
{namespace="<your ns>"} |= `attempt number`
```
- custom logs
visualisation: logs
```
{app="<your app>"} |= ``
```

- Mysql-logs
visualisation: logs
```
{app="mysql"} |= ``
```


## Alerts

Firstly we need to configure a `Contact point` in Alerting.
 -  Go to `Contact point`. select `+ New contact point` 
 -  Name: grafana-slack
 -  Contact point type: slack
 -  Recipient: <channel name>
 -  Token: <git token>
 -  save contact point
 -  Go to `Notification policies`. select `+ New specific policy`
 -  Contact point: grafana-slack
 -  click `+ Add matchers`. Label: env, Operator: =, Value: prod
 -  save policy

### Cpu-usage-alert
Go to `Kubernetes monitoring` dashboard.
 -  click on `Cluster-cpu-usage`and select Edit in the drop-down.
 -  In visualization select `timeseries`
 -  go to the alert column. click on "create alert rule for the panel"
 -  Leave the query A as it is. 
 -  In Query B, change the value in `is above` option from 3 to 75
 -  Click `Run Queries` and check.
 -  In `Alert evaluation behavior`, In `Evaluate every` options change 1m to 10s and 5m to 20s
 -  In `Add details for your alert`, In `Group` give alert-group. 
 -  In `Notifications` for `Key`- env, For `value` prod
 -  Click `save and exit` button on the top corner.
 
### Cluster filesystem usage
Go to `Kubernetes monitoring` dashboard.
 -  click on `Cluster filesystem usage`and select Edit in the drop down.
 -  In visualization select `timeseries`
 -  go to the alert column. click on "create alert rule for the panel"
 -  Leave the query A as it is. 
 -  In Query B, change the value in `is above` option from 3 to 73
 -  Click `Run Queries` and check.
 -  In `Alert evaluation behavior`, In `Evaluate every` options change 1m to 10s and 5m to 20s
 -  In `Add details for your alert`, In `Group` give alert-group. 
 -  In `Notifications` for `Key`- env, For `value` prod
 -  Click `save and exit` button on the top corner.
 
### Cluster memory usage
Go to `Kubernetes monitoring` dashboard.
 - Click on `Cluster memory usage` and select Edit in the drop-down.
 - In visualization select `timeseries`
 - go to the alert column. click on "create alert rule for the panel"
 - Leave the query A as it is. 
 - In Query B, change the value in `is above` option from 3 to 75
 - Click `Run Queries` and check.
 - In `Alert evaluation behavior`, In `Evaluate every` options change 1m to 10s and 5m to 20s
 - In `Add details for your alert`, In `Group` give alert-group. 
 - In `Notifications` for `Key`- env, For `value` prod
 - Click `save and exit` button on the top corner.
 
### Custom log alert
Go to the `Loki custom logs` dashboard.
 - Click on `Custum-logs` and select Edit in the drop-down.
 - In visualization select `timeseries`
 - Go to the alert column. click on "create alert rule for the panel"
 - In Query A, in `Log browser` change the code to ```count_over_time({app="app-name"}[10m] |= "attempt number" )```. 
 - In Query B, change the value in `is above` option from 3 to 50
 - Click `Run Queries` and check.
 - In `Alert evaluation behavior`, In `Evaluate every` options change 1m to 10s and 5m to 1m
 - In `Add details for your alert`, In `Rule name` give "cluster-creation-exceed" In `Group` give alert-group. 
 - In `Notifications` for `Key`- env, For `value` prod
 - Click `save and exit` button on the top corner.
 
### Mysql-error
Go to the `Loki custom logs` dashboard.
 - Click on `Mysql-logs` and select Edit in the drop-down.
 - In visualization select `timeseries`
 - go to the alert column. click on "create alert rule for the panel"
 - In Query A, in `Log browser` change the code to ```count_over_time({app="mysql"}[10m]|= "connection")```. 
 - In Query B, change the value in `is above` option from 3 to 15
 - Click `Run Queries` and check.
 - In `Alert evaluation behavior`, In `Evaluate every` options change 1m to 10s and 5m to 1m
 - In `Add details for your alert`, In `Rule name` give "Mysql-error" In `Group` give alert-group. 
 - In `Notifications` for `Key`- env, For `value` prod
 - Click `save and exit` button on the top corner.
 
