# Data Lineage Orchestration

*Capstone project for Udacity's Cloud DevOps Nanodegree*


---

## Technology Background

<details>
<summary><b>Kubernetes</b></summary>

### Pods

The smallest unit available in Kubernetes is the **Pod**, the basic building block:

> Pods are the smallest deployable units of computing that can be created and managed in Kubernetes.

In other words, Pods are units that encapsulate the elements of the application that must work together. Therefore, when scaling the application - increasing / decreasing the number of Pods - , all the elements inside the Pod will equally scale. Moreover, containers inside the same Pod can easily communicate between them, being isolated from anything else that could be placed outside of their *world*.

However, by definition Pods are not meant to be reliable units. Yes, they have their own IP addresses and we could directly communicate to any of them, but this is discouraged in a production environment as they are ephemeral. Depending on the application load, new Pods may appear or others may crash, so we instead want to communicate to our application in a greater level of abstraction. In order to achieve this, Kubernetes brings us two resources: Workload API Objects and Services.

### Replica Sets

This is an API Object that helps to manage the scaling of Pods.

> Replica Sets ensure that a specified number of pod replicas are running at any given time.

Based on a given **template** and *specs* - such as `specs.replicas = 3`, Replica Sets create Pods to manage. However, a Replica Set may also manage Pods that were not created by it, by specifying a **Selector**, that will be used to match any pod with that given label.

However, this API lacks the ability to perform updates. That's why we need **Deployments**.

### Deployment

Deployment encapsulate both Replica Sets and Pods, providing a declarative method of update their state: `kubectl`. This adds another layer of abstraction to managing Kubernetes:

`User [interface] -> Deployment -> Replica Set -> Pod`

Through the `kubectl` interface, the Deployment will check the current status of the cluster and make it match the desired state specified by the user.

### Stateful Set

Finally, we will introduce another Kubernetes framewoek called **Stateful Set**, used to manage *stateful applications* such as databases. In this [link](http://pauldone.blogspot.com/2017/06/deploying-mongodb-on-kubernetes-gke25.html) I found the best definition of them:

> StatefulSets provides the capabilities of stable unique network hostnames and stable dedicated network storage volume mappings, essential for a database cluster to function properly and for data to exist and outlive the lifetime of inherently ephemeral containers.

</details>

<details>
<summary><b>MongoDB</b></summary>

MongoDB is a Document Store used by Spline to save the information regarding the Data Lineage that will then be shown in the UI.

We will follow this [documentation](http://k8smongodb.net/) to deploy a MongoDB instance on Kubernetes.

</details>

## First approach

In order to get in touch with the different elements involved in the project, we will start by developing the solution in our local workstation.

The contents for this first approach can be found under `local/`, where the `resources` and `scripts` directories are forked from this [repo](https://github.com/pkdone/minikube-mongodb-demo).

Thus, we will set the Kubernetes **Service** type to **NodePort**, as it is the easiest way to exporte the service. Note that in the repo, the usage is set to ClusterIp, which makes the service reachable only within the cluster.

> More info on Kubernetes exposing services [here](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types)

Then, by running `kubectl describe service mongodb-service` we will get the different endpoints for the 3 replicas, and we will connect the Spline application to those in the form of: `<ip-0>:<nodePort>,<ip-1>:<nodePort>,<ip-2>:<nodePort>`

---

## Resources

* [Nirmata series on Kubernetes](https://www.nirmata.com/2018/03/03/kubernetes-for-developers-part-2-replica-sets-and-deployments/)

