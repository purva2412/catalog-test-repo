---
title: MariaDB Operator installation verification
description: Learn how to verify that the MariaDB Operator has been properly installed in the namespace.
---


### MariaDB Operator status verification 


**Step 1: Verify that the MariaDB Operator has been installed successfully by executing below command.**


```execute
kubectl get csv -n my-mariadb-operator-app
```

You will see a similar output as below.

```
NAME                      DISPLAY            VERSION   REPLACES                  PHASE
mariadb-operator.v0.0.4   Mariadb Operator   0.0.4     mariadb-operator.v0.0.3   Succeeded
```

Note: If the operator is successfully installed, the output should mention the PHASE as "Succeeded".


**Step 2: Check the status of Operator pods by running the command below.**

```execute
kubectl get pods -n my-mariadb-operator-app
```

Output:

```
NAME                               READY   STATUS    RESTARTS   AGE
mariadb-operator-f96ddc69f-d5vgr   1/1     Running   0          100s
```

Note: In above output, STATUS is "Running", which shows that the operator pods are up and active.


### Conclusion

This will verify that MariaDB Operator has been installed successfully.
