# **Running SPA on minikube**

This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 15.1.4.

## Table of contents

- [**Running SPA on minikube**](#running-spa-on-minikube)
  - [Table of contents](#table-of-contents)
  - [Prerequisites and Installation](#prerequisites-and-installation)
    - [Installation](#installation)
  - [Quick start](#quick-start)
    - [Running SPA on Docker Container](#running-spa-on-docker-container)
    - [Running SPA on minikube](#running-spa-on-minikube-1)
    - [Cleanup](#cleanup)

## Prerequisites and Installation

Library | Version | Notes
:-------|:--------:|-------
[Node](https://nodejs.org/) | 16.17.x | Recommended NodeJS version
[NPM](https://nodejs.org/) | 8.15.x | Recommended NPM version
[Angular](https://angularjs.org/) | 15.1.x | JavaScript-based open-source front-end SPA framework
[Angular CLI](https://cli.angular.io/)| ~15.1.x | Angular tool to create, manage and run build tasks
[Minikube](https://minikube.sigs.k8s.io/docs/start/)| v1.29.0 | Lightweight Kubernetes implementation that creates a VM on your local machine and deploys a simple cluster containing only one node

### Installation

1. Install Node v16.17.x and NPM v8.15.x

1. Install Angular cli

```bash
// uninstall older version of Angular CLI
     npm uninstall -g @angular/cli
// To get the latest production version of Angular CLI 12.2.x
     npm install @angular/cli@12.2 -g
// Verify the angular cli and workspace versions
     ng --version
```

1. Install minikube, refer: https://minikube.sigs.k8s.io/docs/start/

## Quick start

1. Clone repository and enter it

  ```bash
  git clone git@github.com:kumaran-is/demo-spa.git
  cd demo-spa
  ```

2. Install NPM

  ```bash
  npm install
  ```

3. Start the spa and navigate to <http://localhost:4200/>.

```bash
npm run start

```

This command starts Angular UI application on port `4200`

### Running SPA on Docker Container

1. We run SPA on a nginx server on port `8000`  inside the container. Lets create a [custom nginx configuration](./nginx-custom.conf) which will be used by nginx docker image in step 2


2. Write a multi-stage [docker file](./Dockerfile). Dockerfile consists of two stages:

First stage: Create a node environment using node 16.17 base image and builds the angular application with production configuration.

Second stage: Copy the dist folder from the previous stage to Nginx container and copy [custom nginx configuration](./nginx-custom.conf)inside the nginx


3. Build docker image

```
docker build -t kumaranisk/spa-demo:v1 .
```

4. Verify the created image

```
docker images -a
```

5.  Run docker image. This will start the SPA on docker container at ngnix port `8000`

```
docker run -it -p 8000:8000 kumaranisk/spa-demo:v1 .
```

6.  Launch the application by navigating to <http://localhost:8000>

7.  Stop and remove the container

```
docker ps -a
docker stop <container_id>
docker rm <container_id>
```

8. To remove container upon exit, pass argument "--rm" to docker run command

```
docker run -it -p 8000:8000 --rm kumaranisk/spa-demo:v1 .
```

### Running SPA on minikube

1. Login to docker to publish docker image `kumaranisk/spa-demo:v1` to public `https://hub.docker.com/` registry using your docker hub `user id` and `password`

```
docker login --username=kumaranisk
# enter your docker hub password when prompt
```

2. Docker push to docker registry

```
docker push kumaranisk/spa-demo:v1
```

3. start the minikube

```
minikube start
```

4. start the minikube dashboard

```
minikube dashboard
```

5. Create a [kubernetes Deployment Pod resource](./spa-deployment.yaml). A Deployment named `demo-deployment` is created, indicated by the .metadata.name field. This name will become the basis for the ReplicaSets and Pods which are created later. You can find the sample template for Deployment file in the [official website](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

6. Create the deployment by running the following command:
   
```
 kubectl apply -f spa-deployment.yaml
```

7. Check if the Deployment was created:
   
```
 kubectl get deployments
```

8. To see the Deployment rollout status:
   
```
 kubectl rollout status deployment/demo-deployment
```

9. To see the pods created, by running the following command:
   
```
 kubectl get pods
```

10. To get details of your Deployment, by running the following command:
   
```
 kubectl describe deployments
```

11.  Create a [kubernetes service resource](./spa-service.yaml). In Kubernetes, a Service is an abstraction which expose an application running on a set of Pods as a network service. The set of Pods targeted by a Service is usually determined by a selector label `demo`.  For example, in deployment resource we defineda set of Pods where each listens on TCP port `8000` and contains a label `demo`. You can find the sample template for service file in the [official website](https://kubernetes.io/docs/concepts/services-networking/service/)

12. Create the service by running the following command:
   
```
 kubectl apply -f  spa-service.yaml
```

13. Check if the service was created:
   
```
 kubectl get svc -o wide
```


14. To get details of your services, by running the following command:
   
```
 kubectl describe services
```

15. To get node ip:
   
```
 minikube ip

 or

kubectl get no -o wide
```

16.  To launch the application:
   
```
 minikube service demo-service
 
 or

 kubernete minikube service demo-service --url 
```

### Cleanup

1. Remove the deployment resource

```
kubectl delete deployment demo-deployment
```

1. Remove the service resource

```
kubectl delete service demo-service
```

1. Stop the minikube

```
minikube stop
```
