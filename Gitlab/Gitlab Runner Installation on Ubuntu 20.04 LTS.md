# Install Gitlab Runner on Ubuntu 20.04 LTS

*Reference:* [Install GitLab Runner manually on GNU/Linux](https://docs.gitlab.com/runner/install/linux-manually.html)

## Prerequirement:

1. Before installing the gitlab-runner, Please [install Docker](https://github.com/PFTian/TechNotes/blob/master/Docker/Docker%20Installation.md) at first.

2. Notice: 
    > We strongly advise against installing GitLab Runner on the same machine you plan to install GitLab on. Depending on how you decide to configure GitLab Runner and what tools you use to exercise your application in the CI environment, GitLab Runner can consume significant amount of available memory.

    >Memory consumption calculations, that are available above, will not be valid if you decide to run GitLab Runner and the GitLab Rails application on the same machine.

    >It is also not safe to install everything on a single machine, because of the security reasons - especially when you plan to use shell executor with GitLab Runner.

    >We recommend using a separate machine for each GitLab Runner, if you plan to use the CI features.

## Download

To download the appropriate package for your system:

1. Find the latest file name and options at https://gitlab-runner-downloads.s3.amazonaws.com/latest/index.html

2. Choose a version and download a binary, as described in the documentation for [downloading any other tagged releases](https://docs.gitlab.com/runner/install/bleeding-edge.html#download-any-other-tagged-release) for bleeding edge GitLab Runner releases.

For Debian or Ubuntu

```bash
curl -LJO https://gitlab-runner-downloads.s3.amazonaws.com/latest/deb/gitlab-runner_<arch>.deb
```

For my example: 

```bash
curl -LJO https://gitlab-runner-downloads.s3.amazonaws.com/latest/deb/gitlab-runner_amd64.deb
```

## Install

### Install the package for your system as follows:

For Debian or Ubuntu

```bash
sudo dpkg -i gitlab-runner_<arch>.deb
```

For my example

```bash
sudo dpkg -i gitlab-runner_amd64.deb
```

### Register the Runner

1. Run the following command: 

```bash
sudo gitlab-runner register
```

2. Enter your GitLab instance URL:

```
Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com )
https://gitlab.com
```

3. Enter the token you obtained to register the Runner:

```
Please enter the gitlab-ci token for this runner
xxx
```

The information of above step 2 & 3 can be found in the **Admin Area** of gitlab.

* Login gitlab as administrator and click the Admin Area icon on the top. 
* Go to **Overview** section and Click **Runner**.
* You can find the information on the right.
![image](https://user-images.githubusercontent.com/17267466/90331860-82526280-dfea-11ea-9051-a096133fc496.png)

4. Enter a description for the Runner, you can change this later in GitLab’s UI:

```
Please enter the gitlab-ci description for this runner
[hostname] my-runner
```

5. Enter the tags associated with the Runner, you can change this later in GitLab’s UI:

```
Please enter the gitlab-ci tags for this runner (comma separated):
my-tag,another-tag
```

6. Enter the Runner executor:

```
Please enter the executor: ssh, docker+machine, docker-ssh+machine, kubernetes, docker, parallels, virtualbox, docker-ssh, shell:
docker
```

7. If you chose Docker as your executor, you’ll be asked for the default image to be used for projects that do not define one in .gitlab-ci.yml:

```
Please enter the Docker image (eg. ruby:2.6):
alpine:latest
```

8. If the runner is registered successfully, it will show you the below message.

```
Runner registered successfully. Feel free to start it, but if it's running already the config should be automatically reloaded!
```

You can also go to the **Admin Area**, Click **Overview > Runner** to check the configured shared runner.

![image](https://user-images.githubusercontent.com/17267466/90332057-47512e80-dfec-11ea-9ed4-57225dd3cae9.png)

## Update

Download the latest package for your system then upgrade as follows:

For Debian or Ubuntu:

```bash
dpkg -i gitlab-runner_<arch>.deb
```
