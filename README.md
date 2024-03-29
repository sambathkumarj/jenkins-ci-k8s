# jenkins-ci-k8s

# Microk8s:-

MicroK8s is the easiest and fastest way to get Kubernetes up and running.MicroK8s is the go-to platform for mission-critical workloads. Quickly spin nodes up in your CI/CD and reduce your production maintenance costs

MicroK8s architecture and OS compatibility allow you to deploy on COTS hardware and develop on any workstation.

# Reference link:-

https://ubuntu.com/tutorials/install-a-local-kubernetes-with-microk8s#1-overview

# Setup Microk8s :

# Install a local Kubernetes with MicroK8s :
# Deploying MicroK8s:
```
sudo snap install microk8s --classic
```
NOTE: You may need to configure your firewall to allow pod-to-pod and pod-to-internet communication:
```
sudo ufw allow in on cni0 && sudo ufw allow out on cni0
sudo ufw default allow routed
```
Enable addons:
```
microk8s enable dns 
microk8s enable dashboard
microk8s enable storage
```
NOTE : These addons can be disabled at anytime by running the microk8s disable,
With microk8s status you can see the list of available addons and the ones currently enabled.
# Accessing the Kubernetes dashboard:
NOTE: Now that we have enabled the dns and dashboard addons we can access the available dashboard. To do so we first check the deployment progress of our addons with microk8s kubectl get all --all-namespaces

#Kubernetes dashboard :
NOTE : EX :- As we see above the kubernetes-dashboard service in the kube-system namespace has a ClusterIP of 10.152.183.64and listens on TCP port 443. The ClusterIP is randomly assigned, so if you follow these steps on your host, make sure you check the IP adress you got. Point your browser to https://10.152.183.64:443 and you will see the kubernetes dashboard UI.
```
token=$(microk8s kubectl -n kube-system get secret | grep default-token | cut -d " " -f1)
microk8s kubectl -n kube-system describe secret $token
```
Here ,the Microk8s is successfully deployed.

# Jenkins

Jenkins is an open-source automation tool for Continuous Integration (CI) and Continuous Deployment (CD). It is a server-based system that runs in servlet containers like Apache Tomcat.

It is a Java-based open-source automation platform with plugins designed for continuous integration. It is used to create and test software projects continually, making it easier for developers and DevOps engineers to integrate changes to the project and for consumers to get a new build.

# Helm Charts

A Helm chart is a package that contains all the necessary resources to deploy an application to a Kubernetes cluster. This includes YAML configuration files for deployments, services, secrets, and config maps that define the desired state of your application.

It is a collection of files that describe a Kubernetes cluster’s resources and package them together as an application. They comprise three basic components: The chart — Chart. yaml defines the application metadata like name, version, dependencies,

# Adding the Jenkins chart repository
Jenkins Helm charts are provided from https://charts.jenkins.io. To make this chart repository available, run the following commands:
```
helm repo add jenkins https://charts.jenkins.io
helm repo update
```
# Deploying a simple Jenkins instance
To deploy a Jenkins instance with the default settings, run the command:
```
helm upgrade --install myjenkins jenkins/jenkins
```
The helm upgrade the command is typically used to upgrade an existing release. However, the --install argument ensures the release is created if it does not exist. This means helm upgrade --install creates and updates a release, removing the need to juggle installation and upgrade commands depending on whether or not the release exists.

The name of the release is myjenkins, and the final argument jenkins/jenkins defines the chart to be installed.

The output looks something like this:
```
$ helm upgrade --install myjenkins jenkins/jenkins
```
Release "myjenkins" does not exist. Installing it now.
NAME: myjenkins
LAST DEPLOYED: Tue Oct 19 08:13:11 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get your 'admin' user password by running:
  kubectl exec --namespace default -it svc/myjenkins -c jenkins -- /bin/cat /run/secrets/chart-admin-password && echo
2. Get the Jenkins URL to visit by running these commands in the same shell:
  echo http://127.0.0.1:8080
  kubectl --namespace default port-forward svc/myjenkins 8080:8080
3. Login with the password from step 1 and the username: admin
4. Configure security realm and authorization strategy
5. Use Jenkins Configuration as Code by specifying configScripts in your values.yaml file, see documentation: http:///configuration-as-code and examples: https://github.com/jenkinsci/configuration-as-code-plugin/tree/master/demos
For more information on running Jenkins on Kubernetes, visit:
https://cloud.google.com/solutions/jenkins-on-container-engine
For more information about Jenkins Configuration as Code, visit:
https://jenkins.io/projects/jcasc/
NOTE: Consider using a custom image with pre-installed plugins
The first command listed in the notes returns the password for the admin user:

$ kubectl exec --namespace default -it svc/myjenkins -c jenkins -- /bin/cat /run/secrets/chart-admin-password && echo
The second command listed in the notes establishes a tunnel to the service in the Kubernetes cluster.

In Kubernetes, a service is a resource that configures the cluster’s network to expose one or more pods. The default service type exposes pods via a private IP address. We tunnel into this private IP address to gain access to the Jenkins web UI.

A Kubernetes pod is a resource that hosts one or more containers. This means the Jenkins instance is running as a container inside a pod:
```
$ kubectl --namespace default port-forward svc/myjenkins 8080:8080
```
Forwarding from 127.0.0.1:8080 -> 8080
Forwarding from [::1]:8080 -> 8080
After the tunnel is established, open http://localhost:8080 on your local PC and you’ll be directed to the Jenkins instance in the Kubernetes cluster. Log in with the username admin and password returned by the first command.

Or else We can access it by editing Jenkin service Cluster IP into NodePort to expose the service to the outside the cluster. Example : http://<localhost IP>:nodeport .

You now have a functional, if basic, Jenkins instance running in Kubernetes.

