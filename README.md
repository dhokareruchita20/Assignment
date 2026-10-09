1. Clone and Validate Application
Clone the provided Node.js Git repository.
Validate the application by installing dependencies and confirming it runs properly.
## Clone the Repository

```bash
git clone https://github.com/dhokareruchita20/nodejs-app.git
cd nodejs-app
```

## Validate the Application

```bash
npm test
npm start
```

## Test the Application

```bash
curl http://localhost:3000
```

**Expected Output:**

```text
Node.js Application is Running Successfully!
```
2. CI/CD Pipeline with Jenkins o Install Jenkins on a Linux server. o Create a Jenkins job or pipeline that:  Pulls code from the Git repository  Installs dependencies  Runs tests (if available)  Builds and deploys the application 
**step 2 ⚡ Installation Steps**

### 1️⃣ Install Java
```bash
sudo apt update
sudo apt install openjdk-17-jdk
java -version
```

### 2️⃣ Install Jenkins
```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```
Start Jenkins and enable it to start automatically after reboot:

sudo systemctl enable --now jenkins

Check the service status:

sudo systemctl status jenkins
Open this address in your browser, replacing the IP with your EC2 public IP:

http://YOUR_EC2_PUBLIC_IP:8080
**Step 2: Update the Jenkins pipeline**

In Jenkins, open your job → Configure → Pipeline → Script.

Use this code and replace the GitHub URL with your actual URL.
```
pipeline {
    agent any

    stages {
        stage('Pull Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/YOUR_USERNAME/nodejs.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                    if node -e "process.exit(require('./package.json').scripts?.test ? 0 : 1)"; then
                        npm test
                    else
                        echo "No test script found. Skipping tests."
                    fi
                '''
            }
        }

        stage('Build') {
            steps {
                sh '''
                    if node -e "process.exit(require('./package.json').scripts?.build ? 0 : 1)"; then
                        npm run build
                    else
                        echo "No build script found. Skipping build."
                    fi
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh 'echo "Code pulled, dependencies installed, and available tests/build completed."'
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check Console Output.'
        }
    }
}
```
This version verifies the CI steps; the Deploy stage is only a placeholder, not an actual application deployment.

**Step 3: Run the pipeline**

Click Save.

Click Build Now.

Open the build number.

Click Console Output.
