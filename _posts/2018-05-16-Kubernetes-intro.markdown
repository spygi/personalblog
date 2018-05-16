---
layout: post
title:  "🚧 Kubernetes intro 🚧"
date:   2018-05-16
categories: devops
---
## Orchestration: Kubernetes (Minikube or Google Kubernetes Engine)
### Quick start - Minikube
- Prerequisites: install [minikube](https://github.com/kubernetes/minikube), kubectl + a vm-driver (Virtualbox)
- `minikube start`
This creates a minikube context to run all further commands. It also creates 2 namespaces + default for the cluster but more importantly 2 services (`kubectl cluster-info`): a kubernetes service and a DNS service
- Create a deployment: `kubectl run DEPLOYMENT_NAME --image=IMAGE_TAG --port=INTERNAL_PORT # eg. same as the one exposed by the Dockerfile`
- Create a service for it: `kubectl expose deployment DEPLOYMENT_NAME --type=NodePort|LoadBalancer`
- Get the "public address" (still on your machine) of service: `minikube service SERVICE_NAME --url` or check the cluster external IP with `kubectl config` and get the external port with `kubectl get service SERVICE_NAME`
- `minikube dashboard`
- `kubectl delete service SERVICE_NAME`
- `kubectl delete deployment DEPLOYMENT_NAME`
- `minikube stop`

### Glossary
![Basic logical parts of a cluster]({{ "/assets/kubernetes/logical_parts.jpg" | absolute_url }})
- [Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/) are the smallest deployable units of computing that can be created and managed in Kubernetes. It's a group of one or more containers (such as Docker containers), with shared storage/network, and a specification for how to run the containers.
Why not use a container as the smallest unit? a) because Kubernetes works with different types of containers (Docker, rkt etc), b) to manage a container, Kubernetes needs additional information, such as a restart policy etc..Instead of overloading the existing “thing” with additional properties, Kubernetes architects have decided to use a new entity [from](https://www.mirantis.com/blog/multi-container-pods-and-container-communication-in-kubernetes/). [Full API object reference](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.10/#pod-v1-core):
> It is recommended that users create Pods only through a Controller, and not directly. See Controllers: Deployment, Job, or StatefulSet.

- [Service](https://kubernetes.io/docs/concepts/services-networking/service/) is an abstraction which defines a logical set of Pods and a policy by which to access them <- usually a label selector. Interesting use-case:
> Services without selectors: you are migrating your workload to Kubernetes and some of your backends run outside of Kubernetes.

- [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) is a _controller_ that provides declarative updates for Pods and ReplicaSets. Prefer deployments over `rolling-update` (which is imperative) (the strategy of deployment is specified in `.spec.strategy.type` anyway) `kubectl run creates a reployment`

- FYI: [ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) ensures that a specified number of pod replicas are running at any given time. It is the next-generation Replication _controller_.
> Therefore, we recommend using Deployments instead of directly using ReplicaSets, unless you require custom update orchestration or don’t require updates at all.  

- FYI: [Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) are virtual clusters on the same physical cluster. They provide a scope for names, divide cluster resources or access control. A serviceaccount is automatically created for a namespace.
> For clusters with a few to tens of users, you should not need to create or think about namespaces at all.

- FYI: Node is the same more or less as in Swarm. Kubelet is a process on the node for communicating with the master, see [here](https://kubernetes.io/docs/tutorials/kubernetes-basics/explore/explore-intro/)
![Node]({{ "/assets/kubernetes/node.svg" | absolute_url }})

### Useful commands
kubectl get|edit|describe|delete RESOURCE_TYPE RESOURCE_NAME

- explain, describe, logs
- Debugging: `kubectl exec -it POD_NAME -- /bin/bash # to get a shell in a pod`
- [Updating](https://kubernetes.io/docs/concepts/cluster-administration/manage-deployment/#in-place-updates-of-resources) In-place updates resources: `apply` a configuration to a resource, `edit` (similar to opening an editor and then apply) or `patch` inline.  ~~If your deployed application is managed by Replication Controllers: `rolling-update` though this is imperative and generally they recommend (the declarative) Deployments.~~ Disruptive updates: `replace` (with or without --force?) To scale: `kubectl scale DEPLOYMENT_NAME --replicas NEW_NUMBER`
- Meta: `kubectl config current-cluster`, `cluster-info`, `top`
- [Cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/): especially the resources, -o for output, -v for verbose logs
- [Kubectl reference commands](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)

### In depth
- What is the connection of Service with deployments? From `kubectl expose --help`:
> Looks up a deployment, service, replica set, replication controller or pod by name and uses the selector for that
resource as the selector for a new service on the specified port.

Note that the replica sets have the selector but not the pods. Label vs selector?

- [Networking with services](https://kubernetes.io/docs/concepts/services-networking/connect-applications-service/): check the networking model of Kubernetes vs Docker, the 2 options for service discovery (hardcoded or DNS) and securing services. If all fails: [debugging DNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/).

- [Configuration best practices](https://kubernetes.io/docs/concepts/configuration/overview/#naked-pods-vs-replicasets-deployments-and-jobs) and [assigning pods to nodes](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#node-affinity-beta-feature)

### Google Kubernetes Engine
- Install locally GCloud sdk + kubectly: `gcloud components install kubectl`
- Create a cluster from the UI
- Connect to it: `gcloud container clusters get-credentials CLUSTER_NAME`
- Create a deployment + service like usual
- Get external IP of the cluster: `gcloud compute instances list`
- Get external port of service: `kubectl get service SERVICE_NAME`

## Putting it all together: an example pipeline with microservices
- Build part (CI): On push Jenkins tells the Docker client to build and tag appropriately (time?) the image. Then pushes the image to a repo in the registry.
- Deploy part (CD): Pull/run the latest (by time?) image from registry on certain port. Run more tests? Point other services via the load balancer to the new version.
- Maintenance: If everything is ok, stop previous containers. Prune them. Periodically clean up old images. If deployment goes wrong, switch load balancer to previous container.
- Advanced: security, service discovery (DNS), affinity, registry

## Further research
- Labels vs selectors (vs label selector??): adding a pod directly on a replicaset - how does it reconcile? Adding a FE pod with a BE selector.. The service will direct traffic to it right?
