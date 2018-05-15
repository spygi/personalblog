---
layout: post
title:  "🚧 Docker & Kubernetes intro 🚧🏗👷‍"
date:   2018-05-15
categories: devops
---
## Containers
### Quick start
- $dev-machine: Write Dockerfile in which you expose a port
- $build-machine: `docker build -t IMAGE_TAG .` # build image based on local .Dockerfile, for IMAGE_TAG use the id/repo:tag convention
- $build-machine: `docker push IMAGEID_OR_TAG DOCKERID/REPO:tag`
- $run-machine: `docker run -p CONTAINER_PORT:IMAGE_PORT -d IMAGE_TAG/ID` # detached mode

### Glossary
- Basic: Docker client/daemon builds an Image, pushes to a Registry, runs it in a Container.

### Prerequisites
- Docker client/daemon for the build and the container machines.
- A Docker registry e.g. hub.docker.com or Google Container registry Create a Docker id (on hub.docker.com or cloud.docker.com for more features)
- A Dockerfile

### Useful commands
- `docker system df -v` # for disk usage from Docker
- Docker image `docker image --help`
  - `docker image ls` (aka `docker images`)
  - `docker image rm IMAGE_ID` (aka `docker rmi IMAGE_ID`)
- Docker container `docker container --help`
  - `docker container ls --all` # aka `docker ps`
  - `docker prune` # to delete stopped containers
- `docker tag EXISTING_TAG NEW_TAG`

### Registries
### Docker hub
1 private repo

### Google Container registry
1. Tag it: `docker tag IMAGE_ID eu.gcr.io/nes-app-193321/NAME`
1. Push it: `gcloud docker -- push eu.gcr.io/nes-app-193321/NAME`

More: list images with `gcloud container images list --repository=eu.gcr.io/nes-app-193321` or you can view it on the GUI under Container registry

TODO: adjust Access Control on the Cloud Storage bucket?

## Orchestration: Docker Swarm
### Glossary
- A **swarm** is a group of (physical or virtual) machines that are running Docker and joined into a cluster. The machines (`docker-machine`) are referred to as **nodes** once they join a swarm (`docker node`) and can be managers or workers. Nodes can be only part of 1 swarm. If the only manager of a swarm leaves (with --force) "in situations where the swarm will no longer be used after the manager leaves, such as in a single-node swarm"
- A **stack** is deployed on a swarm by the swarm manager. It is defined from a  `docker-compose.yml` and consists of services, networks and volumes.
- **Service**: “containers in production” A service only runs one image but it can use mupliple containers (replicas). Replicas are called **tasks**.

### Quick start
- Create `docker-compose.yml`
- `docker swarm init` # to enable swarm mode and make your current machine a swarm manager
- Add nodes to the swarm: how to do this via the docker-compose file?? Is this where Puppet etc come into play?
- `docker stack deploy -c PATH_TO_DOCKER_COMPOSE STACK_NAME` # deploy a stack to the swarm
- `docker service ls` # to check services or `docker stack services STACK_NAME`
- `docker service ps SERVICE_NAME` # to check tasks
- `docker inspect TASK_OR_CONTAINER_ID`

### Sidetopic: Docker compose vs Swarm
From [Nick](https://nickjanetakis.com/blog/docker-tip-23-docker-compose-vs-docker-stack):
> It just so happens both Docker Compose and the Docker stack command support the same docker-compose.yml file with slightly different features.  

And why is that a good thing?  
> ... define a single docker-compose.yml ... which is a nice quality of life improvement if you happen to use Docker Compose in development and Docker Swarm in production.  

Personally I find it confusing the fact that a docker-**compose** file can be used in 2 different (?) operations with different parameters and it [seems I am not the only one](https://forums.docker.com/t/when-to-use-docker-compose-and-when-to-use-docker-swarm/29107). [In this nice tutorial](https://github.com/docker/labs/blob/master/beginner/chapters/votingapp.md) they named it docker-stack.yml

According to the [official docs](https://docs.docker.com/compose/overview/#common-use-cases) Docker compose is recommended for single host deployments (development, testing etc). However you can also use Docker compose in [production](https://docs.docker.com/compose/production/) by
> defining an additional Compose file, say production.yml, which specifies production-appropriate configuration. This configuration file only needs to include the changes you’d like to make from the original Compose file. The additional Compose file can be applied over the original docker-compose.yml to create a new configuration.

From [deprecated?](https://docs.docker.com/compose/swarm/)
> Docker Compose and Docker Swarm aim to have full integration, meaning you can point a Compose app at a Swarm cluster and have it all just work as if you were using a single Docker host.

[Docker compose Version 3](https://docs.docker.com/compose/compose-file/)

## Swarm in depth
- [Scheduling strategy](https://docs.docker.com/swarm/scheduler/strategy/)


## Orchestration: Kubernetes on Google Kubernetes Engine
### High level overview
Build image, push to registry, connect to cluster, delete service, delete deployment, deploy, apply service

Prerequisite: GCloud sdk + install kubernetes: `gcloud components install kubectl`
1. Create a cluster from the UI
1. Connect to it: `gcloud container clusters get-credentials CLUSTER_NAME`
1. Deploy: `kubectl run DEPLOYMENT_NAME --image eu.gcr.io/nes-app-193321/NAME --port 8080` or create or apply
1. Expose it with `kubectl expose deployment DEPLOYMENT_NAME --type "LoadBalancer"`
1. Get external IP:PORT `kubectl get service DEPLOYMENT_NAME` #

Inspect deployment with `kubectl get deployments [DEPLOYMENT_NAME] -o yaml`.
You can see the pods where it got deployed `kubectl get pods` and then `kubectl describe pod POD_NAME` or in the Kubernetes Engine GUI.
Scale with: `kubectl scale DEPLOYMENT_NAME --replicas NEW_NUMBER`
Delete service + deployment


## Putting it all together: an example pipeline with microservices
- Build part (CI): On push Jenkins tells the Docker client to build and tag appropriately (time?) the image. Then pushes the image to a repo in the registry.
- Deploy part (CD): Pull/run the latest (by time?) image from registry on certain port. Run more tests? Point other services via the load balancer to the new version.
- Maintenance: If everything is ok, stop previous containers. Prune them. Periodically clean up old images. If deployment goes wrong, switch load balancer to previous container.
- Security: who can push?
