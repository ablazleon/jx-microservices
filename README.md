
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

Firts, I've spret three days reading about how to use jenkins X. Then, I wanted to deployed the udagram with microservices, but although I joined the jenkins X slack I wasn't able to configure the nginx to access the frontend. So I deployed a nodejs server.

- https://github.com/ablazleon/myserver.git

- https://github.com/ablazleon/environment-myinfra-production.git
- https://github.com/ablazleon/environment-myinfra-staging.git
-https://github.com/ablazleon/environment-myinfra-dev.git

***Credits***
Udacity Cloud Developer Nanodegree Program

## What I did (rubric), steps

## What I learnt

-----------

# What I did: rubric

(Option 1): CI/CD, Github & Code Quality

- [x] ***The project demonstrates an understanding of CI and Github.***: All project code is stored in a GitHub repository and a link to the repository has been provided for reviewers. The student uses a CI tool to build the application

- https://github.com/ablazleon/myserver.git

- [x] ***The project has a proper documentation.***: The README file includes introduction how to setup and deploy the project. It explains the main building blocks and has comments in the important files.

- [x] ***The project use continuous deployments (CD)***: A CD tool is in place to deploy new version of the app automatically to production. The way is described and easy to follow.

- I learnt Jenkins X

(Option 1): Container

- [x] ***The app is containerized***: There is a Dockerfile in repo and the docker image can be build.

- Yes

- [x] ***The project have public docker images***: Starting the app as a container on a local system

- gcr.io/striking-joy-285411/myserver

- [x] ***The applications runs in a container without errors***: Screenshot of Kubernetes services shows a reverse proxy

- ![ReverseProxy](https://github.com/ablazleon/jx-microservices/blob/master/ReverseProxy.png)


(Option 1): Deployment

- [x] ***The application runs on a cluster in the cloud***: The project can be deployed to a kubernetes cluster.

- ![Frontend](https://github.com/ablazleon/jx-microservices/blob/master/Frontend.png)

- [x] ***The app can be upgraded via rolling-update***: The students can deploy a new version of the application without downtime.

- [x] ***A/B deployment of the application***: Two versions of the same app can run at the same and service traffic

- ![AB](https://github.com/ablazleon/jx-microservices/blob/master/AB.png)

- [x] ***Monitoring***: The application is monitored by Amazon CloudWatch

- As I had not so much creadits for amazon I started the trial period for gcp, so I wasn't able to monitored the app with cloudwatch.

My requirements:

- [x] ***that the different versions of the containerized services are stored to be rollback if neccesary***:

- [x] ***that the deploymnet in continous way on kubernetes is managed through git pull requests***:

- [x] ***that it is provision three environments (development, staging and production)***:


### Steps

#### 1.1 Set up cluster
#### 1.2 Set up the service


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



    ```bash
    jx import --url https://github.com/ablazleon/myserver.git

    ```
    ```bash
    jx promote ---env production
    ```



---------------------


## What I learnt


Asking to some friends they told they are now using Jenkins X beacuse it allows converging every step in git: so I propose to project with Jenkins X as I thought I will be appreciated by mentors. 
