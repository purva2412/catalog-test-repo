---
title: Create Prometheus Operator 
description: Learn how to create Prometheus Operator and deploy Prometheus Server and ServiceMonitor
---


### Install Prometheus Operator and Deploy Prometheus Server and ServiceMonitor


**Step 1: Install the Prometheus Operator by executing the following command.**

```execute
kubectl create -f https://operatorhub.io/install/prometheus.yaml
```

The output looks like below.

```
subscription.operators.coreos.com/my-prometheus created
```

Consequently, the operator will be installed in the ‘operators’ namespace and can be used from all the namespaces in the cluster.


**Step 2: Verify that your operator has been successfully installed by executing the below command.**

```execute
kubectl get csv -n operators
```

See the output below.

```
NAME                        DISPLAY               VERSION   REPLACES                    PHASE
prometheusoperator.0.37.0   Prometheus Operator   0.37.0    prometheusoperator.0.32.0   Succeeded
```

From above output, once operator is successfully installed, **PHASE** will be as "Succeeded" 

**Step 3: Check the status of Operator pods.**

```execute
kubectl get pods -n operators
```

This will display an output like:

```
NAME                                   READY   STATUS    RESTARTS   AGE
prometheus-operator-6f7589ff7f-wq9zd   1/1     Running   0          8m35s
```

In above output, STATUS as "Running" shows the pods are up and running.


Note: If you are installing the Operator from operatorhub.io, then by default it installs the operator in ‘operators’ namespace. Below steps assume that it’s deployed in `operators` namespace.

### Create the Prometheus Instance,ServiceAccount and NodePort Service.


**Step 1: Create the below yaml definition of the Custom Resource to create a Prometheus Instance along with a ServiceAccount and a Service of type NodePort.**


```execute
cat <<'EOF' > prometheusInstance.yaml
apiVersion: monitoring.coreos.com/v1
kind: Prometheus
metadata:
  name: server
  labels:
    prometheus: k8s
  namespace: operators
spec:
  replicas: 1
  serviceAccountName: prometheus-k8s
  securityContext: {}
  serviceMonitorSelector:
    matchLabels:
      app: playground  
  alerting:
    alertmanagers:
      - namespace: operators
        name: alertmanager-main
        port: web  
EOF
```


**Step 2: Create the Prometheus Instance using the command below.**



```execute
kubectl create -f prometheusInstance.yaml -n operators
```

The resulting output is given below.

```
prometheus.monitoring.coreos.com/server created
```


**Step 3: Check the status of Operator pods using the following command.**



```execute
kubectl get pods -n operators
```

The output looks like below.

```
NAME                                   READY   STATUS    RESTARTS   AGE
prometheus-operator-6f7589ff7f-wq9zd   1/1     Running   0          14m
prometheus-server-0                    3/3     Running   1          40s
```

You need to wait for the pod STATUS turn “Running” before proceeding further.


**Step 4: Create below yaml definition of the custom resource to create the service NodePort to access prometheus server.**


```execute
cat <<'EOF' > prometheus_service.yaml
apiVersion: v1
kind: Service
metadata:
  name: prometheus
spec:
  type: NodePort
  ports:
  - name: web
    nodePort: 30100
    port: 9090
    protocol: TCP
    targetPort: web
  selector:
    app: prometheus
EOF
```

**Step 5: Use the following command to create the Prometheus Service.**

```execute
kubectl create -f prometheus_service.yaml -n operators
```

This should produce the following output.

```
service/prometheus created
```

**Step 6: Use the link below to access Prometheus Service via Prometheus dashboard.**


http://##DNS.ip##:30100



**Step 7: Create below yaml definition of the Custom Resource to create ServiceMonitor instance.**


```execute
cat <<'EOF' > ServiceMonitor.yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: mariadb-monitor 
  labels:
    app: playground
  namespace: operators
spec:
  namespaceSelector:
    any: true
  selector:
    matchLabels:
      tier: monitor-app 
  endpoints:
   - interval: 20s
     port: monitor    
EOF
```


**Step 8: Create an instance of ServiceMonitor using the command below.**


```execute
kubectl create -f ServiceMonitor.yaml -n operators
```

This results in the output as below.

```
servicemonitor.monitoring.coreos.com/mariadb-monitor created
```


**Step 9: Check the status of Operator’s pods by using the command below.**



```execute
kubectl get pods -n operators
```

You need to wait for the pod STATUS turn “Running” before proceeding further.

### Conclusion

Prometheus Operator created and Prometheus Server deployed. Also we are able to access Prometheus dashboard.
