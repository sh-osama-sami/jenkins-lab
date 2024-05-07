# Jenkins Node.js Pipeline

This repository contains a Jenkins pipeline script for hosting a simple website pulled from a private Git repository on an AWS EC2 instance using Docker. The pipeline automates the process of pulling the code, building a Docker image, and running the Docker container on a remote EC2 instance.

## Prerequisites

- Jenkins installed with Docker image:
  ```bash
  docker run -d -p 8080:8080 -v my-vol:/var/jenkins_home jenkins/jenkins:lts
  ```
- Role Based Authorization plugin installed:
- Navigate to **Manage Jenkins -> Plugins -> Install Role Based Authorization Strategy**

## Steps

### 1. User Management

- Create a new user:
- Navigate to **Manage Jenkins -> Users -> Create New User**
- Create a read role and assign it to the new user:
- Navigate to **Manage and Assign Roles -> Create a Role named "Developers"**
- Add the new user under **Assign Roles**

### 2. Freestyle Pipeline

- Create a freestyle pipeline and link it to the private Git repo:
- From the dashboard, click on **New Item** and select **Freestyle**
- Configure Git in SCM, add credentials with your Git token, branch name, and repo URL
- Check **Delete Before Each Build**
- Write your build steps:
  ```bash
  mkdir folder
  touch helloworld
  ```
  
### 3. Declarative Pipeline

- Create a declarative pipeline in Jenkins GUI for your own repo to execute "ls":
- Upload a declarative file to the private repo
- Use the **Pipeline from Git** option in Jenkins to locate the declarative file in the repo
- [Link to Declarative pipeline](Jenkins)
  
### 4. Scripted Pipeline

- Create a scripted pipeline in Jenkins GUI for your own repo to execute "ls":
- Upload a scripted file to the private repo
- Use the **Pipeline from Git** option in Jenkins to locate the scripted file in the repo
- [Link to Scripted pipeline](scripted)
  
### 5. Multibranch Pipeline

- Create the same with Jenkinsfile in your branches as a multibranch pipeline:
- Include a Jenkinsfile in each branch of your repository with the pipeline configuration
- Jenkins will automatically detect and create multibranch pipelines for each branch
![Screenshot from 2024-05-06 00-27-44](https://github.com/sh-osama-sami/jenkins-nodejs/assets/85364511/1d51df37-35fa-4130-8ff5-e994e8af3396)

### 6. Configure Jenkins Image to Run Docker Commands

- Configure Jenkins image to run Docker commands on your host Docker daemon:
- Create a Dockerfile that is based on the jenkins image in addition to commands that installes docker client
  [Link to the Dockerfile](Dockerfile)
- Mount the old container's volume to the new container 
- Mount the Docker socket into the Jenkins container or use Docker Remote API
  ```bash
  docker run -v /var/run/docker.sock:/var/run/docker.sock -v volume:/var/jenkins_home jenkins:lts 
  ```

### 7. CI/CD for Repository

- Create CI/CD for this repository: [jenkins_nodejs_example](https://github.com/mahmoud254/jenkins_nodejs_example.git)
- Configure a Jenkins pipeline to pull from this repository and execute build and deployment steps
- [Link to node-pipeline](nodejs_pipeline)
- ![image](https://github.com/sh-osama-sami/jenkins-nodejs/assets/85364511/5bc32604-787f-4dcb-84a4-d7615cf1a0ec)
- ![Screenshot from 2024-05-06 01-08-21](https://github.com/sh-osama-sami/jenkins-nodejs/assets/85364511/81961246-8ecb-42ac-9d85-54a01935334f)


## Docker Slave Configuration

### 1. Dockerfile for Jenkins Slave

- Create a Dockerfile to build an image for Jenkins slave:
- Include necessary dependencies for running Jenkins jobs like openjdk , git , docker cli
- Configure SSH server by installing openssh above Ubuntu base image in the docker file
- [Link to slave Dockerfile](Dockerfile.slave)
- Build the image using:
- ```bash
  docker build -t slave:latest -f Dockerfile.slave .
  ```

### 2. Jenkins Slave Container

- Create a container from the Jenkins slave image and configure SSH:
- ```bash
  docker run --name jenkins_slave -v /var/run/docker.sock:/var/run/docker.sock slave:latest
  ```
- Ensure SSH access is properly configured for communication with the Jenkins master
- Go inside the container using:
- ```bash
  docker exec -it jenkins_slave bash
  ```
- Use cat command to print the content of the private key file and copy it


### 3. Create Jenkins Node

- From the Jenkins master, create a new node with the slave container
    - In **Launch Method** section select **launch agents via ssh**
    - Use the private key in the ssh credentials of the node and the ip to connect to the slave server / container


## AWS Slave Configuration

### 4. Create AWS Instance

- Create an AWS EC2 instance to be used as a Jenkins slave:
- Note down the public IP address in the ansible inventory file for accessing the instance
- ![image](https://github.com/sh-osama-sami/jenkins-nodejs/assets/85364511/05b1ef5a-3ce7-48f9-8d2c-d81644e8f439)

### 5. Ansible Configuration

- Use Ansible to configure the EC2 instance to be a Jenkins slave:
- Install necessary dependencies and tools required for running Jenkins jobs:
- [Link to ansible file](ec2.yml)
- Set up SSH access and connection between the Jenkins master and the AWS slave

### 6. Run Pipeline on AWS Slave

- Run the same pipeline on the AWS slave:
- Configure the Jenkins pipeline to use the AWS slave node for executing jobs
- Configure the docker run stage to forward to port 80 
- Verify that the pipeline runs successfully on the AWS slave
- ![image](https://github.com/sh-osama-sami/jenkins-nodejs/assets/85364511/eeac2835-56bb-4911-93c0-f761278b0172)

### 7. Access Application

- Access the application using the EC2 public IP:
- Once the pipeline is successfully executed, access the deployed application using the public IP address of the AWS EC2 instance
- ![Screenshot from 2024-05-06 04-59-07](https://github.com/sh-osama-sami/jenkins-nodejs/assets/85364511/abd9d60d-ddfd-47f3-9303-dadc642ee7f6)


## Integrating Slack with Jenkins

### 1. Integrate Slack with Jenkins

- Install the Jenkins Slack plugin:
  - Navigate to **Manage Jenkins -> Manage Plugins -> Available**
  - Search for "Slack Notification Plugin" and install it
  
- Configure Slack integration:
  - Navigate to **Manage Jenkins -> Configure System**
  - Scroll down to the **Global Slack Notifier Settings** section
  - Add your Slack workspace URL and credentials
  
## Sending Slack Messages on Pipeline Stage Success

### 2. Send Slack Messages on Pipeline Stage Success

- Within your Jenkins pipeline script:
  - Use the `slackSend` step to send messages to Slack channels when specific stages are successful
  - Example:
    ```groovy
    pipeline {
        agent any
        stages {
            stage('Build') {
                steps {
                    // Your build steps
                }
                post {
                    success {
                        slackSend(channel: '#channel-name', message: 'Build successful!')
                    }
                }
            }
            // Other stages
        }
    }
    ```

## Installing Audit Logs Plugin

### 3. Install Audit Logs Plugin and Test

- Install the Jenkins Audit Trail plugin:
  - Navigate to **Manage Jenkins -> Manage Plugins -> Available**
  - Search for "Audit Trail" and install it
  - Configure the plugin to log actions you want to track
  
- Test the plugin:
  - Perform various actions in Jenkins such as job creation, configuration changes, etc.
  - Verify that these actions are logged in the Audit Trail
  
## Forking and Dockerizing the Django App

### 4. Fork and Dockerize the Django App

- Fork the repository [Booster_CI_CD_Project](https://github.com/mahmoud254/Booster_CI_CD_Project) to your GitHub account
  
- Dockerize the Django app:
  - Add a Dockerfile to the root of the project to define the environment for running the Django app
  - Example Dockerfile:
    ```Dockerfile
    FROM python:3.8
    
    WORKDIR /app
    
    COPY requirements.txt ./
    
    RUN pip install --no-cache-dir -r requirements.txt
    
    COPY . .
    
    CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
    ```
  
- Use GitHub Actions to build and push the Docker image to Docker Hub:
  - Create a GitHub Actions workflow YAML file (e.g., `.github/workflows/docker.yml`) in the root of the repository
  - Configure the workflow to build the Docker image and push it to Docker Hub on each push to the main branch
  - Example GitHub Actions workflow:
    ```yaml
    name: Build and Push Docker Image
    
    on:
      push:
        branches:
          - main
    
    jobs:
      build:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout repository
            uses: actions/checkout@v2
          
          - name: Login to Docker Hub
            uses: docker/login-action@v1
            with:
              username: ${{ secrets.DOCKER_USERNAME }}
              password: ${{ secrets.DOCKER_PASSWORD }}
          
          - name: Build Docker image
            run: docker build -t yourdockerhubusername/appname .
          
          - name: Push Docker image
            run: docker push yourdockerhubusername/appname
    ```



## Contributors

- [sherry](https://github.com/sh-osama-sami)

