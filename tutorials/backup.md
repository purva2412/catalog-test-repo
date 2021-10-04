---
title: MariaDB Backup 
description: Learn how to schedule backup for MariaDB
---

### Create MariaDB Backup Instance 

**Step 1: Create Custom resource yaml file to Setup a MariaDB Backup Instance.**

```execute
cat <<'EOF' > MariaDBBackup.yaml
apiVersion: mariadb.persistentsys/v1alpha1
kind: Backup
metadata:
  name: mariadb-backup
spec:
  # Backup Path
  backupPath: "/mnt/backup"

  # Backup Size (Ex. 1Gi, 100Mi)
  backupSize: "1Gi" 

  # Schedule period for the CronJob.
  # This spec allow you setup the backup frequency
  # Default: "0 0 * * *" # daily at 00:00
  schedule: "0 0 * * *"
EOF
```

**Step 2: Execute the command below to create an instance of MariaDB Backup.**


```execute
kubectl create -f MariaDBBackup.yaml -n my-mariadb-operator-app
```


This should display the following output:

```
backup.mariadb.persistentsys/mariadb-backup created
```

Note: The Custom Resource will schedule the backup of MariaDB at a defined interval. The Database backup files will be stored at the location: '/mnt/backup'. Ensure that you create this location before applying the Custom Resource.

**Step 3: Execute the command below to check the configuration status of cronjob.**


```execute
kubectl get cronjob -n my-mariadb-operator-app
```

You should see an output like below.

```
NAME             SCHEDULE    SUSPEND   ACTIVE   LAST SCHEDULE   AGE
mariadb-backup   0 0 * * *   False     0        <none>          17m
```

Once the operation is successful, a new job will take database backup at scheduled interval, and will create a backup file at defined location, by default: /mnt/backup.

### Conclusion

Backup for MariaDB has been scheduled and it will take database backup at scheduled interval.
