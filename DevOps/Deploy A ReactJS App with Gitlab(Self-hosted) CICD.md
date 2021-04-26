# Deploy A ReactJS App with Gitlab(Self-hosted) CI/CD

This article is mainly referred from [GitLab CI/CD example with a dockerized ReactJS App 🚀](https://dev.to/christianmontero/gitlab-ci-cd-example-with-a-dockerized-reactjs-app-1cda), I just made some personalized changes for my notes as learning purpose.

## 1. Setup a Gitlab service on a server

Your can follow [Gitlab Installation on Ubuntu 20.04 LTS](https://github.com/PFTian/TechNotes/blob/master/Gitlab/Gitlab%20Installation.md) or [offical document](https://about.gitlab.com/install/#ubuntu) to install Gitlab on a new Ubuntu server.

Basically we just need to do

* Install and configure the necessary dependencies

    ```bash
    sudo apt-get update
    sudo apt-get install -y curl openssh-server ca-certificates tzdata perl
    ```

*  Install Postfix
    ```bash
    sudo apt-get install -y postfix
    ```
    Choose `Internet Site` and use your server's external DNS as `mail name`.

*  Add the GitLab package repository and install the package

    ```bash
    curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh | sudo bash   
    ```
    Use your domain name as parameter to install the gitlab
    ```bash
    sudo EXTERNAL_URL="https://gitlab.yourdomain.com" apt-get install gitlab-ee
    ```

## 2. Install a Gitlab Runner (Better on A Seperate Machine)

Gitlab runner is an application that runs Giltab jobs in pipelines

> You should install GitLab Runner on a machine that’s separate from the one that hosts the GitLab instance for security and performance reasons.

You can follow [Gitlab Runner Installation on Ubuntu 20.04 LTS](https://github.com/PFTian/TechNotes/blob/master/Gitlab/Gitlab%20Runner%20Installation%20on%20Ubuntu%2020.04%20LTS.md) or [offical document](https://docs.gitlab.com/runner/install/linux-manually.html) to install gitlab runner on a seperate Ubuntu server.

Simply you just need to do

*  Download the latest `deb` file
    ```bash
    curl -LJO https://gitlab-runner-downloads.s3.amazonaws.com/latest/deb/gitlab-runner_amd64.deb
    ```

*  Install the package
    ```bash
    sudo dpkg -i gitlab-runner_amd64.deb
    ```
*  Register your runner with command
    ```bash
    sudo gitlab-runner register
    ```
*  Enter your Gitlab instance URL
    ```
    Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com )
    https://gitlab.yourdomain.com
    ```
*  Enter the token you obtained to register the Runner:
    ```
    Please enter the gitlab-ci token for this runner
    Register Token
    ```

## 3. Create a New Project on Gitlab

Login to your Gitlab and create a new project (I will name it bright-future)

*  Click `New project`
    ![image](https://user-images.githubusercontent.com/10986601/116002743-bd9f3980-a62d-11eb-9894-92f2c62dd49c.png)

*  Choose `Create blank project`
    ![image](https://user-images.githubusercontent.com/10986601/116002892-4f0eab80-a62e-11eb-8182-b874f968df9d.png)

*  Fill the project information and click `Create project`
    ![image](https://user-images.githubusercontent.com/10986601/116002834-166ed200-a62e-11eb-8fcc-e9bac09df966.png)


## 3. Create a ReactJS Application and Push to Gitlab repository

*  On your local machine, initialise a project called `bright-future` as well.

    ```bash
    npx create-react-app bright-future
    ```
*  After creating the project successfully, we run the project
    ```bash
    cd bright-future
    npm start
    ```
*  If everything is install correctly, you will see a default webpage shows as below on http://localhost:3000/
   ![image](https://user-images.githubusercontent.com/10986601/116003343-17a0fe80-a630-11eb-8d46-049737f2f283.png)

*  Check test passes
    Run the `test` command
    ```
    npm test
    ```
    In the console prompt, press `a` to run all tests, if everything goes well, we will see

    ![image](https://user-images.githubusercontent.com/10986601/116003979-41a7f000-a633-11eb-95bd-e1d7a69797e2.png)


*  Push the project to your Gitlab repository
    ```bash
    git init
    git remote add origin https://gitlab.yourdomain.cn/root/bright-future.git
    git add .
    git commit -m "Initial commit"
    git push -u origin master
    ```
*  Refresh the Gitlab repository, you will see your `Bright Future` has been listed there.
    ![image](https://user-images.githubusercontent.com/10986601/116003800-2dafbe80-a632-11eb-9136-411848b6ce11.png)

## 4. Dockerize our application on Nginx
We will use [Docker multi-stage builds](https://docs.docker.com/develop/develop-images/multistage-build/) to create a Nginx image with compiled app in the end. You can obviously choose to use Nodejs image to run your app in a docker container, but using Ngnix image to deploy app is a thinner and more efficient way.

* Add `.dockerignore` for `node_modules` folder at the root of your `bright-future` project, since this file is usually huge and docker can help you download all the dependencies.

* Create a `Dockerfile` and add a build stage

    Since ReactJS application is based on Node.js, we still Node.js to build and compile the application. 

    At the build stage, add the below code to the `Dockerfile`

    ```dockerfile
    # Build stage: based on Node.js, build and compile the application
    FROM node:14.16.1-alpine3.13 as build

    # Docker work directory, docker will create an `/app` folder and all the next steps will happen in this directory
    WORKDIR /app

    # Copy your application to the container
    COPY . /app/

    # Prepare the container for building React
    RUN npm install
    # Build your application
    RUN npm run build

    # Nginx stage: deploy build/compiled app to Nginx.
    #Prepare nginx
    FROM nginx:1.19.10-alpine

    # Copy application build files from build stage above to nginx
    COPY --from=build /app/build /usr/share/nginx/html

    # Delete the default Nginx configration file and copy your Nginx configuration file to the container
    RUN rm /etc/nginx/conf.d/default.conf
    COPY nginx/nginx.conf /etc/nginx/conf.d

    #Fire up the Nginx
    EXPOSE 80

    #If you add a custom CMD in the Dockerfile, be sure to include -g daemon off; in the CMD in order for nginx to stay in the foreground, so that Docker can track the process properly (otherwise your container will stop immediately after starting)!
    CMD ["nginx", "-g", "daemon off;"]
    ```

* Create Nginx configuration file

    You can see from the above step that we use our own nginx configration file. So that your root of the porject, create a folder called `nginx` add the below nginx configuration code and named it `nginx.conf`

    ```
    server {
        listen 80;
        
        location / {
            root /usr/share/nginx/html;
            index index.html index.htm;
            try_files $uri $uri/ /index.html;
        }

        error_page 500 502 503 504 /50x.html;

        location /50x.html {
            root /usr/share/nginx/html;
        }
    }
    ```
* Now, we try to create our docker image locally to see if all above steps work.

    Open your project, if you create the files correctly, your project file structure should look like as below

    ![image](https://user-images.githubusercontent.com/10986601/116006377-83d62f00-a63d-11eb-9f0b-9e7ceb151e55.png)

    Now open the terminal and make sure you are at the root of your project folder and run

    ```
    docker build -t bright-future:1.0 .  
    ```

    Docker will output lots of log messages, and if we create our image successfully, we can check the image by using

    ```
    docker images
    ```

    if you see the below line, we can verify that we have created a nginx dcoker with out application
    ![image](https://user-images.githubusercontent.com/10986601/116007288-a0746600-a641-11eb-81b4-f1ed9466f324.png)


    Fire up a docker container based on the image we created
    ```
    docker run -d --rm --name bright-future -p 5000:80 -t bright-future:1.0
    ```

    Now you can go to http://localhost:5000 and see our dockerlised app running.

    ![image](https://user-images.githubusercontent.com/10986601/116007341-e92c1f00-a641-11eb-8d5d-a0817a32fbf6.png)

## 5. Gitlab Pipelines

Gitlab uses `gitlab-ci.yml` to run the pipelines. So we need to create a `gitlab-ci.yml` file at the root of the project folder.

Once we added the `gitlab-ci.yml` file and push it to the Gitlab repository, Gitlab will detect this file and Gitlab-runner will go through the file and run all jobs that we specific there.

In this article, we will create 4 stages as example to demonstrate how Gitlab pipeline works

* build
* test
* docker-build
* deploy

## 5.1 Pipelines - build

Let's first create a build stage for pipelines. See the below example:

```
stages:
  - build

build:
  stage: build
  image: node:14.16.1-alpine3.13
  before_script:
    - echo "Start building app..."
  script:
    - npm install
    - npm run build
    - echo "Build successfully"
```

Commit and push your code to your self-hosted Gitlab. You will see there is a `build` stage on your project piplines. 

<img width="634" alt="image" src="https://user-images.githubusercontent.com/10986601/116027198-d12abe80-a686-11eb-95e9-c2a60197aeba.png">

If everything runs well, it should show `passed`.

<img width="628" alt="image" src="https://user-images.githubusercontent.com/10986601/116027270-00d9c680-a687-11eb-88af-6e26207e453d.png">

## 5.2 Pipelines - test

After `build`, we should add a test stage to check if all the test pass.

```
stages:
  - build
  - test

build:
  stage: build
  image: node:14.16.1-alpine3.13
  before_script:
    - echo "Start building app..."
  script:
    - npm install
    - npm run build
    - echo "Build successfully"

test:
  stage: test
  image: node:14.16.1-alpine3.13
  before_script:
    - echo "Start testing app..."
  script:
    - npm install
    - npm test
    - echo "Test successfully!"


```
Commit and push your code, the test stage will also be added on the piplines

<img width="661" alt="image" src="https://user-images.githubusercontent.com/10986601/116028981-e3a6f700-a68a-11eb-93e6-7986a59f1285.png">

If everything runs well, both build and test should show `passed`.

<img width="579" alt="image" src="https://user-images.githubusercontent.com/10986601/116029154-3da7bc80-a68b-11eb-980d-ef349068c09b.png">

Till now, we have 2 stages (build and test) to show how piplines work. We build the app at first and then run the test in sequence. You might be curious why we run `npm install` two times in two stages? That is because each job runs in a new empty docker container instance and we don't have any data from previous jobs. In order to share data among stages, we need to use artifacts or cache, what is the difference? Here is the answer from Gitlab offical document.

> **Cache vs artifacts**
> 
> If you use cache and artifacts to store the same path in your jobs, the cache might be overwritten because caches are restored before artifacts.
> Don’t use caching for passing artifacts between stages, as it is designed to store runtime dependencies needed to compile the project:
> * `cache`: **For storing project dependencies**
> Caches can increase the speed of a given job in subsequent pipelines. You can store downloaded dependencies so that they don’t have to be fetched from the internet again. Dependencies include things like npm packages, Go vendor packages, and so on. You can configure a cache to pass intermediate build results between stages, but you should use artifacts instead. 
> * `artifacts`: **Use for stage results that are passed between stages.**
> Artifacts are files that are generated by a job so they can be stored and uploaded. You can fetch and use artifacts in jobs in later stages of the same pipeline. You can’t create an artifact in a job in one stage, and use this artifact in a different job in the same stage. This data is not available in different pipelines, but can be downloaded from the UI.
> 
> If you download modules while building your application, you can declare them as artifacts and subsequent stage jobs can use them.
>
> You can define an expiry time so artifacts are deleted after a defined time. Use dependencies to control which jobs fetch the artifacts.
> 
> Artifacts can also be used to make files available for download after a pipeline completes, like a build image.

Let's improve our script with below code:

```
stages:
  - build
  - test

build:
  stage: build
  image: node:14.16.1-alpine3.13
  before_script:
    - echo "Start building app..."
  script:
    - npm install
    - npm run build
    - echo "Build successfully"
  artifacts:
    expire_in: 1 hour
    paths:
      - build
      - node_modules/

test:
  stage: test
  image: node:14.16.1-alpine3.13
  before_script:
    - echo "Start testing app..."
  script:
    - npm test
    - echo "Test successfully!"
```

Commit and push your code, you should get the same result but more faster.

## 5.3 Pipelines - Docker Build

Now it's time to dockerlise our project in a Nginx docker image just like what we did at step 3. We will use docker in docker to buid this image. 

***IMPORTANT:***
Based on the offical document [here](https://docs.gitlab.com/ee/ci/docker/using_docker_build.html),
> If you want to use Docker-in-Docker, you must always use privileged = true in your Docker containers.

So we should register a new runner with `privileged` enabled.

```
sudo gitlab-runner register -n --url https://gitlab.yourdomian.com/ --registration-token REGISTRATION_TOKEN --executor docker --description "My Docker Runner" --docker-image "docker:20.10.6-dind"  --docker-privileged --docker-volumes "/certs/client"
```

otherwise, you probably get an error like below when running the pipelines process

```
error during connect: Post http://docker:2375/v1.24/auth: dial tcp: lookup docker on 168.63.129.16:53: no such host
Cleaning up file based variables
00:01
ERROR: Job failed: exit code 1
```
<img width="806" alt="image" src="https://user-images.githubusercontent.com/10986601/116033306-c3c80100-a693-11eb-80b6-c55b9c77313a.png">

***Note:***
If you register a new runner with `privileged` enabled, you should use this new runner to run the docker in docker jobs. You may can choose to delete the older runner in `/etc/gitlab-runner/config.toml` file or using gitlab-runner `tags` to specify which runner to excute which specific jobs with the same `tags`.

Add the below code to your `.gitlab-ci.yml`

```
stages:
  - build
  - test
  - docker-build

build:
  stage: build
  image: node:14.16.1-alpine3.13
  before_script:
    - echo "Start building app..."
  script:
    - npm install
    - npm run build
    - echo "Build successfully"
  artifacts:
    expire_in: 1 hour
    paths:
      - build
      - node_modules/

test:
  stage: test
  image: node:14.16.1-alpine3.13
  before_script:
    - echo "Start testing app..."
  script:
    - npm test
    - echo "Test successfully!"

docker-build:
  stage: docker-build
  image: docker:latest
  services:
    - name: docker:20.10.6-dind
  before_script:
    - docker login --username "$CI_REGISTRY_USER" --password "$CI_REGISTRY_PASSWORD" $CI_REGISTRY
  script:
    - docker build --pull -t $CI_REGISTRY_IMAGE .
    - docker push $CI_REGISTRY_IMAGE
    - echo "Registry Image:" $CI_REGISTRY_IMAGE
```

Commit and push your code. You will see a new `docker-build` is added and passed.

<img width="729" alt="image" src="https://user-images.githubusercontent.com/10986601/116048363-aef56880-a6a7-11eb-8dd9-5b5f8caec46e.png">

Now go to your project side bar and click `Packages & Registries` -> `Container Registry`, you will also find the image that you just built by docker in docker has been uploaded.

<img width="1016" alt="image" src="https://user-images.githubusercontent.com/10986601/116048664-f976e500-a6a7-11eb-8956-c8187818a2af.png">

