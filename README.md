```
requirement:
- Jenkins Server
- Application Server with Docker Installed
```
* install docker on jenkins & application server (access to both server via ssh)
```bash
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF
sudo apt update
```
```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
```bash
docker --version
```
* install ansible & grant user jenkins to run docker
```
ssh root@jenkins_server
```
```bash
apt install python3-pip
```
```bash
pip3 install ansible --break-system-packages
```
```bash
usermod -aG docker jenkins
```
```bash
systemctl restart jenkins 
```
* generate ssh key from jenkins server
```bash
ssh root@jenkins_server
```
```bash
sudo su jenkins
```
```bash
ssh-keygen
```
```bash
type ENTER
```
```bash
type ENTER
```
```bash
cat ~/.ssh/id_rsa.pub or cat .ssh/id_ed25519.pub
```
* Add jenkins ssh key to application server 
```bash
ssh root@application_server
```
```bash
nano .ssh/authorized_keys
```
then paste your ssh key that generate from jenkins server to this file 
```bash
CTRL +X & Y & ENTER
```
* Test SSH from jenkins server to application server (make sure you switch to jenkins user)
```bash
ssh root@application_server
```
write ```Y``` + ```ENTER```

* Steps to Create Secret Text Credentials in Jenkins:

Navigate to Credentials

From the Jenkins dashboard, click on "Manage Jenkins"
Click on "Manage Credentials"


Select the Credential Store

Click on the domain where you want to add the credential (usually "Global credentials (unrestricted)")
Or click on "System" → "Global credentials (unrestricted)"


Add New Credentials

Click "Add Credentials" on the left side


Configure the Secret Text Credential

Kind: Select "Secret text" from the dropdown
Scope: Choose "Global" (accessible from any job) or "System" (only for system-level operations)
Secret: Enter your Docker Hub password/token here
ID: Enter docker_hub_password (this is the identifier you'll use to reference this credential)
Description: (Optional) Add a description like "Docker Hub Password" to help identify it later

Save

Click "OK" or "Create" to save the credential

* Install build user var public on jenkins
Part 1: Installing the Build User Vars Plugin
Method 1: Through Jenkins UI (Recommended)

Navigate to Plugin Manager

From Jenkins dashboard, click "Manage Jenkins"
Click "Manage Plugins" (or "Plugins" in newer Jenkins versions)


Search for the Plugin

Go to the "Available" tab (or "Available plugins")
In the search box, type: Build User Vars Plugin
Alternatively, search for: user build vars


Install the Plugin

Check the box next to "Build User Vars Plugin"
Click "Install without restart" or "Download now and install after restart"
Wait for the installation to complete


Restart Jenkins (if required)

If prompted, restart Jenkins to activate the plugin
You can do this by visiting: http://your-jenkins-url/restart

Part 2: Enable Build User Vars in System Configuration
For Freestyle Jobs:

Job Configuration

Open your Jenkins job
Click "Configure"


Enable Build User Vars

In the "Build Environment" section
Check the box: "Set jenkins user build variables"
Save the configuration

* Update code on jenkins file
File ```jenkins-ansible-cicd/ansible.jenkinsfile```

```bash
environment {
        docker_hub_password = credentials('docker_hub_password')
        docker_repo_url = "sothy/employee-api-demo"
        project_name = "employee-api"
        git_repo = "https://github.com/sothylorn/employee-api.git"
        app_url = "https://employee-api.sothy.site"
        Telegram_Token="8567963411:AAF6c1uoaVPazm-WY6VkY-7yuG7WYRtDrRE"
        Telegram_ChatID="-5196312295"
}
```
==> Please update all values to your application information 
File ```jenkins-ansible-cicd/rollback.jenkinsfile```
```bash
environment {
        docker_hub_password = credentials('docker_hub_password')
        docker_repo_url = "sothy/employee-api-demo"
        project_name = "employee-api"
        git_repo = "https://github.com/sothylorn/employee-api.git"
        app_url = "https://employee-api.sothy.site"
        Telegram_Token="8567963411:AAF6c1uoaVPazm-WY6VkY-7yuG7WYRtDrRE"
        Telegram_ChatID="-5196312295"
}
```
==> Please update all values to your application information

File ```jenkins-ansible-cicd/ansible/hosts```
```bash
localhost ansible_connection=local

[employee]
employee-api-dev ansible_host=152.42.214.43 ansible_ssh_user=root
employee-api-uat ansible_host=152.42.214.41 ansible_ssh_user=root
employee-api-prod ansible_host=152.42.214.44 ansible_ssh_user=root
[all:vars]
ansible_python_interpreter=/usr/bin/python3
```
==> Please update all values to your application server ip address


