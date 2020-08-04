
# jx-microservices

In the capstone project of the cloud developer nd, it is proposed a functionality for an app: it is focused on making an app production ready. Three features are desired to be improved: 

1. that the different versions of the containerized services are stored to be rollback if neccesary, 
2. that the deploymnet in continous way on kubernetes is managed through git pull requests 
3. and that it is provision three environments (development, staging and production). 

To so first, was thought on this approach:

1. using a container version manager as skaffold, and a deployment manager as helm
2. deploy github webhooks,
3. Create three different folders with different kubernetes environment files.

But it was discovered a tool called jenkins x allow these three functionalities over the app.

![cicd2](https://github.com/ablazleon/udagram_microservices/blob/master/screenshots/cicd2.png)

In the next section it is discussed that it has not only been covered the capstone project rubric requriments but these three.

***Credits***
Udacity Cloud Developer Nanodegree Program

## What I did (rubric), steps

## What I learnt

-----------

# What I did: rubric

(Option 1): CI/CD, Github & Code Quality

- [x] ***The project demonstrates an understanding of CI and Github.***: All project code is stored in a GitHub repository and a link to the repository has been provided for reviewers. The student uses a CI tool to build the application

- [x] ***The project has a proper documentation.***: The README file includes introduction how to setup and deploy the project. It explains the main building blocks and has comments in the important files.

- [x] ***The project use continuous deployments (CD)***: A CD tool is in place to deploy new version of the app automatically to production. The way is described and easy to follow.

(Option 1): Container

- [x] ***The app is containerized***: There is a Dockerfile in repo and the docker image can be build.

- [x] ***The project have public docker images***: Starting the app as a container on a local system

- [x] ***The applications runs in a container without errors***: Screenshot of Kubernetes services shows a reverse proxy

(Option 1): Deployment

- [x] ***The application runs on a cluster in the cloud***: The project can be deployed to a kubernetes cluster.

- [x] ***The app can be upgraded via rolling-update***: The students can deploy a new version of the application without downtime.

THese logs are icnluded in the readme.md of the screenshot folder


### Steps

1 Using jenkins X => a problem in an environment in gke: services seems unaccesible and also the frontend, as if there weren't enough memory=> so it is accessed with k8s alone. 
In gke is tried to create a cluster iwht much more memory but a problem is found realated having to udpate the github token in the Auth file

![401updateAuth.yml](https://github.com/ablazleon/udagram_microservices/blob/master/screenshots/401updateAuth.yml.png)

Previuosly it is tried with eks, but the wsl gives errors with jx boot and it is not possible to create a cluster from an ubuntu server. So it is created the cluster in wsl and use "aws eks --region us-west-2 update-kubeconfig --name c". But, it is obtained a "error: You must be logged in to the server (Unauthorized)" 



![GKE-logs](https://github.com/ablazleon/udagram_microservices/blob/master/screenshots/GKE-logs.png)

![GKE-logs-env](https://github.com/ablazleon/udagram_microservices/blob/master/screenshots/GKE-logs-env.png)

![Frontend-gke-problem](https://github.com/ablazleon/udagram_microservices/blob/master/screenshots/Frontend-gke-problem.png)

![ProblemWSL](https://github.com/ablazleon/udagram_microservices/blob/master/screenshots/ProblemWSL.png)

![ubuntuFailedJxCreateCluster](https://github.com/ablazleon/udagram_microservices/blob/master/screenshots/ubuntuFailedJxCreateCluster.png)

#### 1.1 Set up cluster
#### 1.2 Set up the service
#### 1.3 Develop the service

-------------

#### 1 Set up cluster

From https://jenkins-x.io/docs/install-setup/install-binarls, jx is installed.

Then, I created an eks cluster, but as the costs grows so quickly I decided to choose instead gke, to the tests and deploy the final in eks.

    ```bash
    jx create cluster gke --cluster-name mycluster --skip-installation --skip-login=true
    ```

    ```bash
    jx create cluster eks --cluster-name mycluster --skip-installation 
    ```

Then it is copied a jx-requirments.yml and modified it accordingly.

    ```bash
    jx boot 
    ```

To check it works, it is created a quickstart. Then it is checked that the reverse proxy is a service.

    ```bash
    kubectl get svc
    ```

#### 2 Set up the service

First, both services are deployed independently.

Next, the amazon rds is set up, and so the environment 

Then, both the v0 microservices are imported and took to production.

    ```bash
    jx import --url https://github.com/ablazleon/udagram-api-users.git
    jx import --url https://github.com/ablazleon/udagram-api-feed.git
    jx import --url https://github.com/ablazleon/udagram-frontend.git
    jx get app
    ```
It is config the env values in the dev environmanet in a values.yml file

In this photo it is shown how both microservices work.


    ```bash
    jx promote ---env production
    ```

#### 3 Develop the service

From there it is refactored the microservices in dev enviroment, importing the microservice feed and removing the users functionality from the the other microservice.

Here it is icnlude that these works.

THen it is included a .travis to run the steps out of the cluster

---------------------

#### 2. With k8s alone

To do so it is created some kubernetes arquitecture with a reverse proxy and services and a config map

    ```bash
    eksctl create cluster \
     --name c2 \
     --version 1.16 \
     --without-nodegroup
    ```
    
    https://docs.aws.amazon.com/eks/latest/userguide/launch-workers.html
    
    ```bash
    eksctl create nodegroup \
    --cluster default \
    --version auto \
    --name standard-nodes \
    --node-type t3.medium \
    --node-ami auto \
    --nodes 3 \
    --nodes-min 1 \
    --nodes-max 4
    ```
    
Then it is applied the deplyoment for the forntend, then the service and check witht the port forward if that works.

    ```bash
    kubectl apply -f frontend-dpl.yml
    kubectl apply -f frontend-svc.yml
    ```
    
    ```bash
    kubectl get po
    kubectl port-forward pod/PODNAME 8100:80 
    http://localhost:8100
    ```
    
    I'm going to set the congig map
    
      ```bash
    kubectl apply -f configMap.yml   
     
    kubectl apply -f api-users-dpl.yml
    kubectl apply -f api-users-svc.yml 
    
    kubectl apply -f api-feed-dpl.yml
    kubectl apply -f api-feed-svc.yml 
    ```  
    
    Finally, a reverse-proxy is set up.
    
    https://docs.aws.amazon.com/eks/latest/userguide/alb-ingress.html#:~:text=EKSUser%20Guide-,ALB%20Ingress%20Controller%20on%20Amazon%20EKS,ingress.class:%20alb%20annotation.
    
    ```bash
    eksctl utils associate-iam-oidc-provider \
    --region us-west-2 \
    --cluster c1 \
    --approve
       
    curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.8/docs/examples/iam-policy.json
    
    aws iam create-policy \
    --policy-name ALBIngressControllerIAMPolicy \
    --policy-document file://iam-policy.json
    
    kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.8/docs/examples/rbac-role.yaml
    
    eksctl create iamserviceaccount \
    --region us-west-2 \
    --name alb-ingress-controller \
    --namespace kube-system \
    --cluster c1 \
    --attach-policy-arn arn:aws:iam::764217278004:policy/ALBIngressControllerIAMPolicy \
    --override-existing-serviceaccounts \
    --approve
    
    kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.8/docs/examples/alb-ingress-controller.yaml
    
    kubectl edit deployment.apps/alb-ingress-controller -n kube-system
    
    spec:
      containers:
      - args:
        - --ingress-class=alb
        - --cluster-name=c1
    
    kubectl get pods -n kube-system  
    ```  
    
It's checked that this ing controller is created.

    ```bash
    kubectl apply -f ingCtl.yml
    ```
 So the commands to create the deployment are the following:
 
     ```bash
    kubectl apply -f frontend-dpl.yml
    kubectl apply -f frontend-svc.yml
    
    kubectl apply -f configMap.yml   
     
    kubectl apply -f api-users-dpl.yml
    kubectl apply -f api-users-svc.yml 
    
    kubectl apply -f api-feed-dpl.yml
    kubectl apply -f api-feed-svc.yml 
        
    kubectl apply -f ingCtl.yml
    
    kubectl autoscale deployment frontend --cpu-percent=70 --max=4
    ```
As it appears a sequelize error it is ccheked with kube command if the environemnt values are exported. 

    ```bash
    kubectl exec PODNAME
    ```

It is cheked this is beacuse the databse was not configure to accept all ips

THe problem is why in local it is connected to the port 8080, but there not.

## What I learnt

The motivation of this project is to understand how to turn a "monolith" software into services. To do so it is taken the rest api and approach it as two separate projects running in two different docker instances, and fianlly as two containers in a kubernetes cluster.

First, I started approaching this CI/CD pipeline as follows.

![cicd1](https://github.com/ablazleon/udagram_microservices/blob/master/screenshots/cicd1.png)

Then, asking to some friends they told they are now using Jenkins X beacuse it allows converging every step in git: so I propose to project with Jenkins X as I thought I will be appreciated by mentors. But then I found, there were some problems related envrionemtn varialbes in jenkins X, so I first decided trying wiht kubernetes deployment alone.
