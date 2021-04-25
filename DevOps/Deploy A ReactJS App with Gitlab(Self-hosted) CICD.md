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

Gitlab runner is an application that runs Giltab jobs in piplines

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
    RUN npm react-scripts@3.0.1 -g
    # Build your application
    RUN npm build

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

    
