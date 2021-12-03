# Delio DevOps Senior Engineer Test

Delio Engineering have made the decision to start selling "Delio" branded hoodies and coasters. We have developed an API back-end written in Node JS and a simple HTML front-end frontend. 

## The task

It's your job to create the infrastructure for this new site. To complete this task, you'll need to:

* Use "infrastructure as code" to create the infrastrucure in either AWS or Azure. 
* Deploy the API within a Kubernetes cluster.
* Deploy the frontend to static hosting.
* Show a clear commit history and an understand of Git.
* Document any decisions you've made and why.

Optional extras:

* Add unit tests to your code.
* Create a deployment pipeline.

## The rest

Please do not hesitate to get in touch with any questions you have during the process.

## README Begins Here 

For additional info see [wiki page](https://github.com/Kaneofthrones/devops-technical-test/wiki)

Requirements / Tools Used:

* awscli
* aws credentials saved in ~/.aws/credentials | configured
* docker
* terraform
* kubectl
* nodejs
* npm
* Helm

keep in mind certain files have been excluded as standard code practise these are:

     node_modules/*
     terraform/.terraform/*
     terraform/kubeconfig*

### 1. Create branches using a git flow branching strategy 

Master Branch -> Dev branch -> feature/iac -> Dev Branch -> feature/frontend -> Dev Branch -> feature/jenkins -> Dev Branch -> Master Branch

![image](https://user-images.githubusercontent.com/30622959/141043288-7785614f-9d44-43b1-a47f-ffb835756e71.png)


### 2. Create infrastructure on AWS with infastructure orchestration tool terraform

I decided on Terraform as its my preferred tool for IAC and Terraform > Ansible for this use case

    vpc.tf 
to provision a VPC, subnets and availability zones using the AWS VPC Module

    security-groups.tf
provisions security groups for the eks cluster, I made the decision to leave in the efs securty group to show my attempt at deploying jenkins via efs instead of docker, i switched to docker to save time but for production docker may not be suitable

    eks-cluster.tf 
Provisions all the resources required to setup the EKS cluster, it will clone another git repo that has the source code for eks which i left out, EKS was chosen as it works seamlessly on AWS, saves time and is production ready

Versions.tf and outputs.tf define the configuration 

kubernetes.tf defines the certificate to enable EKS to function, would secure using vault or similar for productione

### 3. Package app into docker

Used node:16 as the base for the app.js container as it requires node & nginx was used as the base for the frontend to expose the html file 

make dockerfile for app.js

run app to check docker image works

    docker run -p 49160:3000 -d km-node-app

Curl to verify app is behaving, you should see "hello world"

    curl -i localhost:49160 

### 4. Deploy app on EKS & static host the html file

URLs:

  [frontend URL](http://ad5b019e148e5415d8734efe53fba4ed-2045226712.eu-west-2.elb.amazonaws.com:80)

  [API URL](http://a4ee6e07bae4c4a5b83ed3ddb9c897ec-97789143.eu-west-2.elb.amazonaws.com:80)

  [Demo-App URL](http://a64b4eb06b6f3469fb0a9465bb6ed0ae-326720983.eu-west-2.elb.amazonaws.com:80)

To get the containers onto EKS I used a deployment to create a pod for the container and a service to expose the container 


To deploy on EKS:

    kubectl apply -f kubed-app

the container is on my dockerhub account (kaneofthrones) if it cant be found locally

Then to check the app is running:

    kubectl get pods --watch

Once its running run the following to get the loadbalancer address

    kubectl get svc

copy the external ip of the load balancer into your web browser to be greated with "hello world"


Static host html file:

 static hosting on eks

![delio_1](https://user-images.githubusercontent.com/30622959/140850493-c70b6428-ef48-40d8-b6f6-353e12becb02.png)


### 5. Setup jenkins server 

[Jenkins URL](http://ae561c1398e3d481db642f6653c8617b-1234937774.eu-west-2.elb.amazonaws.com:8080)

run the following to setup a jenkins server on EKS

    kubectl apply -f jenkins.yaml

Then exec onto the pod to retrieve the admin passwd

    kubectl exec --stdin --tty jenkins-pod -- /bin/bash

Then go the to url at port 8080 displayed from:

    kubectl get svc

Then there is jenkins for future pipeline use

![image](https://user-images.githubusercontent.com/30622959/141039725-51fa77cc-f516-490a-a9ab-84c26f0dd147.png)

username: user
password: user

### 6. Have fun ;)
