# CI/CD Pipeline with Jenkins and Docker

## **Objective**

This guide outlines the steps to create a CI/CD pipeline using Jenkins and Docker, based on a hands-on project.

---

## **Steps and Code**

### **1. Install Jenkins**

Follow the [Jenkins Installation Guide](https://www.jenkins.io/doc/book/installing/) to set up Jenkins on your system.

---

### **2. Create a Web Project with a Dockerfile**

- **Project Structure:**

  - `index.html`
  - `Dockerfile`

- **`index.html`:**

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Welcome</title>
  </head>
  <body>
    <h1>Welcome BDCC</h1>
  </body>
</html>
```

- **`Dockerfile`:**

```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
```

---

### **3. Initialize and Push the Project to GitHub**

Run the following commands in the project folder:

```bash
git init
git add *
git config --global user.email "your-email@example.com"
git commit -m "tp4 v1"
git remote add origin https://github.com/<your-username>/tp4.git
git push origin master
```

---

### **4. Create a Jenkins Freestyle Job (job1tp4)**

- **Steps to Configure:**
  - Clone the repository.
  - Build the Docker image:
    ```bash
    docker build -t your-dockerhub-username/tp4:latest .
    ```
  - Push the image to Docker Hub:
    ```bash
    docker login -u your-dockerhub-username -p your-password
    docker push your-dockerhub-username/tp4:latest
    ```

---

### **5. Add Docker Plugins to Jenkins**

Install Docker and Pipeline plugins via **Manage Jenkins > Plugins**.

---

### **6. Enable Automatic Triggering for Changes**

Modify `index.html` and push to GitHub:

```bash
git commit -am "tp4 v2"
git push origin master
```

Configure Jenkins to poll GitHub or use webhooks for automatic builds.

---

### **7. Create a CI/CD Pipeline Job (job2tp4)**

- **Pipeline Script:**

```groovy
pipeline {
    environment {
        registry = "your-dockerhub-username/tp4"
        registryCredential = 'dockerhub'
        dockerImage = ''
    }
    agent any
    stages {
        stage('Cloning Git') {
            steps {
                git 'https://github.com/<your-username>/tp4.git'
            }
        }
        stage('Building Image') {
            steps {
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Publishing Image') {
            steps {
                script {
                    docker.withRegistry('', registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Deploying Image') {
            steps {
                script {
                    sh "docker run -d -p 80:80 ${registry}:${BUILD_NUMBER}"
                }
            }
        }
    }
}
```

---

### **8. Add Testing to the Pipeline**

Include a testing stage:

```groovy
stage('Testing Image') {
    steps {
        script {
            echo "Tests passed"
        }
    }
}
```

---

### **9. Stage View and Iterations**

Push changes to the repository and monitor the Jenkins build pipeline in the **Stage View**.

---

### **10. Extended Pipeline with Deployment Stage**

Add a deployment stage to the pipeline:

```groovy
stage('Deploy Image') {
    steps {
        script {
            sh "docker run -d -p 80:80 ${registry}:${BUILD_NUMBER}"
        }
    }
}
```

---

This guide provides a complete framework to implement a CI/CD pipeline integrating Jenkins and Docker. Modify and expand based on your project requirements.

### Docker Build

![first step](/assets/image.png)

### Docker Push

![docker push](/assets/docker-push.png)

### Jenkins

![Jenkins](/assets/jenkins.png)
