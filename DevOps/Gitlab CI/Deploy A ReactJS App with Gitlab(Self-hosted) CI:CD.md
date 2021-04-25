# Deploy A ReactJS App with Gitlab(Self-hosted) CI/CD

This article is mainly referred from [GitLab CI/CD example with a dockerized ReactJS App 🚀](https://dev.to/christianmontero/gitlab-ci-cd-example-with-a-dockerized-reactjs-app-1cda), I just made some personalized changes for my notes as learning purpose.

## 1. Setup a Gitlab service on a server

Your can follow [Gitlab Installation on Ubuntu 20.04 LTS](https://github.com/PFTian/TechNotes/blob/master/Gitlab/Gitlab%20Installation.md) or [offical document](https://about.gitlab.com/install/#ubuntu) to install Gitlab on a new Ubuntu server.

Basically we just need to do

1. Install and configure the necessary dependencies

    ```
    sudo apt-get update
    sudo apt-get install -y curl openssh-server ca-certificates tzdata perl
    ```

2. Install Postfix
    ```
    sudo apt-get install -y postfix
    ```
    Choose `Internet Site` and use your server's external DNS as `mail name`.

3. Add the GitLab package repository and install the package

    ```
    curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh | sudo bash   
    ```
    Use your domain name as parameter to install the gitlab
    ```
    sudo EXTERNAL_URL="https://gitlab.yourdomain.com" apt-get install gitlab-ee
    ```

## 2. Install a Gitlab Runner (Better on A Seperate Machine)

Gitlab runner is an application that runs Giltab jobs in piplines

> you should install GitLab Runner on a machine that’s separate from the one that hosts the GitLab instance for security and performance reasons.


