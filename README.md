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
<img width="1713" height="962" alt="image" src="https://github.com/user-attachments/assets/a97e1c40-4fa5-467a-a005-6e8e6ae54cc7" />


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
<img width="971" height="823" alt="image" src="https://github.com/user-attachments/assets/93fe2c1c-3660-4969-b61c-279e96321ab1" />

<img width="1877" height="837" alt="image" src="https://github.com/user-attachments/assets/4544f63b-9322-47fd-987d-8af38146e3b7" />

3. Implement Matrix-Based Security in Jenkins o Enable Matrix-based security. o Create roles with appropriate permissions (e.g., admin, developer). o Restrict anonymous access.
Step 1: Log in to Jenkins as Administrator

Open Jenkins in your browser.

Enter your administrator username and password.

From the dashboard, click Manage Jenkins.

Example URL:

```
http://YOUR_SERVER_IP:8080
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

***Administrator: Log in as adminuser. Confirm that Manage Jenkins and security settings are accessible.***
<img width="745" height="693" alt="image" src="https://github.com/user-attachments/assets/79c0b2ce-fe12-41b2-8359-58ded5f21108" />
<img width="1917" height="557" alt="image" src="https://github.com/user-attachments/assets/608d53b9-f262-4927-9fcb-379afc9ff0cb" />


***Developer: Log in as developer1. Confirm that jobs can be viewed and built, but global security settings cannot be changed.***
<img width="1716" height="910" alt="image" src="https://github.com/user-attachments/assets/806aa26b-c3e6-4fb9-8042-6f2dbc1caa83" />
<img width="1918" height="774" alt="image" src="https://github.com/user-attachments/assets/e521cfe0-1753-43fb-9a70-fc16c9aa1697" />


***Anonymous: Open Jenkins in a private/incognito window without logging in. Confirm that Jenkins does not allow access to protected pages.***
<img width="1681" height="968" alt="image" src="https://github.com/user-attachments/assets/2fc7175d-c238-4824-ad2d-a07c466a0c0b" />

4. Deploy Using NGINX with SSL o Use NGINX as a reverse proxy for the Node.js app. o Make the application accessible at: https://devlogin.nextastra.com using DuckDNS and Let's Encrypt SSL.
Step 1: Map the Domain on DuckDNS
Go to duckdns.org and log in.

In the subdomain field, add your domain/subdomain (e.g., devlogin or your assigned DuckDNS identifier).

Set the IP address to your server’s public IPv4 address.

If devlogin.nextastra.com is a custom domain, ensure a CNAME or A record is configured in your DNS provider pointing devlogin.nextastra.com directly to your DuckDNS domain or your server's public IP.

Verify resolution from your terminal:
```
ping devlogin.nextastra.com
```
Step 2: Open Firewall Ports
Ensure HTTP (80) and HTTPS (443) traffic is allowed through your server's firewall and cloud security groups:
```
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw reload
```
Step 3: Install NGINX and Certbot
Update packages and install NGINX along with the Certbot NGINX plugin:
```
sudo apt update
sudo apt install -y nginx certbot python3-certbot-nginx
```
Verify that NGINX is running:
```
sudo systemctl enable --now nginx
```
Step 4: Configure NGINX as a Reverse Proxy
Create a dedicated server block configuration for devlogin.nextastra.com:

Create a new configuration file:
```
sudo nano /etc/nginx/sites-available/devlogin.nextastra.com```
***Paste the following configuration (assuming the Node.js app runs locally on port 3000):***

```Nginx


server {
    listen 80;
    server_name devlogin.nextastra.com;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_http_version 1.1;

        # Enable WebSockets and maintain headers
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;

        # Forward real client IP addresses
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
***Enable the site by creating a symlink in sites-enabled:***

```
sudo ln -s /etc/nginx/sites-available/devlogin.nextastra.com /etc/nginx/sites-enabled/
```
***Test and reload NGINX:***
```
sudo nginx -t
sudo systemctl reload nginx
```
***Step 5: Obtain and Install the Let's Encrypt SSL Certificate***

Use Certbot to automatically fetch the SSL/TLS certificate and configure HTTPS redirection in NGINX:

```
sudo certbot --nginx -d devlogin.nextastra.com
```
***Follow the prompts:***

Provide your email address for renewal notices.

Agree to the terms of service.

Certbot will automatically verify ownership via the HTTP-01 challenge, retrieve the certificates, and update your /etc/nginx/sites-available/devlogin.nextastra.com file with the SSL configuration and automatic HTTP-to-HTTPS redirect.

***Step 6: Verify SSL Auto-Renewal***
Let's Encrypt certificates are valid for 90 days. Certbot installs a systemd timer for automatic renewal. Test the renewal process with a dry run:
```
sudo certbot renew --dry-run
```
***Step 7: Test the Deployment***
Ensure your Node.js application is running in the background (e.g., using PM2):
```
pm2 start app.js --name "node-app"
```
***Open your browser and navigate to:***

```
https://devlogin.nextastra.com
```
Check that the secure lock icon displays and that requests are proxied directly to your Node.js backend.

