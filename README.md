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
<img width="699" height="232" alt="image" src="https://github.com/user-attachments/assets/07e61b9b-5422-4bba-90d6-3a13db017591" />

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

```sudo systemctl enable --now jenkins```

Check the service status:

```sudo systemctl status jenkins```

Open this address in your browser, replacing the IP with your EC2 public IP:

```http://YOUR_EC2_PUBLIC_IP:8080```

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

3. Implement Matrix-Based Security in Jenkins o Enable Matrix-based security. o Create roles with appropriate permissions (e.g., admin, developer). o Restrict anonymous access.
Step 1: Log in to Jenkins as Administrator

Open Jenkins in your browser.

Enter your administrator username and password.

From the dashboard, click Manage Jenkins.

Example URL:

```http://YOUR_SERVER_IP:8080
```
Step 2: Enable Matrix-based Security

Open Manage Jenkins → Security or Manage Jenkins → Configure Global Security (the menu name depends on your Jenkins version).

Find the Authorization section.

Select Matrix-based security.

This option lets you assign individual permissions to specific users or groups.
Step 3: Configure the security matrix

In the permissions table, each row represents a user or group, and each checkbox grants a permission. Matrix-based security is provided by the Matrix Authorization Strategy plugin. 
Jenkins
+1

If the option is missing, go to Manage Jenkins → Plugins → Available plugins, search for Matrix Authorization Strategy, install it, and restart Jenkins if prompted.
Step 4: Create administrator and developer users

Jenkins' built-in matrix does not create named roles such as admin and developer. It grants permissions to individual users or groups. For this practical, create two users and assign permissions directly.

Go to Manage Jenkins → Users.

Click Create User.

Create an administrator account, for example adminuser.

Create a developer account, for example developer1.

Log in as the administrator account to configure permissions.

If your Jenkins version uses a different user-management screen, use the Create an account option from the login page, if available.

Step 5: Assign permissions

Return to Manage Jenkins → Security → Authorization → Matrix-based security.

Add adminuser and developer1 to the matrix if they are not already listed.
Step 6: Restrict anonymous access

In the matrix, find the row named anonymous.

Uncheck every permission in that row, including Overall → Read.

Do not grant anonymous users permission to view jobs, build projects, or configure Jenkins.

Keep the adminuser account's Overall → Administer permission enabled.

Ensure the developer has Overall → Read so they can access Jenkins after logging in.

This restricts unauthenticated access. Jenkins recommends avoiding significant permissions for anonymous users. 
Jenkins

Step 7: Save the configuration

Double-check that adminuser has Overall → Administer.

Confirm that developer1 has only the permissions needed for development.

Confirm that the anonymous row has no checked permissions.

Click Save.
Step 8: Verify the security configuration

Test the configuration using separate browser sessions.

Administrator: Log in as adminuser. Confirm that Manage Jenkins and security settings are accessible.

Developer: Log in as developer1. Confirm that jobs can be viewed and built, but global security settings cannot be changed.

Anonymous: Open Jenkins in a private/incognito window without logging in. Confirm that Jenkins does not allow access to protected pages.

