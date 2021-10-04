---
title: MariaDB Instance creation
description: Learn how to create Instance of MariaDB Operator
---

### Create an Instance of MariaDB


 
**Step 1: Create the below yaml definition of the custom resource for MariaDB server instance, and setup a database ‘test-db’ with the provided user credentials.**

```execute
cat <<'EOF' > MariaDBserver.yaml
apiVersion: mariadb.persistentsys/v1alpha1
kind: MariaDB
metadata:
  name: mariadb
spec:
  # Keep this parameter value unchanged.
  size: 1
  
  # Root user password
  rootpwd: password

  # New Database name
  database: test-db
  # Database additional user details (base64 encoded)
  username: db-user 
  password: db-user 

  # Image name with version
  image: "mariadb/server:10.3"

  # Database storage Path
  dataStoragePath: "/mnt/data" 

  # Database storage Size (Ex. 1Gi, 100Mi)
  dataStorageSize: "1Gi"

  # Port number exposed for Database service
  port: 30685
EOF
```

**Step 2: Execute the following command to create MariaDB Server instance.**


```execute
kubectl create -f MariaDBserver.yaml -n my-mariadb-operator-app 
```


The generated Custom Resource will create a database called test-db, along with user credentials. The Server image name is mentioned in "image" parameter.

Note: MariaDB Database uses external location on host to store all Database files. This location is by default set to /mnt/data from MariaDB Custom Resource. This location should be created before applying the Custom Resource.
 



### Verify MariaDB Deployment


**Step 1: Verify the status of pods.** 


```execute
kubectl get pods -n my-mariadb-operator-app 
```

You should see an output like below.

```
NAME                              READY   STATUS    RESTARTS   AGE
mariadb-operator-78c95468-m824g   1/1     Running   0          118s
mariadb-server-778b9b7cb5-nt6n5   1/1     Running   0          109s
```

It may take a few minutes for the pod STATUS to be “Running”. Do not continue till it appears on the screen. 


**Step 2: Verify services using the command below.**



```execute
kubectl get svc -n my-mariadb-operator-app 
```

You should see an output as shown below.


```
NAME                       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
mariadb-operator-metrics   ClusterIP   10.98.179.105    <none>        8383/TCP,8686/TCP   101s
mariadb-service            NodePort    10.105.103.156   <none>        80:30685/TCP        73s
```

The service `mariadb-service` is of type NodePort that exposes mariadb on port 3306. See the description using the following command.

```execute
kubectl describe service/mariadb-service -n my-mariadb-operator-app
```

This should produce the following output.

```
Name:                     mariadb-service
Namespace:                mariadb
Labels:                   MariaDB_cr=mariadb
                          app=MariaDB
                          tier=mariadb
Annotations:              <none>
Selector:                 MariaDB_cr=mariadb,app=MariaDB,tier=mariadb
Type:                     NodePort
IP:                       10.100.40.161
Port:                     <unset>  80/TCP
TargetPort:               3306/TCP
NodePort:                 <unset>  30685/TCP
Endpoints:                172.17.0.5:3306
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

### Conclusion

You’re done with creating an instance of MariaDB operator.
