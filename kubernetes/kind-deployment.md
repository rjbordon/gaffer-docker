# Deploying using kind

The following instructions will guide you through provisioning and configuring a local Kubernetes cluster, using [kind](https://kind.sigs.k8s.io/) (Kubernetes IN Docker), that our Helm Charts can be deployed on.


## Install CLI Tools

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
* [helm](https://github.com/helm/helm/releases)
* [kind](https://kind.sigs.k8s.io/docs/user/quick-start/)


## Kubernetes Cluster

Simply run the following command to spin up a local Kubernetes cluster, running inside a Docker container:
```
kind create cluster
```


## Container Images

If the versions of the container images you would like to deploy are not available in [Docker Hub](https://hub.docker.com/u/gchq) then you will need to build them yourself and import them into your kind cluster:
bash
```
export HADOOP_VERSION=${HADOOP_VERSION:-3.2.1}
export GAFFER_VERSION=${GAFFER_VERSION:-1.11.0}
export GAFFER_TOOLS_VERSION=${GAFFER_TOOLS_VERSION:-$GAFFER_VERSION}

docker-compose --project-directory ../docker/accumulo/ -f ../docker/accumulo/docker-compose.yaml build
docker-compose --project-directory ../docker/gaffer-road-traffic-loader/ -f ../docker/gaffer-road-traffic-loader/docker-compose.yaml build

kind load docker-image gchq/hdfs:${HADOOP_VERSION}
kind load docker-image gchq/gaffer:${GAFFER_VERSION}
kind load docker-image gchq/gaffer-rest:${GAFFER_VERSION}
kind load docker-image gchq/gaffer-road-traffic-loader:${GAFFER_VERSION}
```


## Ingress

Deploy the Nginx Ingress Controller:
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/mandatory.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/baremetal/service-nodeport.yaml
```


## Deploy Helm Charts

* [HDFS](hdfs/docs/kind-deployment.md)
* [Gaffer](gaffer/docs/kind-deployment.md)
* [Example Gaffer Graph containing Road Traffic Dataset](gaffer-road-traffic/docs/kind-deployment.md)


## Uninstall

```
kind delete cluster
```