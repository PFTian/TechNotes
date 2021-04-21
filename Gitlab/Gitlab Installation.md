
# Gitlab Installation
## Content

* [Requirements](#Requirements)
* [Installation](#Installation)

## Requirements

The official hardware requirements is listed in [this page](https://docs.gitlab.com/ee/install/requirements.html), Please check this **`Requirement`** document before you install the gitlab.

Based on the official document, the below hardware is needed for a server.

> ## CPU
> The following is the recommended minimum CPU hardware guidance for a handful of example GitLab user base sizes.
> * **4 cores** is the **recommended** minimum number of cores and supports up to 500 users
> * 8 cores supports up to 1000 users

> ## Memory
> The following is the recommended minimum Memory hardware guidance for a handful of example GitLab user base sizes.
> * **4GB RAM** is the **required** minimum memory size and supports up to 500 users
> * 8GB RAM supports up to 1000 users

Based on my testing/experiment. The minimum hardware requirement that can run gitlab smoothly is

Hardware | Value
---- | ----
CPU  | 2 Cores
Memory | 4GB RAM

With this hardware, we don't need to add any swap space on the server.

However, based on the suggestion from the official document

> In addition to the above, we generally recommend having at least 2GB of swap on your server, even if you currently have enough available RAM. Having swap will help reduce the chance of errors occurring if your available memory changes. We also recommend configuring the kernel’s swappiness setting to a low value like 10 to make the most of your RAM while still having the swap available when needed.

We'd better to add a swap space on the server. (*Reference*: [How to add swap space on ubuntu 18.04](https://linuxize.com/post/how-to-add-swap-space-on-ubuntu-18-04/))


With adding swap space (at leaset 2 GB) on linux server, the machine with below hardware can also run the gitlab.

Hardware | Value
---- | ----
CPU  | 1 Core
Memory | 1GB RAM
Swap | 4GB

Hardware | Value
---- | ----
CPU  | 1 Core
Memory | 2GB RAM
Swap | 2GB

## Installation

OS: *Ubuntu 16.04LTS 18.04LTS 20.04LTS*

### 1. Install and configure the necessary dependencies

**STEP 1**

```bash
sudo apt-get update
sudo apt-get install -y curl openssh-server ca-certificates
```

**STEP 2**

Next, install `Postfix` to send notification emails. If you want to use another solution to send emails please skip this step and [configure an external SMTP server](https://docs.gitlab.com/omnibus/settings/smtp.html) after GitLab has been installed.

```bash
sudo apt-get install -y postfix
```

During Postfix installation a configuration screen may appear. Select `Internet Site` and press enter. Use your server's external DNS for `mail name` and press enter. If additional screens appear, continue to press enter to accept the defaults.

*TODO*:
- [ ] Solve how to config postfix. Based on the current postfix configuration during above description, the postfix cannot send email to the registered users.

### 2. Add the GitLab package repository and install the package

**STEP 1**

Add the GitLab package repository

```bash
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh | sudo bash
```

*__NOTE: Mainland Server Installation__* 

For the server in the mainland China, it is better to change the gitlab official repository to  tsinghua gitlab repository, otherwise, it will be very slow to download the gitlab. 

```bash
sudo vim /etc/apt/sources.list.d/gitlab_gitlab-ce.list
```
Replace or comment the below lines

```
deb https://packages.gitlab.com/gitlab/gitlab-ee/ubuntu/ bionic main
deb-src https://packages.gitlab.com/gitlab/gitlab-ee/ubuntu/ bionic main
```

to

```
deb https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/ubuntu bionic main

deb-src https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/ubuntu bionic main
```

Then, excute

```bash
sudo apt-get update
```

**STEP 2**

Next, install the GitLab package. Change `https://gitlab.example.com` to the URL at which you want to access your GitLab instance. Installation will automatically configure and start GitLab at that URL.

For `https://` URLs GitLab will automatically request a certificate with **Let's Encrypt**, which requires inbound HTTP access and a valid hostname. You can also use your own certificate or just use `http://`.

```bash
sudo EXTERNAL_URL="https://gitlab.example.com" apt-get install gitlab-ee
```

*Reference* [How to assign a domain name to a server]()

### 3. Browse to the hostname and login 

On your first visit, you'll be redirected to a password reset screen. Provide the password for the initial administrator account and you will be redirected back to the login screen. Use the default account's username `root` to login.

See our [documentation for detailed instructions on installing and configuration](https://docs.gitlab.com/omnibus/README.html#installation-and-configuration-using-omnibus-package).

# What is NEXT?

After installing the Gitlab, we need to config CI/CD on the Gitlab.

1. [Install Docker on the server](https://github.com/LeoTianJob/TechWiki/wiki/Docker-Installation)
2. [Install gitlab runner on the server](https://github.com/LeoTianJob/TechWiki/wiki/Gitlab-Runner)
