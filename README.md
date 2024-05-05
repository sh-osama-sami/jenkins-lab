# Jenkins Node.js Pipeline

This repository contains a Jenkins pipeline script for hosting a simple website pulled from a private Git repository on an AWS EC2 instance using Docker. The pipeline automates the process of pulling the code, building a Docker image, and running the Docker container on a remote EC2 instance.

## Prerequisites

- Jenkins installed with Docker image:
- ```bash
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

### 4. Scripted Pipeline

- Create a scripted pipeline in Jenkins GUI for your own repo to execute "ls":
- Upload a scripted file to the private repo
- Use the **Pipeline from Git** option in Jenkins to locate the scripted file in the repo

### 5. Multibranch Pipeline

- Create the same with Jenkinsfile in your branches as a multibranch pipeline:
- Include a Jenkinsfile in each branch of your repository with the pipeline configuration
- Jenkins will automatically detect and create multibranch pipelines for each branch

### 6. Configure Jenkins Image to Run Docker Commands

- Configure Jenkins image to run Docker commands on your host Docker daemon:
- Add the Jenkins user to the Docker group on the host machine
- Mount the Docker socket into the Jenkins container or use Docker Remote API

### 7. CI/CD for Repository

- Create CI/CD for this repository: [jenkins_nodejs_example](https://github.com/mahmoud254/jenkins_nodejs_example.git)
- Configure a Jenkins pipeline to pull from this repository and execute build and deployment steps

## Docker Slave Configuration

### 1. Dockerfile for Jenkins Slave

- Create a Dockerfile to build an image for Jenkins slave:
- Include necessary dependencies for running Jenkins jobs
- Configure SSH server if required

### 2. Jenkins Slave Container

- Create a container from the Jenkins slave image and configure SSH:
- Ensure SSH access is properly configured for communication with the Jenkins master

### 3. Create Jenkins Node

- From the Jenkins master, create a new node with the slave container:
- Configure the node to connect to the Jenkins master using SSH
- Set up any necessary environment variables or tools required for the node

## AWS Slave Configuration

### 4. Create AWS Instance

- Create an AWS EC2 instance to be used as a Jenkins slave:
- Ensure the instance meets the requirements for running Jenkins jobs
- Note down the public IP address for accessing the instance

### 5. Ansible Configuration

- Use Ansible to configure the EC2 instance to be a Jenkins slave:
- Install necessary dependencies and tools required for running Jenkins jobs
- Set up SSH access and connection between the Jenkins master and the AWS slave

### 6. Run Pipeline on AWS Slave

- Run the same pipeline on the AWS slave:
- Configure the Jenkins pipeline to use the AWS slave node for executing jobs
- Verify that the pipeline runs successfully on the AWS slave

### 7. Access Application

- Access the application using the EC2 public IP:
- Once the pipeline is successfully executed, access the deployed application using the public IP address of the AWS EC2 instance

## Contributors

- [Your Name](https://github.com/yourusername)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
