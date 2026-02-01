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




