# AUTO DEPLOY FROM GITLAB

## Install gitlab runner

https://docs.gitlab.com/runner/install/linux-manually.html

Simply download one of the binaries for your system:

#### Linux x86-64
````sudo curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64````

#### Linux x86
````sudo curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-386````

#### Linux arm
````sudo curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-arm````

#### Linux arm64
````sudo curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-arm64````

You can download a binary for every available version as described in Bleeding Edge - download any other tagged release.

Give it permissions to execute:

````sudo chmod +x /usr/local/bin/gitlab-runner````

Create a GitLab CI user:

````sudo useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash````

Install and run as service:

````sudo gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner
sudo gitlab-runner start
````

## Regsiter the GitLab runner

https://docs.gitlab.com/runner/register/ 

* Go to the project repository
* Next Settings
* Next CI/CD
* Into Runner section -- disable shared runners

Under **Specific Runners** copy and save somewhere the token and url for runner setup

Go back to the terminal and regsiter this runner using the follwoing commands

1. Run this

    ````sudo gitlab-runner register````
2. Enter your GitLab instance URL:

    ````Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com ) the one you copied in previous step.````
    ````https://gitlab.com````

3. Enter the token you obtained to register the Runner:

    ````Please enter the gitlab-ci token for this runner````
    ````xxx````
4. Enter a description for the Runner, you can change this later in GitLab’s UI:

    ````Please enter the gitlab-ci description for this runner````
    ````[hostname] my-runner````

5. Enter the tags associated with the Runner, you can change this later in GitLab’s UI: Not needed to give

    ````Please enter the gitlab-ci tags for this runner (comma separated):````
    ````my-tag,another-tag````

6. Enter the Runner executor:

    ````Please enter the executor: ssh, docker+machine, docker-ssh+machine, kubernetes, docker, parallels, virtualbox, docker-ssh, shell:````
    ````shell````

7. If you chose Docker as your executor, you’ll be asked for the default image to be used for projects that do not define one in .gitlab-ci.yml:

    ````Please enter the Docker image (eg. ruby:2.6):````
    ````alpine:latest````

Once you have done this if you reload the gitlab page you must find the runners in the specific runners

**START THE SERVICE**

````sudo gitlab-runner start````

## SSH connection / deployment from Gitlab CI

#### Prequisites:
Make sure you can connect from your termainal to the ec2 instance via SSH

To access your instance:
1. Open an SSH client. (find out how to connect using PuTTY)
2. Locate your private key file (<key_name>.pem). The wizard automatically detects the key you used to launch the instance.
3. Your key must not be publicly viewable for SSH to work. Use this command if needed:

    ````chmod 400 <key_name>.pem````

4. Connect to your instance using its Public DNS:

    ````ec2-xx-xxx-xxx-xxx.<region>.compute.amazonaws.com````

Example:

````ssh -i "<key_name>.pem" ec2-user@ec2-xx-xxx-xxx-xxx.<region>.compute.amazonaws.com````

Please note that in most cases the username above will be correct, however please ensure that you read your AMI usage instructions to ensure that the AMI owner has not changed the default AMI username.

The file **.gitlab-ci.yml** has the code for deployment 

## ADD SSH PRIVATE INTO THE CI/CD

* Go to the project repository
* Next Settings
* Next CI/CD
* Drop down **Variables**

Setting to do:
* Type: Variable
* Key : Name given in the .gitlab-ci.yml
* value: In the case of aws , it is the contents of the <key_name>.pem ,open the file with text editor and copy paste the content inside this Value.
* save