# Jenkins GitHub Polling
Tutorial on how to use Jenkins with GitHub and take advantage of the polling system.

# Table Of Contents
- [Installation](#installation)
  - [Java](#java)
  - [Jenkins](#jenkins)
- [Configuration](#configuration)
  - [Jenkins](#jenkins-1)
  - [Adding GitHub Credentials](#adding-github-credentials)
  - [Adding repository polling on GitHub](#adding-repository-polling-on-github)
  - [Jenkins configuration with GitHub Polling on Push](#jenkins-configuration-with-github-polling-on-push)
- [Example of Jenkinsfile](#example-of-jenkinsfile)
- [Common errors in Jenkinsfile](#common-errors-in-jenkinsfile)
  - [Authentication error in Jenkins on using sudo](#authentication-error-in-jenkins-on-using-sudo)

## Installation

### Java
Firstly you'll have to install Java JDK, JRE (credits to [DigitalOcean - How To Install Java with Apt on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-install-java-with-apt-on-ubuntu-20-04#installing-specific-versions-of-openjdk)).

```bash
sudo apt update
```
Next, check if Java is already installed:
```bash
java -version
```
If Java is not currently installed, you’ll see the following output:
```bash
Command 'java' not found, but can be installed with:

sudo apt install openjdk-11-jre-headless  # version 11.0.11+9-0ubuntu2~20.04, or
sudo apt install default-jre              # version 2:1.11-72
sudo apt install openjdk-13-jre-headless  # version 13.0.7+5-0ubuntu1~20.04
sudo apt install openjdk-16-jre-headless  # version 16.0.1+9-1~20.04
sudo apt install openjdk-8-jre-headless   # version 8u292-b10-0ubuntu1~20.04
```
Execute the following command to install the default Java Runtime Environment (JRE), which will install the JRE from OpenJDK 11:
```bash
sudo apt install default-jre
```
Next, check if Java is already installed:
```bash
java -version
```
You’ll see output similar to the following:
```bash
openjdk version "11.0.11" 2021-04-20
OpenJDK Runtime Environment (build 11.0.11+9-Ubuntu-0ubuntu2.20.04)
OpenJDK 64-Bit Server VM (build 11.0.11+9-Ubuntu-0ubuntu2.20.04, mixed mode, sharing))
```
You may need the Java Development Kit (JDK) in addition to the JRE in order to compile and run some specific Java-based software. To install the JDK, execute the following command, which will also install the JRE:
```bash
sudo apt install default-jdk
```
Verify that the JDK is installed by checking the version of javac, the Java compiler:
```bash
javac -version
```

### Jenkins
First, add the repository key to the system (credits to [DigitalOcean - How To Install Jenkins on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-install-jenkins-on-ubuntu-20-04)):

```bash
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
```
After the key is added the system will return with OK.
Next, let’s append the Debian package repository address to the server’s sources.list:
```bash
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
```
```bash
sudo apt update
```
Finally, we’ll install Jenkins and its dependencies.
```bash
sudo apt install jenkins
```
Start and verify if Jenkins is running:
```bash
sudo systemctl start jenkins
sudo systemctl status jenkins
```

## Configuration

### Jenkins
Go to ```http://your_server_ip_or_domain:8080``` to configure Jenkins. You'll see this image:
![image](https://user-images.githubusercontent.com/22435398/143280579-41217dbf-38d6-495a-8f3c-9af0baa96bfb.png)
Get the password from:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
Install suggested plugins:
![image](https://user-images.githubusercontent.com/22435398/143280671-b45f5887-3c55-4615-b50d-b5ba346a4449.png)
Create your new user and password for the Jenkins server.

### Adding Github credentials
Go to Manage Jenkins
![image](https://user-images.githubusercontent.com/22435398/143283266-9fbb21bb-9c1c-4612-bfc4-d1a4db87ef53.png)

Then hit Manage Credentials
![image](https://user-images.githubusercontent.com/22435398/143283364-6484554a-7c3a-42ce-8e17-e28fdd9b8f3a.png)

Then go to Global
![image](https://user-images.githubusercontent.com/22435398/143283461-3a84c346-4f3c-43b6-b78d-407e9bed3a44.png)

Add Credential
![image](https://user-images.githubusercontent.com/22435398/143283553-b8ca550b-a222-43d9-8e8a-d327cb461a02.png)

If you're using a PAT (Personal Access Token):
- Kind: select ```Username with Password```
- Scope: Global (Jenkins, nodes...)
- Username: give it a name (any name)
- Password: paste your PAT
- Hit OK

![image](https://user-images.githubusercontent.com/22435398/143283802-03c968c7-c581-49ab-b5f9-59e8d92c7c8c.png)

--------------------------------------------
### Adding repository polling on GitHub
Go to your repository on GitHub, then go to ```Settings > Webhooks > Add webhook```

![image](https://user-images.githubusercontent.com/22435398/143284780-523f0022-3b7b-4270-81c1-06228a314b74.png)

- In <code>Payload URL</code> add your Jenkins server address, adding ```/github-webhook/``` at the final, to look like: <code>http://{IP Jenkins Server}:8080/github-webhook/</code>
- In <code>Content type</code>, choose ```application/json```
- Leave <code>Secret</code> blank
- Choose in which events you want to trigger the webhook
- Check the <code>Active</code> checkbox
- Hit <code>Add webhook</code>
  
![image](https://user-images.githubusercontent.com/22435398/143285446-06d68ca0-175b-4e31-b7c4-30709519cefe.png)


--------------------------------------------
### Jenkins configuration with GitHub Polling on Push
Inside Jenkins server, click on New Item
![image](https://user-images.githubusercontent.com/22435398/143281047-fb50f93c-d587-4522-aa63-1be1ee499382.png)
Give the process a name, select <code>Pipeline</code> and click on OK.
![image](https://user-images.githubusercontent.com/22435398/143281233-783f0514-1e80-4e8e-b761-a85d04c7af04.png)

Add some configurations:
![image](https://user-images.githubusercontent.com/22435398/143281607-1d203747-3bb9-4d4f-8baa-75dc6a74ee5d.png)

<b>GitHub hook trigger for GITScm polling</b> must be checked in order to generate the build when some event occurs in GitHub(push, pull...)
![image](https://user-images.githubusercontent.com/22435398/143281645-7a795b67-64d8-4820-8b20-eea274378b9f.png)

Add the GitHub repository that you're going to publish, add your GitHub Credentials and select the specific branch you want to pull (or leave it blank for any).
![image](https://user-images.githubusercontent.com/22435398/143282605-60972887-4934-41da-9e77-8b97bce54711.png)

Add some behaviours (it's needed if you want to work with submodules)
![image](https://user-images.githubusercontent.com/22435398/143282762-4032de20-d1a8-49e8-8fc5-84137f1fe42c.png)

Finally, put the name of your Jenkinsfile file and then hit Save.
![image](https://user-images.githubusercontent.com/22435398/143282843-f93adcc4-5835-4c6b-aa77-444d729f8721.png)

## Example of Jenkinsfile
```yml
pipeline{
    agent any
    
    stages{
        stage('Run Docker Compose'){
            steps {
                sh """ #!/bin/bash
                    git submodule update --init --recursive
                """
                sh """ #!/bin/bash
                    sudo docker-compose -f docker-compose-staging.yml up -d --build
                """
            }
        }  
    }
}
```

## Common errors in Jenkinsfile
### Authentication error in Jenkins on using sudo
```bash
sudo su    
visudo -f /etc/sudoers
```
add the following line at the end: ```jenkins ALL= NOPASSWD: ALL```
