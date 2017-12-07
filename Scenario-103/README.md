Table of Contents
=================
<a href="https://www.dennyzhang.com"><img align="right" width="201" height="268" src="https://raw.githubusercontent.com/USDevOps/mywechat-slack-group/master/images/denny_201706.png"></a>

   * [Requirements](#requirements)
   * [Background Knowledge](#background-knowledge)
   * [Procedure](#procedure)
      * [Deployment](#deployment)
      * [Verify Deployment](#verify-deployment)
   * [Highlights](#highlights)
   * [More resources](#more-resources)

# Requirements
<a href="https://www.dennyzhang.com"><img align="right" width="185" height="37" src="https://raw.githubusercontent.com/USDevOps/mywechat-slack-group/master/images/dns_small.png"></a>
```
1. In yaml, define a k8s namespace. Thus we can segreate different k8s envs
2. Make sure we can create no more than 1 pods for DB server. (Hint: ResourceQuota)
3. Before we can start mysql server, make sure volume is ready. (Hint: livenessProbe)
4. When we initialize mysql, pass mysql root password in a secured way. (Hint: secrets)
5. Use StatefulSet to create one mysql db instance in yaml
6. When db first started, create a dummy table and dummy records
7. When db process has failed, make sure a new one will be started and no data loss
```

See [kubernetes.yaml](kubernetes.yaml)

# Background Knowledge

- What is StatefulSet? Why we need it?
- ResourceQuota can apply to current namespace.

# Procedure
## Deployment

To setup mysql service, here we use mysql image in [docker hub](https://hub.docker.com/_/mysql/).

- Start vm
```
# start a VM to host our deployment
minikube start
```

Here we have created a namespace of k8s-1node-test
```
# https://kubernetes.io/docs/tasks/administer-cluster/namespaces-walkthrough/
kubectl create namespace k8s-1node-test
```

```
# Create k8s volume, deployment and service
kubectl create -f ./kubernetes.yaml --namespace k8s-1node-test
```
See [kubernetes.yaml](kubernetes.yaml)

## Verify Deployment
- Check k8s web UI Dashboard
```
minikube dashboard
```

- List k8s resources
```
# list deployments
kubectl get deployment

# list service
kubectl get services

# list pods
kubectl get pods
```
- Run functional test

Open mysql client to access the mysql server

Use phpmyadmin, create a database and a table 
TODO

phpmyadmin url:
```minikube service my-dbclient-service --url```

dbserver_url:
```"$(minikube service my-dbserver-service --url)"```

TODO: remove http, add screenshot

-  Mysql server resilient test
- If one instance is down, another will be started automatcially.

TODO

From Web UI, delete the mysql server Pod.

We should see ReplicationController will start a new one.

Confirm the database and table still persist, which were created in last step.

- Delete k8s resources
```
kubectl delete -f ./kubernetes.yaml --namespace k8s-1node-test
```

- Delete namespace
```
kubectl delete namespace k8s-1node-test
```

- Destroy env
```
minikube delete
```

# Highlights
- Q: How does the volume process work?

- Q: How PersistentVolumeClaim know use which PersistentVolume?

# More resources
- minikube: https://kubernetes.io/docs/getting-started-guides/minikube/
- k8s local solutions: https://kubernetes.io/docs/setup/pick-right-solution/#local-machine-solutions
- k8s volume: https://blog.couchbase.com/stateful-containers-kubernetes-amazon-ebs/

<a href="https://www.dennyzhang.com"><img align="right" width="185" height="37" src="https://raw.githubusercontent.com/USDevOps/mywechat-slack-group/master/images/dns_small.png"></a>