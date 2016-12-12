
# Drone Pipeline 

This is the effort for POC the pipeline to build sprinboot applications, build a docker image using the binary 
file build and Dockerfile provided. Push the docker image to artifactory. Finally deploy the pod in turn which has the 
application container of the application.

Following is the diagram representing the architectural design of the pipeline :
![N|Solid](https://git.target.com/storage/user/5615/files/2c61f5f6-bffb-11e6-85c9-42410d2e8156)


This project focuses more on creating a continous integration and continous deployment pipeline for the application using drone.

### Techonology stack required
* Source code management: GitHub Enterprise
* Continuous integration: Drone (version 0.5)
* Application packaging: Docker
* Binary repository: Artifactory hosting Docker Registry
* Deployment platform: Kubernetes
* Secret management: Drone secrets [for pipeline specific secrets] and Kubernetes secrets [for application specific secrets]

## Steps to Steup

### Requirements
* Github account with permissions to modify webhooks
* Drone server with a personal access token
* Namespace in Kubernetes namespace, associated authorization token and IP address of the host name or kubernetes cluster 

### Drone server
* We are using dorne 5 server
* Login using your github credentials

### Creating webhooks on drone server
* Go to the account section to see all the github repositories available for you on drone server
* Click and turn the required application button switch to on.
* The webhook gives access for drone to check on any events happening to the repository and kick off appropriate pipeline step.

### Installing and seting up droneCLI 

* ```brew tap drone/drone && brew install drone```

* In the account section of drone navigate to show token and copy the token

```export DRONE_SERVER=<drone-server-url>```

```export DRONE_TOKEN=<your-drone-token>```

This would set up and connect to drone server on your local machine

### YAML File
* Drone File

```* Replace the text with appropriate registry in drone/docker plugin```

```* Replace the text with appropriate repository in drone/docker plugin```

To read more about how to use drone/docker plugin [read here](https://github.com/drone-plugins/drone-docker)

* Kubernetes File

```* Replace the text with appropriate repository in containers.name.image```

```* Replace the text with appropriate secrets in spec.volumes.name.secret.secretName```


To read more about writing deployment files [read here](http://kubernetes.io/docs/user-guide/deployments/)

### Secrets management

We would be using drone server to keep the pipeline specific secrets i.e.
* Kube config file
* Docker username
* Docker password
* Docker email
* Kubernetes cluster name
* Kubernetes cluster context
* Kubernetes namespace name

These all variables can be injected considering you are using Drone5 directly to the namespace in the server that you are working on.
* For docker plugin, secrets can be addded as following :

```drone secret add --image plugins/docker <git-org>/<git-repo> DOCKER_USERNAME <username>```

```drone secret add --image plugins/docker <git-org>/<git-repo> DOCKER_PASSWORD <password>```

```drone sign <git-org>/<git-repo>```

Signing the secret will make sure that secrets are added to the server.

* For Kubernetes plugin, secrets can be addded as following :

* ```drone secret add --image brianberzins/kubectl <git-org>/<git-repo> KUBE_CONFIG @```

```drone sign <git-org>/<git-repo>```

* ```drone secret add --image brianberzins/kubectl <git-org>/<git-repo> CLUSTER-NAME <cluster name>```

```drone sign <git-org>/<git-repo>```

* ```drone secret add --image brianberzins/kubectl <git-org>/<git-repo> CLUSTER-CONTEXT <cluster context> ```

```drone sign <git-org>/<git-repo>```

Read in detail about secrets on [on drone docs](http://readme.drone.io/usage/secrets/)

### Execution commands :
```drone exec --plugin docker```

This is command to run pipeline with drone docker plugin

### Documents for reading 
* [DRONE DOCS](http://readme.drone.io/setup/overview/)
* [KUBERNETES DOCS](http://kubernetes.io/docs/)
* [Kubernetes deployment ](http://kubernetes.io/docs/user-guide/deployments/)
* [Drone docker plugin](https://github.com/drone-plugins/drone-docker)

### images used for kubernetes
* chinmaym/kubernetes : provides a container to run kubectl commands
* plugins/docker : for publishing docker images to repository


For POC purposes all the secrets can be replaced with the original values
