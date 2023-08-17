# Install docker and jenkins
- sudo apt-get update
- sudo apt-get install openjdk-11-jdk -y
- java -version

- curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
- echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
- sudo apt-get update
- sudo apt-get install jenkins -y

- sudo systemctl start jenkins.service
- sudo systemctl enable jenkins.service
- sudo systemctl status jenkins.service
- cat /var/lib/jenkins/secrets/initialAdminPassword
------------------------------------------------------------
# Install docker
```
sudo apt-get update
sudo apt install docker.io -y
sudo usermod -aG docker jenkins
sudo systemctl start docker
sudo systemctl enable docker
sudo systemctl start jenkins
sudo systemctl enable jenkins
```
------------------------------------------------------------
# install aws cli
```
sudo apt-get update
sudo apt-get install awscli -y

aws configure   
```
------------------------------------------------------------
# Create IAM role with administrator access
## this is useful for eks   
```
- create a role
- aws service
- ec2
- administrator access
- attach policy
- save
- copy the role arn
```
------------------------------------------------------------
# Create a new user
```
- create a user
- programmatic access
- attach existing policies directly
- administrator access
- create user
- copy the access key id and secret access key
```
------------------------------------------------------------
# Install eksctl
```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```
------------------------------------------------------------
# Install kubectl
```
sudo curl --silent --location -o /usr/local/bin/kubectl \
  https://amazon-eks.s3.us-west-2.amazonaws.com/1.21.2/2021-07-05/bin/linux/amd64/kubectl
sudo chmod +x /usr/local/bin/kubectl
```
------------------------------------------------------------
# switch to jenkins user and create a cluster using eksctl
```
sudo su - jenkins
eksctl create cluster \
--name sam-eks-test \
--region ap-south-1 \
--nodegroup-name my-nodes \
--node-type t3.small \
--managed --nodes 2

eksctl get cluster
- check the cluster is created or not
- Update the kubecofig file
- aws eks update-kubeconfig --name sam-eks-test --region ap-south-1
```
------------------------------------------------------------
# Now install the plugins in jenkins 
```
Docker pipeline
Docker API plugin
Docker commons plugin
Docker pipeline
Docker plugin

kubernetes cli
kubernetes client api
Add maven plugin to Global tool configuration

Nexus platform
Nexus artifact uploader
pipeline utility steps
pipeline maven integration

Cloud Statistics	 Success
Authentication Tokens API	 Success
Docker Commons	 Success
Apache HttpComponents Client 5.x API	 Success
Docker API	 Success
Docker	 Success
Docker Commons	 Success
Docker Pipeline	 Success
Docker API	 Success
CloudBees Docker Build and Publish	 Success
Loading plugin extensions	 Success
Kubernetes Client API	 Success
Kubernetes Credentials	 Success
Kubernetes CLI	 Success
Kubernetes Client API	 Success
Loading plugin extensions	 Success
Commons HttpClient 3.x API	 Success
Nexus Artifact Uploader	 Success
Nexus Platform	 Success
Loading plugin extensions	 Success
SonarQube Scanner	 Success
Oracle Java SE Development Kit Installer	 Success
Sonar Quality Gates	 Success
Command Agent Launcher	 Success
Quality Gates	 Pending
Loading plugin extensions	 Pending


list of pliugin needs to be installed
```
------------------------------------------------------------
# Install Maven
```
 Global tool configuration
  Maven
  Name: maven3
  Install automatically
  Save
```
------------------------------------------------------------
# Install SonarQube
```
Global tool configuration
  SonarScanner for MSBuild
  Name: Sonar
  Version: .Net fwk:4.6
  Install automatically
  

  SonarQube Scanner
  Name: sonar
  Version: 4.7.0.2472
  Install automatically
  Save
```
------------------------------------------------------------
# Credetials for sonarqube, docker, docker secret
```
- for eks_cluste
  - copy the content of the file from /var/lib/jenkins/.kube/config
  - paste that in some extenal file explorer
  - kind: secret file
  - file: upload

  - id: eks_cluster
  - description: eks cluster
  - save

- for sonarqube
  - administrator
  - security
  - users
  - generate token
  - name: sonarqube
  - generate
- for dockerhub
  - username and password
- for docker secret
  - only dockerhub password
- for nexus
  - username and password
  - admin
  - <password>
```
------------------------------------------------------------
# Configure sonar,nexus,dockerhub credentials


------------------------------------------------------------
# Creating a user in nexus
```
- settings
- users
- create local user
- fill the details
- roles: nx-admin
- save
```
------------------------------------------------------------
# Creating a repository in nexus
```
- repositories
- create repository
- maven2(hosted)
- Version policy: mixed
- Hosted: allow redeploy
- create repository
```
------------------------------------------------------------
# In Sonarqube Dashboard
```
--> Administration
   ---> configuration
         ---> Webhooks
            ---> Create Webhook
               ---> Name: jenkins
               ---> URL: http://<jenkins_ip>:8080/sonarqube-webhook/
               ---> Save


---> In sonarqube dashboard
    ---> Quality gates
      ---> create -> name and save
         ---> unlock editing
        ---> Add conditions
            ---> Bugs less than 2 & coverage less than 80
            ---> it might give some errors review it
            ---> Make it default


    ---> projects
        ---> create project (Manually)
        ---> give project display and set
        ---> give a name & generate token & save it 
        ---> locally
        ---> Run analysis select maven
        ---> copy code and generate in run analysis for maven
```
