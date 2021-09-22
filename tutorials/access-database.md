---
title: MariaDB Database Access and table creation inside database.
description: Learn how to access MariaDB Database and create table inside database.
---

### Access MariaDB Database 

To access MariaDB database, you need to connect with server and check for newly created custom database, i.e (test-db).


Follow the steps below to proceed.

**Step 1: Get MariaDB Server Instance podname.**


```execute
kubectl get pods -n my-mariadb-operator-app
```

This should produce the output as below.

```
NAME                                         READY   STATUS    RESTARTS   AGE
mariadb-operator-f96ddc69f-zk56k             1/1     Running   0          2d18h
mariadb-server-5dccfb7b59-rhxkm              1/1     Running   0          2d18h
```


**Step 2: Connect to MariaDB Server pod.**

To do so,

- Copy the command below to the terminal, then add the podname of MariaDB Server Instance.

    
```copycommand
 kubectl exec -it <podname> bash -n my-mariadb-operator-app
 ```


**Step 3: Connect to the database using username db-user and password db-user.**


 ```execute
 mysql -h ##DNS.ip## -P 30685 -u db-user -pdb-user
 ```


**Step 4: Display the database list.**

```execute
show databases;
```


**Step 5: Exit the database.**


```execute
exit
```


**Step 6: Login as `root` user.**


```execute
mysql -h ##DNS.ip## -P 30685 -u root -ppassword
```

**Step 7: Create database ‘testdb’**

```execute
create database testdb;
```


**Step 8: Use the testdb database to create some table.**

```execute
use testdb;
```


**Step 9: Create a table, say Population and define the attributes.**

```execute
create table Population(year numeric,population numeric);
```

**Step 10: Insert some data values into the table.**

```execute
insert into Population values(2017,1380004385 );
```

```execute
insert into Population values(2018,1366417754 );
```

```execute
insert into Population values(2019,1352642280 );
```

```execute
insert into Population values(2020,1338676785 );
```

**Step 11: Retrieve the data from constructed table.**

```execute
select * from Population;
```

**Step 12: Exit from testdb database.**

```execute
exit
```

**Step 13: Exit from pod.**

```execute
exit
```

### Conclusion

We are able to access MariaDB Database and created table "Population" inside a new database "testdb".
