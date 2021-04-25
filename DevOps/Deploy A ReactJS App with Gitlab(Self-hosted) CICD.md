# Deploy A ReactJS App with Gitlab(Self-hosted) CI/CD

This article is mainly referred from [GitLab CI/CD example with a dockerized ReactJS App 🚀](https://dev.to/christianmontero/gitlab-ci-cd-example-with-a-dockerized-reactjs-app-1cda), I just made some personalized changes for my notes as learning purpose.

## 1. Setup a Gitlab service on a server

Your can follow [Gitlab Installation on Ubuntu 20.04 LTS](https://github.com/PFTian/TechNotes/blob/master/Gitlab/Gitlab%20Installation.md) or [offical document](https://about.gitlab.com/install/#ubuntu) to install Gitlab on a new Ubuntu server.

Basically we just need to do

1. Install and configure the necessary dependencies

    ```bash
    sudo apt-get update
    sudo apt-get install -y curl openssh-server ca-certificates tzdata perl
    ```

2. Install Postfix
    ```bash
    sudo apt-get install -y postfix
    ```
    Choose `Internet Site` and use your server's external DNS as `mail name`.

3. Add the GitLab package repository and install the package

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

1. Download the latest `deb` file
    ```bash
    curl -LJO https://gitlab-runner-downloads.s3.amazonaws.com/latest/deb/gitlab-runner_amd64.deb
    ```

2. Install the package
    ```bash
    sudo dpkg -i gitlab-runner_amd64.deb
    ```
3. Register your runner with command
    ```bash
    sudo gitlab-runner register
    ```
4. Enter your Gitlab instance URL
    ```
    Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com )
    https://gitlab.yourdomain.com
    ```
5. Enter the token you obtained to register the Runner:
    ```
    Please enter the gitlab-ci token for this runner
    Register Token
    ```

## 3. Create a New Project on Gitlab

Login to your Gitlab and create a new project (I will name it bright-future)

1. Click `New project`
    ![image](https://user-images.githubusercontent.com/10986601/116002743-bd9f3980-a62d-11eb-9894-92f2c62dd49c.png)

2. Choose `Create blank project`
    ![image](https://user-images.githubusercontent.com/10986601/116002892-4f0eab80-a62e-11eb-8182-b874f968df9d.png)

3. Fill the project information and click `Create project`
    ![image](https://user-images.githubusercontent.com/10986601/116002834-166ed200-a62e-11eb-8fcc-e9bac09df966.png)


## 3. Create a ReactJS Application and Push to Gitlab repository

1. On your local machine, initialise a project called `bright-future` as well.

    ```bash
    npx create-react-app bright-future
    ```
2. After creating the project successfully, we run the project
    ```bash
    cd bright-future
    npm start
    ```
3. If everything is install correctly, you will see a default webpage shows as below on http://localhost:3000/
   ![image](https://user-images.githubusercontent.com/10986601/116003343-17a0fe80-a630-11eb-8d46-049737f2f283.png)

4. Check test passes
    Run the `test` command
    ```
    npm test
    ```
    In the console prompt, press `a` to run all tests, if everything goes well, we will see

    ![image](https://user-images.githubusercontent.com/10986601/116003979-41a7f000-a633-11eb-95bd-e1d7a69797e2.png)


5. Push the project to your Gitlab repository
    ```bash
    git init
    git remote add origin https://gitlab.yourdomain.cn/root/bright-future.git
    git add .
    git commit -m "Initial commit"
    git push -u origin master
    ```
6. Refresh the Gitlab repository, you will see your `Bright Future` has been listed there.
    ![image](https://user-images.githubusercontent.com/10986601/116003800-2dafbe80-a632-11eb-9136-411848b6ce11.png)
