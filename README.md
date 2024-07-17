# Jenkins Pipeline for Java based application using Maven, SonarQube, Argo CD, Helm and Kubernetes

![Screenshot 2023-03-28 at 9 38 09 PM](https://user-images.githubusercontent.com/43399466/228301952-abc02ca2-9942-4a67-8293-f76647b6f9d8.png)

### Launch an Ubuntu(22.04) T2 Large Instance

Launch an AWS T2 large instance(with 2 vCPUs and 8 GiB of memory). Use the image as Ubuntu. You can create a new key pair or use an existing one. 

Enable below ports in the security group of the Instance,

* SSH: Port 22 (TCP) - Used for secure shell access.
* HTTP: Port 80 (TCP) - Used for unencrypted web traffic.
* HTTPS: Port 443 (TCP) - Used for encrypted web traffic.
* Jenkins: Port 8080 (TCP) - Default HTTP port for Jenkins web interface. 
* SonarQube: 9000
  
![image](https://github.com/user-attachments/assets/6a8b0b33-3f8b-44ac-867a-e72943592e48)

Now click on "Connect" button on "Instances" Dashboard. Then click on "SSH Client".

Here you need to have the created key-pair(.pem file) in the file directory of the your local machine that you launch your console inorder to ssh into EC2 instance which is attached with the same key-pair(.pem file).

![connect](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/04da2651-0421-480f-ac9a-6f2407894691)

![command](https://github.com/user-attachments/assets/1c9f7d39-8332-482a-a80c-8af558be8cc5)

**OR**

ssh into the EC2 instance using GUI of the MobaXtreme termminal like below. 

* Open MobaXterm from your desktop or the Start menu.
* Click the “Session” button, then select “SSH”.
* Enter the IP address or hostname of the server and specify username.
* Click on “Advanced SSH settings” tick the “Use private key” box and select the private key pair you used in your instance.

![image](https://github.com/RavDas/Netflix-Clone-Deployment/assets/86109995/c97cc211-5e64-4ada-bb3c-13f1c639163b)

Here are the step-by-step details to set up an end-to-end Jenkins pipeline for a Java application using SonarQube, Argo CD, Helm, and Kubernetes:

Prerequisites:

   -  Java application code hosted on a Git repository
   -   Jenkins server
   -  Kubernetes cluster
   -  Helm package manager
   -  Argo CD

Steps:

    1. Install the necessary Jenkins plugins:
       1.1 Git plugin
       1.2 Maven Integration plugin
       1.3 Pipeline plugin
       1.4 Kubernetes Continuous Deploy plugin

    2. Create a new Jenkins pipeline:
       2.1 In Jenkins, create a new pipeline job and configure it with the Git repository URL for the Java application.
       2.2 Add a Jenkinsfile to the Git repository to define the pipeline stages.

    3. Define the pipeline stages:
        Stage 1: Checkout the source code from Git.
        Stage 2: Build the Java application using Maven.
        Stage 3: Run unit tests using JUnit and Mockito.
        Stage 4: Run SonarQube analysis to check the code quality.
        Stage 5: Package the application into a JAR file.
        Stage 6: Deploy the application to a test environment using Helm.
        Stage 7: Run user acceptance tests on the deployed application.
        Stage 8: Promote the application to a production environment using Argo CD.

    4. Configure Jenkins pipeline stages:
        Stage 1: Use the Git plugin to check out the source code from the Git repository.
        Stage 2: Use the Maven Integration plugin to build the Java application.
        Stage 3: Use the JUnit and Mockito plugins to run unit tests.
        Stage 4: Use the SonarQube plugin to analyze the code quality of the Java application.
        Stage 5: Use the Maven Integration plugin to package the application into a JAR file.
        Stage 6: Use the Kubernetes Continuous Deploy plugin to deploy the application to a test environment using Helm.
        Stage 7: Use a testing framework like Selenium to run user acceptance tests on the deployed application.
        Stage 8: Use Argo CD to promote the application to a production environment.

####  Step 5. Set up Argo CD:
        Install Argo CD on the Kubernetes cluster.
        Set up a Git repository for Argo CD to track the changes in the Helm charts and Kubernetes manifests.
        Create a Helm chart for the Java application that includes the Kubernetes manifests and Helm values.
        Add the Helm chart to the Git repository that Argo CD is tracking.


    6. Configure Jenkins pipeline to integrate with Argo CD:
       6.1 Add the Argo CD API token to Jenkins credentials.
       6.2 Update the Jenkins pipeline to include the Argo CD deployment stage.

Set Up ArgoCD
ArgoCD manages the continuous deployment segment of CI/CD pipelines, automating deployments to Kubernetes.

You can either have local deployment using Minikube or Cloud Deployment using Amazon EKS.

Prerequisites:

Ensure VirtualBox or Hyper-V is installed on your Windows machine for virtualization, as required by Minikube.
Install Minikube:

Download and install Minikube following the instructions specific to your OS from the Minikube official documentation.
Start your local Kubernetes cluster.
minikube start
Install Kubectl:

Download the latest version of kubectl from the official Kubernetes release page.
Add kubectl to your PATH to run it from anywhere in your command prompt.
Install ArgoCD Operator
You can install Argo CD on Kubernetes using the Argo CD Operator which automates the deployment and management of Argo CD instances.

Go to the official Operator Hub page at OperatorHub.io.
Use the search bar on the Operator Hub website to search for “Argo CD” and click “Install”.
Run the Commands the following commands:
#Install Operator Lifecycle Manager (OLM), a tool to help manage the Operators running on your cluster.

$ curl -sL https://github.com/operator-framework/operator-lifecycle-manager/releases/download/v0.27.0/install.sh | bash -s v0.27.0
#Install the AgroCD Operator
kubectl create -f https://operatorhub.io/install/argocd-operator.yaml
This Operator will be installed in the “operators” namespace and will be usable from all namespaces in the cluster.
# watch your operator come up 
$ kubectl get csv -n operators


Set Up ArgoCD Controller
Navigate to OperatorHub.io.
In the “Argo CD” Operator scroll down to “Operator Documentation”.
Click on “Usage” and then “Basics”.
Copy the YAML configuration provided. This YAML is used to deploy Argo CD in your Kubernetes cluster.


Create a new file named vim argocd-basic.yml with the following content to define your Argo CD instance:
apiVersion: argoproj.io/v1alpha1
kind: ArgoCD
metadata:
  name: example-argocd
  labels:
    example: basic
spec: {}
Apply the Configuration.
kubectl apply -f argocd-basic.yml

Set Up ArgoCD UI
To access the Argo CD server UI via the browser, you need to change the service type from ‘ClusterIP’ to ‘NodePort’.
kubectl get svc
Minikube can generate a URL that provides direct access to the Argo CD server through a browser.
minikube service argocd-server --url

Copy the URL displayed from the previous command into your browser to access the Argo CD UI.

The default username is ‘admin’. To get the admin password, you need to extract it from Kubernetes secrets:
kubectl get secret
Edit the “example-argocd-cluster” secret and copy the admin password.
kubectl edit secret example-argocd-cluster
K8s secrets are base 64 encrypted so to decode it use this command.
echo <encoded password here>= | base64 -d
Use the username ‘admin’ and the password retrieved in the previous step to log into the Argo CD UI.

Deployment with Argo CD
In the Argo CD UI, click on “Create Application”.
Fill in the required information for the application:
Application Name: Enter a descriptive name for your application.

Project Name: Specify the project to which the application belongs.

Sync: Choose “Automatic” for automatic synchronization.

Repository URL: Enter the URL of your Git repository containing the application code.

Path: Specify the path to the deployment files within the repository.

Destination: Enter the URL of your Kubernetes cluster (e.g., https://kubernetes.default.svc).

Namespace: Specify the Kubernetes namespace where the application will be deployed.

After providing all the necessary information, click on “Create”.
Argo CD will automatically create the application on your Kubernetes cluster based on the provided configuration.


    7. Run the Jenkins pipeline:
       7.1 Trigger the Jenkins pipeline to start the CI/CD process for the Java application.
       7.2 Monitor the pipeline stages and fix any issues that arise.

This end-to-end Jenkins pipeline will automate the entire CI/CD process for a Java application, from code checkout to production deployment, using popular tools like SonarQube, Argo CD, Helm, and Kubernetes.
