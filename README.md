# test-
A 2 Tier Deployment using a loadbalancer and an autoscalled host on Virtualbox

The process will involve installing an ubuntu 16.04 instance in Virtualbox. We will use local kubernetes installed in this ubuntu instance. In order to achieve this installation we will proceed with installing docker, then minikube which will deploy our php script as docker containers, we will also run an NGINX container which will serve as the load-balancer.


1. Install docker on the ubuntu 16.04 instance in virtualbox: This will mange autoscalling of the orchestration of the deployment and also autoscalling of the host.
We will also need to ensure we have root access to the VM, Perferably we will be using ubuntu 16.04

#Download docker installation script

$ curl -fsSL https://get.docker.com -o get-docker.sh
#install docker using the docker script downloaded

$ sudo chmod +x get-docker.sh
$ sudo sh get-docker.sh


2. After Installation of the docker image we will need to add the user into the group
#Run the following command 

$ sudo groupadd docker
$ sudo usermod -aG docker $(whoami)
$ sudo reboot

3 Confirm docker is installed 
$ docker -v

4 Once docker has been installed and confirmed we will need to install minikube to use the virtualbox
#install kubectl
$ curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.9.0/bin/linux/amd64/kubectl

#make kubectl executable
$ sudo chmod +x ./kubectl

#Copy executable to be accessible from anywhere with the terminal
$ sudo mv ./kubectl /usr/local/bin/kubectl

5 Install Minikube, make it executable and accessible from anywhere
$ curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.24.1/minikube-linux-amd64 &&    chmod +x minikube && sudo mv minikube /usr/local/bin/

#confirm installation 
$ minikube version

#start minikube
$ sudo minikube start -vm-driver=none
 
#since root previledges are required to use minikube, the none driver kubectl config and credentials will be root owned and will appear in the root home directory
#to resolve this we will need to run the following commands
$ echo 'export CHANGE_MINIKUBE_NONE_USER=true' >> ~/.bashrc

#In order to enable custom metrics collection in minikube, Heapster has to be enabled.

$ minikube addons start heapster
$ minikube addons enable metrics-server

#This enables cluster-wide collection of pods' CPU and memory usage. We will still need to enable custom #metrics on minikube

$ sudo minikube start --etra-config kubelet.EnableCustomMetrics=true 

#confirm everything works properly
$ kubectl cluster-info
$ minikube ssh ps aux | grep EnableCustomMetreics

6 Importing the local docker images into kubernetes (this command makes our terminal interact with docker system within the cluster)
eval $(minikube docker-env)

7 Creating the deployment

$ kubectl create -f test_deployment.yaml

8 Expose the deployment through a service 

$ kubectl expose deployment test --port=80 --target-port=8080

9 Create the horizontalPodAutoscaller

$kubectl create -f test_autoscaller.yaml


10 Accessing Minikube 
#view the dashboard on minikube, this will activate the dashboard addon on minikube
minikube dashboard 



 

