---
title: Grafana Operator
description: Learn how to Install Grafana Operator and Deploy Grafana Server Instance, Grafana Data Source and Grafana Dashboard 
---


### Install Grafana Operator and Verify the Status

Follow the below steps to install Grafana Operator.

**Step 1: Run the following command to install Grafana Operator.**


```execute
kubectl create -f https://operatorhub.io/install/grafana-operator.yaml
```


This Operator will be installed in the "my-grafana-operator" namespace and will be usable/accessible from this namespace only.



**Step 2: After installation is complete, run the command below to verify that your Operator is successfully installed.** 

```execute
kubectl get csv -n my-grafana-operator
```

This should display the below output.

```output
NAME                      DISPLAY            VERSION   REPLACES                  PHASE
grafana-operator.v3.2.0   Grafana Operator   3.2.0     grafana-operator.v3.0.2   Succeeded
```

Please wait for the PHASE status to be “Succeeded”, then proceed.


**Step 3: Check the status of pods.**

After the installation is successful, you can check the status of your operator's pods by executing the command as below:

```execute
kubectl get pods -n my-grafana-operator
```

You should see a pod with:
a.	NAME starting with 'grafana-operator' 
b.	READY value as '1/1' 
c.	STATUS as 'Running'

This should display the below output.

```
NAME                                READY   STATUS    RESTARTS   AGE
grafana-operator-7574bbdbc9-skdk8   1/1     Running   0          45s
```


Note: If you are installing the Operator from operatorhub.io, then by default it installs the operator in ‘my-grafana-operator’ namespace. In the below steps, we assume that it is deployed in `my-grafana-operator` namespace only. 


### Deploy Grafana Server Instance


**Step 1: Create the below yaml definition of Custom resourse for Grafana Server Instance.**


```execute
cat <<'EOF' > grafana-server.yaml
apiVersion: integreatly.org/v1alpha1
kind: Grafana
metadata:
  name: mariadb-grafana
spec:
  ingress:
    enabled: true
  config:
    auth:
      disable_signout_menu: false 
    auth.anonymous:
      enabled: false
    log:
      level: warn
      mode: console
    security:
      admin_password: secret
      admin_user: root
  dashboardLabelSelector:
    - matchExpressions:
        - key: app
          operator: In
          values:
            - grafana
EOF
```


**Step 2: Execute the command below to create Grafana instance.**


```execute
kubectl create -f grafana-server.yaml -n my-grafana-operator
```

This will create the following resources.


```
grafana.integreatly.org/example-grafana created
```


**Step 3: Check the status of pod.**

```execute
kubectl get pods -n my-grafana-operator
```

This should produce the output as below.

```
NAME                                  READY   STATUS    RESTARTS   AGE
grafana-deployment-549c685ddc-b6dq7   1/1     Running   0          83s
grafana-operator-7574bbdbc9-skdk8     1/1     Running   0          6m4s
```

Note: The process may take a few minutes. Please do not proceed till you find the pod STATUS as “Running”.


**Step 4: Create the below custom resource yaml definition to create Grafana Service of type `NodePort`.**


```execute
cat <<'EOF' > grafana_service.yaml
apiVersion: v1
kind: Service
metadata:
  name: grafana
spec:
  type: NodePort
  ports:
  - name: web
    nodePort: 30200
    port: 9090
    protocol: TCP
    targetPort: 3000
  selector:
    app: grafana
EOF
```

**Step 5: Run the command below to create Grafana Service.**


```execute
kubectl create -f grafana_service.yaml -n my-grafana-operator
```

You will see output as below.

```
service/grafana created
```

### Create an Instance of Grafana DataSource

**Step 1: Create the below Custom resource yaml file to create Instance of Grafana Datasource.**

```execute
cat <<'EOF' > prometheus-datasources.yaml
apiVersion: integreatly.org/v1alpha1
kind: GrafanaDataSource
metadata:
  name: prometheus-grafanadatasource
spec:
  datasources:
    - access: proxy
      editable: true
      isDefault: true
      jsonData:
        timeInterval: 5s
      name: Prometheus
      type: prometheus
      url: 'http://prometheus-operated.operators:9090'
      version: 1
  name: prometheus-datasources.yaml  
EOF
```

In the above details, we have chosen Prometheus as our DataSource.

**Step 2: Create an instance of Grafana DataSource using the following command.**


```execute
kubectl create -f prometheus-datasources.yaml -n my-grafana-operator
```

This will produce the below output for the instance created.

```
grafanadatasource.integreatly.org/prometheus-grafanadatasource created
```

**Step 3: Check the status of pods.**


```execute
kubectl get pods -n my-grafana-operator
```


This will produce the below output.

```
NAME                                  READY   STATUS    RESTARTS   AGE
grafana-deployment-549c685ddc-b6dq7   1/1     Running   0          83s
grafana-operator-7574bbdbc9-skdk8     1/1     Running   0          6m4s
```

Note: The process may take a few minutes. Please wait until the STATUS turns “Running”, then proceed.


### Conclusion

Following the above procedure, you’re able to:
- Install Grafana Operator
- Deploy Grafana Service
- Create an instance of Grafana Server and Grafana DataSource
Additionally, you should be able to access Grafana dashboard using the Admin user credentials.
