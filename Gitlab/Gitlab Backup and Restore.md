# Migrate Gitlab from one server to another

This document is mainly for migrating Gitlab (that is installed via Omnibus) version 13.x.x. And it has also been tested by the author.

For more details, please use [offical document](https://docs.gitlab.com/13.10/ee/raketasks/backup_restore.html) as reference 

## Requirement/Background

Migrate gitlab 13.5.4 to 13.10.3

Gitlab was installed by using Omnibus

Linux version is Ubuntu 20.04LTS

## IMPORTANT:
The old Gitlab version should be exact as the same version as the new Gitlab version

In my case, I upgrade my old `Gitlab 13.5.4` to `Gitlab 13.10.3`

The upgrade can be done by using

```bash
sudo apt-get update
sudo apt-get upgrade
```

Or you can also install the old Gitlab version on your new server

## Backup Gitlab on the old server

### 1. Create backup
For the Gitlab 12.2 or later and installed via Omnibus

Use the following command line to do the backup

```bash
sudo gitlab-backup create
```

The defualt location of the backup is at
```
/var/opt/gitlab/backups/
```
and named as (In my case)
```
1619023211_2021_04_21_13.10.3-ee_gitlab_backup.tar
```

You can also find your location at `/etc/gitlab.rb` configuration `gitlab_rails['backup_path']`

### 2. Storing configuration files

>The backup Rake task GitLab provides does not store your configuration files. The primary reason for this is that your database contains items including encrypted information for two-factor authentication and the CI/CD secure variables. Storing encrypted information in the same location as its key defeats the purpose of using encryption in the first place.

At the very **minimum**, you must backup:

- `/etc/gitlab/gitlab-secrets.json`
- `/etc/gitlab/gitlab.rb`

### 3. Transfer backup files to new server

At your old server, create a folder named as `old-gitlab` at your home folder.

```bash
mkdir ~/old-gitlab
```

Copy all the files mentioned in step 1 and 2 above to this folder

```bash
sudo cp /etc/gitlab/gitlab-secrets.json  ~/old-gitlab/gitlab-secrets.json
sudo cp /etc/gitlab/gitlab.rb  ~/old-gitlab/gitlab.rb
sudo cp /var/opt/gitlab/backups/1619023211_2021_04_21_13.10.3-ee_gitlab_backup.tar  ~/old-gitlab/1619023211_2021_04_21_13.10.3-ee_gitlab_backup.tar
```

Transfering all the files from old server to new server (at home folder) by using `scp`

```
scp -r ~/old-gitlab yourname@newserver.com:~/
```

4. Restore the backup on the new server

> This procedure assumes that:
> -  You have installed the exact same version and type (CE/EE) of GitLab Omnibus with which the backup was created.
> - You have run sudo gitlab-ctl reconfigure at least once.
> - GitLab is running. If not, start it using sudo gitlab-ctl start.

First ensure your backup tar file is in the backup directory described in the gitlab.rb configuration gitlab_rails['backup_path']. The default is /var/opt/gitlab/backups. It needs to be owned by the git user.

```bash
sudo cp ~/old-gitlab/1619023211_2021_04_21_13.10.3-ee_gitlab_backup.tar /var/opt/gitlab/backups/
sudo chown git.git /var/opt/gitlab/backups/1619023211_2021_04_21_13.10.3-ee_gitlab_backup.tar
```

Stop the processes that are connected to the database. Leave the rest of GitLab running:

```bash
sudo gitlab-ctl stop unicorn
sudo gitlab-ctl stop puma
sudo gitlab-ctl stop sidekiq
# Verify
sudo gitlab-ctl status
```
Next, restore the backup, specifying the timestamp of the backup you wish to restore:

```bash
sudo gitlab-backup restore BACKUP=1619023211_2021_04_21_13.10.3-ee
```

***NOTE:*** You must only use the backup file name without `_gitlab_backup.tar` as the value of paramter `BACKUP`

Next, restore `/etc/gitlab/gitlab-secrets.json` and `/etc/gitlab/gilab.rb`

```bash
sudo cp ~/old-gitlab/gitlab-secrets.json /etc/gitlab/
sudo cp ~/old-gitlab/gilab.rb /etc/gitlab/
```

If you use new domain name on the new server, you should also change the old domain name to your new domain name at `/etc/gitlab/gitlab.rb`

Reconfigure, restart and check GitLab:
```bash
sudo gitlab-ctl reconfigure
sudo gitlab-ctl restart
# After a while, when gitlab starts completely
sudo gitlab-rake gitlab:check SANITIZE=true
```

In GitLab 13.1 and later, check database values can be decrypted especially if /etc/gitlab/gitlab-secrets.json was restored, or if a different server is the target for the restore.

```
sudo gitlab-rake gitlab:doctor:secrets
```
