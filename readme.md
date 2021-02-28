# Kubernetes (K8's)

- High availabilty, no downtime
- Scalability/ high performance
- Disaster Recovery, backup and restore


## Architecture

- Starts with one master node
- Connected to it are worker nodes
- Each worker node has kubelet process running on it
- Kubelet is a kubernetes process that makes it possible for the cluster to talk to each other and actually perform tasks.
- Each worker node has docker containers of different apps deployed on it.
- Worker nodes is where the actual work is happening!

Whats happening on Master node?
- Several K8's processes are running
- One of them is the API Server which is also a container.
- Entrypoint to the Kubernetes cluster.
    - UI API and CLI all talk to this process

- Controller Manager
    - Maintains overview of what's happening cluster 
    - Detects repairs, crashes, etc

- Scheduler
    - Responsible for scheduling containers on different nodes, based on workload and server resources for each node. 

- etcd 
    - Holds at anytime, the state of K8's cluster

Final component of Kubernetes, which enables those nodes to talk to each other, is the **Virtual Network**.
- Turns all the nodes inside of the cluster, into a unified machine. 
- Has the sum of all the resources of the individual nodes.

**This requires the connection to a master node, so backups of the master node are encouraged in production to ensure smooth operation**

## K8's Components
There are tons of components, but most of the time you will only be using a handful of them. 

### Node and Pod
Assume you have a worker node (server, physical machine)
Pod resides within a node. 
- Smallest unit in Kubernetes
- A pod is abstraction over container
    - Creates a running environment, aka layer over the container
    - You will only interact with the Kubernetes layers.
    - Pod is meant to run 1 application per pod/container. 

How They Communicate in the Kubernetes World
- Each pod gets it's own **IP address**. 
- However, they are efemeral. They can die really easily. 
    - Say app ran out of resources, and pod dies. Then new is put in place. Now you have a new IP address :( 
- Because of this, we use Service. 

### Service and Ingres
Setting of a permanent IP address to each pod. 
The lifecycles of the pod and the service are not connected, so when a pod dies the IP address does not change!

App should be accesible by brower. So you would create an external service. Ex. `http://my-app-service-ip:port`

But you want to protect your DB from public requests, so you would use an internal service!
~~`http://db-service-ip:port`~~

So you're external service may look like
```
http://124.89.101.2:8080
```
But you want it to look like this for production
```
https://myapp.com
```

In comes the **Ingres**

So first the request hits the Ingres, then the Service. 


## Config Map and Secrets
In recap, pods communicate with each other using services. Many config urls such as database urls are bult into the application, so any small changes would require a tedious amount of work to make the adjustments to production. 

ConfigMap is an external configuration of your application.
Example
```
DB_URL = mongo_db_service
```

This could be changed easily in the ConfigMap without any tedious changes. 

You may also have user and credentials, so for this we can use the component **Secret**

### Secret
- used to store secret data
- Base64 encoded
- **Not enabled by default**

## Volumes
If a database pod get's restarted, the data would be gone!

Volumes come into play. 

- Attaches physical storage on your hard drive to your pod
    - Can be local machine
    - Remote/cloud storage

Now when the db pod service gets restarted, the data persists. 
The storage is always an external hard drive plugged into the K*'s cluster, K8's doesn't manage data persistence. 

## Deployment Stateful Set



## KubeCTL
```
minikube start --vm-driver=hyperkit
```
```
kubectl get nodes
kubectl get pod
```

To root into the cluster
```
kubectl exec -it *pod_name* -- bin/bash
```

### Apply command
```
kubectl apply -f config-file.yaml
```

This allows you to apply the configuration for a deployment without adding a series of tags, you can set the options in this .yaml file. 

By running the above command, you can deploy a container with a preset configuration. 

Any changes that need to be made to .yaml file can be made and the apply command run again. It will update the config!

You can create and update components by editing this file. 

## Commands Summary
### Create command
```
kubectl dreate deployment [name]
```

### Status of different K8's components
```
kubectl get nodes | pod | services | replicaset | deployment
```

### Debugging Pods
```
kubectl logs [pod_name]
```

Interactive Terminal for Cluster
```
kubectl exec -it [pod-name] -- bin/bash
```

Get info About Pod
```
kubectl describe pod [pod-name]
```

### Use Config File for CRUD

Apply a config gilr
```
kubectl apply -f [filename]
```

Delete with config file
```
kubectl delete -f [filename]
```