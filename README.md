# Real Time DevOps Project | Deploy to Kubernetes Using Jenkins | End to End DevOps Project | CICD

## Project Description

Creating a CI/CD pipeline for a Java application to deploy on a Kubernetes cluster using Jenkins is a complex and multi-step process. In this blog post, we’ll walk through each step in detail, from installing and configuring the necessary tools to deploying the application on Kubernetes. We will also discuss the integration of Jenkins with SonarQube for static code analysis, Dockerizing the application, scan docker image using trivy then update build number in manifest and deploy it on EKS cluster through ArgoCD Let’s get started.

### Workflow Diagram

![workflow](https://github.com/user-attachments/assets/c9951c4e-316c-4305-8d97-b241babd84da)

### Pre-requisites
  - Login to GitHub, Docker Hub and AWS Accouts
  - Launch EC2 instances 3: Jenkins Master, Agent and EKS Bootstrap server
  - T2 Micro for Jenkins master and EKS Bootstrap server and T2.medium for Agent
  - Security Group: create new group with open All traffic- No recommended in Prod environment, its just for practice
  - Default VPC , RAM 15GB

### Setup and Configuration

__Jenkins Master Server__

    sudo apt update
    sudo apt install openjdk-17-jre
    java -version
    sudo wget -O /usr/share/keyrings/jenkins-keyring.asc   https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
    echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]   https://pkg.jenkins.io/debian-stable binary/ | sudo tee   /etc/apt/sources.list.d/jenkins.list > /dev/null
    sudo apt-get update
    sudo apt-get install jenkins
    sudo systemctl enable jenkins
    sudo systemctl status jenkins
    
    open file : sudo vim /etc/ssh/sshd_config  - remove comment for below keys
    PubkeyAuthentication yes 
      AuthorizedKeysFile      .ssh/authorized_keys .ssh/authorized_keys2
    
    sudo service sshd reload
    Generate ssh key  : ssh-keygen
    
    # Execute it when you setup agent machine
    Copy id_rsa.pub file content to agent - <User>/.ssh/authorized_keys (do not remove exisiting content just add it to new line)


__Agent Machine__
    
    sudo apt update
    sudo apt install openjdk-17-jre
    sudo apt-get install docker.io
    docker --version
    sudo usermod -aG docker $USER
    sudo init 6
    
    open file : sudo vim /etc/ssh/sshd_config  remove comment for below values
      PubkeyAuthentication yes 
      AuthorizedKeysFile      .ssh/authorized_keys .ssh/authorized_keys2
    
    sudo service sshd reload


  - Open Jenkins UI : http:<Public_IP_of_jenkinsmaster>:8080
  - Copy initial password from cat /var/lib/jenkins/secrets/initialAdminPassword and install suggested plugins
  - Install Below plugins

  1]Docker </br>
  2]Docker Commons </br>
  3]Docker Pipeline </br>
  4]Docker API </br>
  5]docker-build-step </br>
  6]CloudBees Docker Build and Publish </br>
  7]Maven Integration Pipeline </br>
  8]Maven Integration </br>
  9]Eclipse Temurin installer </br>
  10]Maven Integration Pipeline </br>
  11]Maven Integration </br>
  12]Eclipse Temurin installer </br>
  13]SonarQube Scanner </br>
  14]Sonar Quality Gates </br>
  15]Quality Gates </br>


__Integrate Maven to Jenkins and Add GitHub Credentials to Jenkins__

  - Manage jenkins — tools — Maven installations — Name : Maven3 , Version: 3.9.4 Install automatically
  - JDK installations — Name: Java17 , Install from adoptium.net — Version: jdk-17.0.5+8
  - Add credentials for github — Credentials — new credentials
    — kind: Username with password
    — Scope: Global
    — Usermame: github username
    — password: github passwrd
    — id: github

__Install and Configure the SonarQube(On Jenkins Agent Machine)__

Installing SonarQube as a docker container.

__Integrate SonarQube with Jenkins__

  - Login to sonarqube UI — <Server_public_IP>:9000
  - default credentials: admin/admin
  - Go to my account- security — Generate Token — Name: jenkins sonarqube-token Type: Global analysis token
  - Jenkins — manage jenkins- credentials
      — kind: Secret text
      — scope: global
      — secret : copy token which is generated in sonarqube-token
      — ID: jenkins-sonarqube-token
  - Manage jenkins — system — SonarQube servers- SonarQube installations
        Name: sonarqube-server
        Server URL:
        credentials : token
        apply and save
  - Manage jenkins — Tools — SonarQube Scanner installations — Add SonarQube Scanner
      name: sonarqube-scanner
      tick on — install automatically
      version: sonarqube scanner 5.0.1.3006
      apply and save
  - Add sonarqube webhook configuration — Sonarqube — Administration- configuration — webhooks — create name : sonarqube-webhook
    URL: http://<Jenkins_master_private_IP>:8080/sonarqube-webhook/
