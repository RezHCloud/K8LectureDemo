# Minikube Demo 

## Intro
This is an instructional page on how to carry out the demo that was/will be on show during the Kubernetes webinar. This demo looks at some of the concepts learnt over the lecture as well as acting as your first interaction with kubernetes infrastructure. The Demo can be reused to spin up and test more advanced concepts with kubectl once you've learnt more.

## Pre-requisites 
This demo is carried out within AWS - this is to eliminate the complexities of setting up (and later - removing) minikube within your own device. 

### Infrastructure needed
You can use your own pipelines to deploy this via code or click ops it using the management console. The main purpose of this demo is to get hands on with some of the content of the lecture therefore its not mandatory to provision through an IaC pipeline. 

#### EC2 Instance (T2/T3 Medium or Higher)
This demo has been tested with 2 replicas within a deployment. If wanting more replicas, go higher but there will be increased costs associated. 2 replicas are plenty for Demo purposes. 

#### EC2 Security Group
This security group needs the 2 following inbound ports open: 

##### SSH (port 22) to AWS's Instance connect
We will be using instance connect to SSH into the box rather than using a desktop app for simplicity. You are more than welcome to use your own SSH terminal if you are comfortable setting it up. 

##### Custom TCP (port 3000) to 0.0.0.0/0
We will be connecting to the application through the open internet using port 3000. You can restrict the IP range to your own if you desire. 

## Installing all required software 
Once the instance has been provisioned,  Click on the connect button for the instance in the EC2 console. Please complete the following:
1. sudo apt update 
2. sudo apt -y install docker.io
3. sudo su -
4. curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl && chmod +x ./kubectl && sudo mv ./kubectl /usr/local/bin/kubectl
5. curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
6. apt install conntrack
7. su ubuntu
8. sudo usermod -aG docker $USER && newgrp docker
9. minikube start --driver=docker

This is the setup now complete! 

## KubeCTL time! 
So now you have Minikube and KubeCTL installed and set up, you can essentially do what you want with this to learn Kubernetes concepts the hands on way. There are plenty of resources online where you can learn and experiment with K8s safely. If you are intending to do your own experimentation and hands on, this is where the guide ends for you. No clean up is needed in the instance itself once you are done - simply terminate the instance in your console! 

If you are using this guide to replicate the demo shown in the lecture - start here. We will be deploying a simple application which will return the name of the pod that served the request. 

### Demo start here

1. Type in the following command "nano echo-server.yaml"
This will create a new file called echo-server.yaml in a text editor called nano. 

2. copy the kubernetes configuration in the DemoDeployment.yaml file in the repo. This is a deployment we will be deploying. Look through the config, make sure you understand everything (Except the env variables, they are simply variables we are passing into the container.)

3. Ctrl + S saves the config file, Ctrl + X closes the page.Make sure you do it in that order

4. type in command "kubectl apply -f echo-server.yaml"
This deploys the yaml config you've created.

Now you have a deployment up and running! 


kubectl get pods - this command shows you all pods that are currently running


kubectl get deployment - this command shows tou all deployments that are running 


There are plenty of commands here you can mess around with to get more information about the resource. 

5. type in command "kubectl expose deployment echo-server --type=NodePort --port=80 --name=echo-server-service"
This creates a service for the deployment. It essentially opens up the application on port 80 of the container.

6. To get the url of the deployment, type in "minikube service echo-server-service --url"
This should return an local URL address. 

7. type in "curl XXXXXX" - replace the Xs with the url from the last step 
It should return the webpage in a HTML format - this is how you know the service is functioning correctly 

8. Type in "kubectl port-forward svc/echo-server-service 3000:80 --address 0.0.0.0 &" 
This sets up port forwarding which is required to forward the incoming request on port 3000 to the correct port (port 80) on the container. 
To reach the server, you need to use the servers public IP address which can be found on the instances page on the AWS management console followed by the port 3000. (For example http://1.2.3.4:3000)

You can now view the page on a web browser and that pretty much the demo done! 

### Clean up 
No clean up is needed within the instance - simply terminate the instance in the AWS EC2 console. 

