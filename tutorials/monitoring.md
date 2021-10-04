---
title: Enable Monitoring for MariaDB
description: Learn how to enable monitoring service for MariaDB.
---


### Enable Monitoring service on MariaDB 


**Step 1: Execute below command to get the services of MariaDB in "my-mariadb-operator-app" namespace.**
  
  ```execute
  kubectl get svc -n my-mariadb-operator-app
  ```

 This should produce the output like below.
 
 ```
  NAME                       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
 mariadb-operator-metrics   ClusterIP   10.111.158.91    <none>        8383/TCP,8686/TCP   3d4h
 mariadb-service            NodePort    10.106.178.202   <none>        80:30685/TCP        3d4h
 ```
 
From the above output, we get the port for mariadb-service as 30685.


**Step 2: Create the below yaml definition of the Custom Resource to enable monitoring using Prometheus exporter pod and service.**


```execute
cat <<'EOF'> MariaDBmonitoring.yaml
apiVersion: mariadb.persistentsys/v1alpha1
kind: Monitor
metadata:
  name: mariadb-monitor
spec:
  # Add fields here
  size: 1
  # Database source to connect with for colleting metrics
  # Format: "<db-user>:<db-password>@(<dbhost>:<dbport>)/<dbname>">
  # Make approprite changes 
  dataSourceName: "root:password@(##DNS.ip##:30685)/test-db"
  # Image name with version
  # Refer https://registry.hub.docker.com/r/prom/mysqld-exporter for more details
  image: "prom/mysqld-exporter"
EOF
```

Note: Make sure you enter correct port number and database host for the metrics to work.



**Step 3: Execute the command below to create an instance of monitoring service.**

```execute
kubectl create -f MariaDBmonitoring.yaml -n my-mariadb-operator-app
```

The output should look like below.


```
monitor.mariadb.persistentsys/mariadb-monitor created
```

This will start the Prometheus exporter pod and service.




## Create Monitoring Resources

To enable monitoring services, you need to:
a.	 Install Prometheus Operator and deploy Prometheus Instance and ServiceMonitor
b.	 Install Grafana Operator and deploy Grafana Server Instance and Grafana Data-Source
If the above prerequisites are already fulfilled, you can proceed with Step 2 and Step 4.
Else, follow the below steps to perform the above tasks.



### Step 1: Install Prometheus operator and Deploy Prometheus Instance and ServiceMonitor
 
 Refer tutorial “Create Prometheus Operator”.
 

### Step 2: Access the Prometheus Dashboard.

Access the Prometheus Dashboard using the below URL:

http://##DNS.ip##:30100

- On the Prometheus UI, go to Status -> Targets to view the endpoints.


 ![](_images/targets.PNG)

Note: It may take some time for the Target's endpoints to become visible.

- From the dropdown, you can select the query and click on "Execute" to see MariaDB Metrics. Refer the below snapshot.


![](_images/queryexecution.PNG)




### Step 3: Install Grafana Operator and Deploy Grafana Server and DataSource


Refer the tutorial: "7: Create Grafana Operator".



### Step 4: Access Grafana dashboard


- Execute the command below to retrieve all the services in “my-grafana-operator” namespace.


```execute
kubectl get svc -n my-grafana-operator
```


Output :

```
NAME                       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
grafana                    NodePort    10.101.18.57    <none>        9090:30200/TCP   5m31s
grafana-operator-metrics   ClusterIP   10.99.122.95    <none>        8080/TCP         6m45s
grafana-service            ClusterIP   10.109.139.18   <none>        3000/TCP         5m53s
```

Port value of "grafana" Service of TYPE NodePort is : 30200


We can access the Grafana dashboard on the nodePort : 30200 using below url:

<a href="http://##DNS.ip##:30200" target="_blank">http://##DNS.ip##:30200</a> 


You will see the Grafana page loading as below :


![](_images/load.png)


Now click on the `Sign In` button as below :

![](_images/signin.png)

Log-in to Grafana Dashboard with the following credentials in the page below:


```
user: root
password: secret
```
![](_images/login.png)


Now you will be able to see the Dashboard like below:


![](_images/dashboard.png)


### Configure Grafana Dashboard to visualize MariaDB monitoring metrics

Learn how to Configure Grafana Dashboard to visualize MariaDB monitoring metrics from this tutorial "8: Grafana Dashboard".





