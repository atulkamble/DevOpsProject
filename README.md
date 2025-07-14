# DevOpsProject
Creating a project that integrates Ansible, Jenkins, Docker Desktop, Minikube, Maven, and Apache Tomcat involves several steps. Below is a detailed guide to set up a CI/CD pipeline using these tools.

### Prerequisites
1. **Install Ansible**: Follow the installation instructions from the [Ansible documentation](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html).
2. **Install Jenkins**: Download and install Jenkins from the [official website](https://www.jenkins.io/download/).
3. **Install Docker Desktop**: Get Docker Desktop from the [Docker website](https://www.docker.com/products/docker-desktop/).
4. **Install Minikube**: Install Minikube by following the [Minikube start guide](https://minikube.sigs.k8s.io/docs/start/).
5. **Install Maven**: Install Maven from the [Apache Maven site](https://maven.apache.org/install.html).
6. **Install Apache Tomcat**: Download and set up Apache Tomcat from the [Tomcat website](https://tomcat.apache.org/download-90.cgi).

### Project Structure
Let's create a sample Java project that will be built using Maven, packaged into a Docker container, deployed on Minikube, and managed via Ansible and Jenkins.

### Step-by-Step Guide

### Installation and Configurations
1) Launch and Connect | EC2 | t2 medium | amazon linux 2023
Region | ap-south-1

```
// update machine
sudo yum update -y
// install python
// install java
sudo yum install jre
java --version
// install maven
sudo yum install maven -y
sudo yum install python -y
// install git
sudo yum install git -y
// configure git
git config --global user.name "username"
git config --global user.email "email"
// clone git project repo
git clone https://github.com/atulkamble/DevOpsProject.git
cd DevOpsProject
pwd
ls
// install docker
sudo yum install docker
sudo usermod -aG docker $USER && newgrp docker
sudo docker login

sudo systemctl status docker.service
sudo systemctl start docker.service
sudo systemctl enable docker.service

// installation of kubectl
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.29.3/2024-04-19/bin/linux/amd64/kubectl
chmod +x ./kubectl
mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$HOME/bin:$PATH
echo 'export PATH=$HOME/bin:$PATH' >> ~/.bashrc
kubectl version --client

// install minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-latest.x86_64.rpm
sudo rpm -Uvh minikube-latest.x86_64.rpm


minikube start
minikube addons storage-provisioner
minikube addons enable storage-provisioner
minikube addons enable default-storageclass
```

// install jenkins
```
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
sudo yum install fontconfig java-17-openjdk
sudo yum install jenkins

#### 1. Create a Maven Project
Create a simple Java web application with Maven.

**pom.xml**:
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>simple-webapp</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>
    <build>
        <finalName>simple-webapp</finalName>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <version>3.3.1</version>
            </plugin>
        </plugins>
    </build>
</project>
```

**src/main/webapp/index.jsp**:
```jsp
<!DOCTYPE html>
<html>
<head>
    <title>Simple Web App</title>
</head>
<body>
    <h1>Hello, World!</h1>
</body>
</html>
```

Solution for mvn build error

The error message indicates that the Maven WAR plugin requires a `web.xml` file to be present in the `src/main/webapp/WEB-INF` directory, but it cannot find it. Here's how to solve this issue step by step:

### Step-by-Step Solution

1. **Create the `WEB-INF` Directory**:
   - Ensure that the `WEB-INF` directory exists under `src/main/webapp`:
     ```sh
     mkdir -p src/main/webapp/WEB-INF
     ```

2. **Create the `web.xml` File**:
   - Create a `web.xml` file in the `src/main/webapp/WEB-INF` directory. This file is required for a proper web application deployment descriptor.
     ```sh
     touch src/main/webapp/WEB-INF/web.xml
     ```

3. **Populate the `web.xml` File**:
   - Add the necessary content to the `web.xml` file. Here is a basic example of a `web.xml` configuration:
     ```xml
     <?xml version="1.0" encoding="UTF-8"?>
     <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
              version="3.1">
         <display-name>simple-webapp</display-name>
         <welcome-file-list>
             <welcome-file>index.jsp</welcome-file>
         </welcome-file-list>
     </web-app>
     ```

4. **Ensure `index.jsp` Exists**:
   - Make sure the `index.jsp` file exists in the `src/main/webapp` directory. You can create it as follows:
     ```sh
     touch src/main/webapp/index.jsp
     ```

   - Add content to the `index.jsp` file:
     ```jsp
     <html>
     <body>
         <h2>Hello, World!</h2>
     </body>
     </html>
     ```

5. **Re-run Maven Package Command**:
   - Now, run the Maven package command again to compile and package the WAR file:
     ```sh
     mvn package
     ```

### Complete Example Directory Structure

Ensure your directory structure looks like this:

```
simple-webapp
├── pom.xml
└── src
    └── main
        └── webapp
            ├── WEB-INF
            │   └── web.xml
            └── index.jsp
```

### `pom.xml` File

Your `pom.xml` should look like this:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>simple-webapp</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>
    <build>
        <finalName>simple-webapp</finalName>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <version>3.3.1</version>
            </plugin>
        </plugins>
    </build>
</project>
```

Run following commands 
```
mvn build
mvn compile
mvn test
mvn package
mvn verify
mvn install
```

### Summary

By ensuring the `web.xml` file exists in the correct directory and populating it with the required content, you can resolve the error and successfully package your Maven web application into a WAR file. Then, you can deploy the WAR file to a servlet container like Apache Tomcat.

#### 2. Dockerize the Application
Create a Dockerfile to containerize the web application.

**Dockerfile**:
```Dockerfile
FROM tomcat:9.0
COPY target/simple-webapp.war /usr/local/tomcat/webapps/
```

#### 3. Set Up Minikube
Start Minikube and create a Kubernetes deployment for the Dockerized application.

```sh
minikube start
```

Create a Kubernetes deployment and service.

**k8s-deployment.yml**:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: simple-webapp-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: simple-webapp
  template:
    metadata:
      labels:
        app: simple-webapp
    spec:
      containers:
      - name: simple-webapp
        image: simple-webapp:latest
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: simple-webapp-service
spec:
  type: NodePort
  selector:
    app: simple-webapp
  ports:
  - port: 8080
    targetPort: 8080
    nodePort: 30007
```

commands
```
touch Dockerfile
sudo nano Dockerfile
docker build -t atuljkamble/devopsproject .
sudo docker images
sudo docker push atuljkamble/devopsproject
touch k8s-deployment.yml
sudo nano k8s-deployment.yml
```

#### 4. Jenkins Setup
1. **Install Plugins**: Install necessary plugins like Docker, Maven, Kubernetes, and Ansible in Jenkins.
2. **Create a Jenkins Pipeline**: Create a Jenkins pipeline to automate the build, Dockerization, and deployment process.
```
java -jar jenkins.war --enable-future-java
```
**Jenkinsfile**:
```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/your-repo/simple-webapp.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("simple-webapp:latest")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                script {
                    sh 'kubectl apply -f k8s-deployment.yml'
                }
            }
        }
    }
}
```

#### 5. Ansible Setup
Ansible can be used to manage configurations and deployments.

**ansible/playbook.yml**:
```yaml
---
- name: Deploy simple-webapp on Kubernetes
  hosts: localhost
  tasks:
    - name: Apply Kubernetes deployment
      command: kubectl apply -f k8s-deployment.yml
```

**ansible/ansible.cfg**:
```ini
[defaults]
inventory = ./inventory
host_key_checking = False
```

**ansible/inventory**:
```ini
localhost ansible_connection=local
```

#### 6. Running the Project
- **Build the Maven Project**:
    ```sh
    mvn clean package
    ```

- **Build and Run Docker Image**:
    ```sh
    docker build -t simple-webapp:latest .
    docker run -d -p 8080:8080 simple-webapp:latest
    ```

- **Deploy to Minikube**:
    ```sh
    kubectl apply -f k8s-deployment.yml
    ```

- **Run Ansible Playbook**:
    ```sh
    ansible-playbook ansible/playbook.yml
    ```

- **Jenkins Pipeline**: Run the pipeline through Jenkins UI.

This setup integrates all the tools specified to create a CI/CD pipeline for a simple Java web application. Adjust configurations and scripts according to your specific requirements and environment.
