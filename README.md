# GitOps-ArgoCD-JENKINS

Gitops uses git as single  source of truth  to deliver applications and   infrastructure

GitOps also tck changes too your cluster i.e version  control too


Actor---> Kubernetes manifests(**GiT**) -----> **ArgoCD** ----->**K8s**
     updates                           reads         deploy


Gitops principle 
1. Declarative
2. Verisoned and immutable
3.  Pulled automatically
4. Continuosly Reconciled


 further ArgoCD does not let to change cluster mannually you have to  push chnges in git repo to do that 
  it would deny tha changes or   trace them back to previous state 


     GIT----- connect to git andd get state------->**Repo Server**
               Application controller-----connect to K8S and get state---->Kubernetes


we can communicate with argocd using CLI  by API server

A. configure instance ,install  java runtime in it  because jenkins is java application
 
 sudo apt update
sudo apt install openjdk-17-jre

B. Proceed to   install and start jenkins server

curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins

C. jenkins server by default listen at port **8080**

ADMIN password is by default in this dir
 cat /var/lib/jenkins/secrets/initialAdminPassword

Reference  "d1d71c44e7cd455981def700000xxxx"


//Using Docker agent for  jenkns  pipeline is efficient
1.  it save  money as  we won't run irautal memor 
2. contianers are delteed after exeutig pipeline  therefore efficient
3. No unneccesary provision of resources or instances




 **Configure a Sonar Server locally**
System Requirements
Java 17+ (Oracle JDK, OpenJDK, or AdoptOpenJDK)
Hardware Recommendations:
   Minimum 2 GB RAM
   2 CPU cores
sudo apt update && sudo apt install unzip -y
adduser sonarqube
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.4.1.88267.zip
unzip *
chown -R sonarqube:sonarqube /opt/sonarqube
chmod -R 775 /opt/sonarqube
cd /opt/sonarqube/bin/linux-x86-64
./sonar.sh start
Hurray !! Now you can access the SonarQube Server on http://<ip-address>:9000


  in sonarqube  create token for jenkins to authenticat to sonarqube
  "squ_7717f10640c6217f09bc19a3af4e579xXXXXX"

   you can restart  jenkins  using   {jenkinsurl:8080}/restart


//TO switch user  
sudo su - username


run argocd  pods 
 than   acccess service by port forwarding and using instnce ip

 further  password can be seen by       exmaple-ccluster-argocd  secret 
" 3cE4bSBUhGXZ0pTAxxxxxxx"







 //Automating   github intergation with jenkins

 Part 1: Configure GitHub to Send the "Alarm" (Webhook)
 
Go to Your GitHub Repository.
Click on the Settings tab.
In the left menu, click on Webhooks.
Click the Add webhook button.

Now, you need to fill out the form:
Payload URL: 
http://<YOUR_JENKINS_PUBLIC_URL>/github-webhook/

Content type: Select application/json.\

Which events would you like to trigger this webhook? Keep the default, Just the push event. This is exactly what you want: the pipeline should run whenever someone pushes new code.

Click Add webhook.

After you add it, GitHub will send a test "ping" event to your Jenkins URL. If everything is configured correctly, you will see a green checkmark next to it in the webhook delivery log.

Part 2: Configure Jenkins to Listen for the "Alarm"

Install the GitHub Plugin: First, make sure you have the necessary plugin.
Go to Manage Jenkins -> Plugins -> Available plugins.
Search for and install the GitHub Integration plugin. (You likely already have it, but it's good to check).
Configure Your Jenkins Pipeline Job:
Go to your pipeline job's main page.
Click on Configure in the left menu.
Find the "Build Triggers" Section:
Scroll down to the Build Triggers section in the configuration page.
Check the box for GitHub hook trigger for GITScm polling.
This is the option that tells Jenkins: "Activate the special listening endpoint (/github-webhook/) for this job. Don't poll continuously; just wait for the signal from GitHub."
Save your configuration.
That's it! Your Jenkinsfile does not need to be changed. All your existing logic for building, testing, and updating manifests will work perfectly. The only thing that has changed is what starts the process.
