
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

- [x] ***A/B deployment of the application***: Two versions of the same app can run at the same and service traffic

- [x] ***Monitoring***: The application is monitored by Amazon CloudWatch

My requirements:

- [x] ***that the different versions of the containerized services are stored to be rollback if neccesary***:

- [x] ***that the deploymnet in continous way on kubernetes is managed through git pull requests***:

- [x] ***that it is provision three environments (development, staging and production)***:


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

Then it is modified jx-requirments.yml accordingly to set repos to public, insed the jx config boot folder

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

After, it is set in the env variables the rds config 

Then, both the v0 microservices are imported and took to production.

    ```bash
    jx import --url https://github.com/ablazleon/udagram-frontend.git
    jx import --url https://github.com/ablazleon/udagram-api-users.git
    jx import --url https://github.com/ablazleon/udagram-api-feed.git

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


## What I learnt

The motivation of this project is to understand how to turn a "monolith" software into services. To do so it is taken the rest api and approach it as two separate projects running in two different docker instances, and fianlly as two containers in a kubernetes cluster.

First, I started approaching this CI/CD pipeline as follows.

![cicd1](https://github.com/ablazleon/udagram_microservices/blob/master/screenshots/cicd1.png)

Then, asking to some friends they told they are now using Jenkins X beacuse it allows converging every step in git: so I propose to project with Jenkins X as I thought I will be appreciated by mentors. But then I found, there were some problems related envrionemtn varialbes in jenkins X, so I first decided trying wiht kubernetes deployment alone.
