```
---
title: Contacts Application using MariaDB
description: Learn how to use MariaDB to send and receive messages
---

### Introduction

Contacts application comprises of a backend and frontend which are deployed independently as a microservice. The backend connects to a MariaDB instance for storing the list of contacts. The frontend is a React application for managing the list of contacts.
The example also uses Skaffold which handles the workflow for building, pushing and deploying your application, allowing you to focus on what matters most: writing code.

### Code Structure

![codestructure](_images/mariadb-app-structure.png)

The code stucture follows a simple modular and MVC pattern. There are 3 folders that are of our interest:

	- backend : This folder contains the sources for the Node.js backend application.
    - frontend : This folder contains the sources for the React frontend application.
    - k8s : This folder contains all the deployment and service yaml files for the application. This defines the deployment and exposure of our application.


### Try the example

**Step 1: Perform the following steps described in the tutorials.**

*  Install the MariaDB operator and create an instance of it.

*  Check that MariaDB operator is installed and running

Once you complete the above steps, proceed with following steps to execute the sample application.


**Step 2: Install the sample application.**

- Get the sample code.

```execute
cd /home/student/projects && git clone https://github.com/operator-playground-io/MariaDB-sample.git
```

- Navigate to the example.

```execute
cd MariaDB-sample
```

- Set the MariaDB instance.

```execute
export ip_addr=$(ifconfig eth1 | grep inet | awk '{print $2}' | cut -f2 -d:)
```
```execute
sed -i "s/host-address/$ip_addr/" ./frontend/.env
```

- Setup skaffold default repository to the local one.
```execute
skaffold config set default-repo localhost:5000
```

- Install and start the sample application. 

```execute
skaffold run
```
Alternatively, you can use this command to install the sample, watch for code changes and re-deploy the application automatically. 

On exiting the command, Skaffold will automatically stop and delete the sample application.

```execute
skaffold dev
```

### Access the application

Follow the below URL: 

http://##DNS.ip##:30501


### Deploy the changes to Kubernetes in Dev Mode

Step 1: Go to Developer Dashboard tab. It will provide you with the IDE along with the integrated terminal. 

Step 2: Click on the bottom status bar and select `TERMINAL`.

k8s folder contains all the manifest files and defines the deployment strategy for the application. 

Step 3: Execute the command below. 

```execute
kubectl apply -f k8s/
```

In this example , we use `Skaffold` which simplifies local development. You can deploy the application is DEV mode which keeps watching for the files changes and on any change, triggers the entire deployment process automatically without the user having to run and manage it manually.

```execute
skaffold dev
```

On exiting the command, Skaffold will automatically destroy all the resources it created with the above command.


Note: You can also use the `skaffold run` to deploy the changes onto Kubernetes in normal mode. In this mode, the resources created by the user remain unless he deletes them.

### Clean up the Application Resources

You can delete all the application resources created by executing the following command:

```execute
skaffold delete
```


