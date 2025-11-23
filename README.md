# kubernetess
Docker: container platform for running and managing conatiners.  
Kubernetees: container orechestration platform.  
# Why kubernetees?  
- prb 1 : (**Single host nature of your container -limitation**) - if there are 100 container under Docker (one VM). If 1st cont1 consuming lot of resources. the 99th or other container is dead or not even started. 
  This is decided by kernel.  
- prb 2: (**Auto healing**) in kubernetess. If container is dieing, docker itself will not bring back.
    Here comes kubernetess
- prb 3: (**Auto scaling**) - if sudden newtork traffic increases,  we need load balancers whether manually or automatically increase conatainers( Not available in DOcker.)
- prb 4: (**enterprise level**) - Docker doesn't support enterprise level of standards.
  ## Kubernetess:
  - Sol 1: Kubernetess is a cluster, Group of nodes (master/ node architecture).
  - Sol 2: kubernetess controls and fix the damage by replacing failing pods before they go down.  when One pod/container goes down.
    Using autohealing , even before pod goes down, whenever apiserver recieves a signal container is going down it will rollout a new pod is created.  
  - Sol 3: Kubernetess solves network traffic by increasing replica set in yaml (1 to 10) or HPA(Horizontal Pod Auto scaling) control when load reached threshold.
  - Sol 4: load balancing support, firewall support by CNCF . Lots of enhancement , developimg integration support for different tools, load balancer, Prometheus monitoring tools

# k8s Architecture
  in Docker - container runtime -> DOckershim resposible for conatiner running.  
  In kuburnetess 
## Worker node: (Data plane) - actually executing the actions
  Container runtime actullay run container- supports multi container runtime -> Docker shim, container-d, crio-o.  
  Kubelet - responsible for creation of pods. Ensures POD is always in the running state. If anything goes wrong will inform one of the componenests called API server in control panel of master.
  kube proxy: responsible for generating IP adresses, networing (uses IP table for networking configuration) and default load balanicing capability..  
## Master node : (Control plane) - actually controlling the actions
  API server - responsible for exposing the kubernetess to the outer world. core component (Basically takes requests from external world). (Who decides the information)
  scheduler - responsible for scheduking the pods or kubernetess resources.  (who acts on the information).. 
  etcd - key value store , contains backup of cluser related info's
  controller manager - ensures controllers like replica set are running
  cloud controllern manager - like terraform
![image](https://github.com/user-attachments/assets/601a52b3-c81f-49c9-8254-6bd02f1f712d)

# Difference between Minikube and Kubectl
kubectl: command line tool for kubernetess, that we can directly interact with kubernetess clusters.  
minikube: command line tool used for creating kubernetess cluster

### Below are Development environments
- Minikube
- k3s
- kind
- k3d
- Micro k8s
### have you used kubenetess distribution in prduction. If you have used, any kuberenetess distribution. did you manage installation and upgrade?
kubernetess: opensource container orchestration platform. But there are certain distribution provides support and build for user experience to its customer. 
  such as Openshift, rancher, tanzu, EKS, AKS, GKE. 
  These are used in production. 
### Difference between Kuberenetess and EKS
If On top of EC2 install Kuberenetess and use it. if there is any misconfiguration or we will not get any support.   
On the other hand if we use distributed supports providing like EKS, management and scalability handled by platform like AWS.   
Distribution provides additional  wrapper, plugin and command line tools. But at the end of day solution is same.

# KOPS  (Kubernetess Operations)
KOPS is one of the most widely used tools for installing kubernetess.  
lifecycle of kubernetess is managed by KOPS.  
lifecycle:  Installation, upgradation, configuration and deletion are part of lifecycle of Kubernetes cluster.  

### KOPS installation for production use
1. depedencies for installing kubernetess
Python3
AWS CLI
kubectl
2. Install KOPS..
```curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
  chmod +x kops-linux-amd64
  sudo mv kops-linux-amd64 /usr/local/bin/kops
```
4. Set up AWS CLI configuration on your EC2 Instance or Laptop.
Run `aws configure`
5. Kubernetes Cluster Installation  
   1. Create S3 bucket for storing the KOPS objects.   
      aws s3api create-bucket --bucket kops-thi-storage --region us-east-1
   2. create the cluster
      kops create cluster --name=demok8scluster.k8s.local --state=s3://kops-thi-storage --zones=us-east-1a --node-count=1 --node-size=t2.micro --master-size=t2.micro  --master-volume-size=8 --node-volume-size=8
6. Build the cluster
   kops update cluster demok8scluster.k8s.local --yes --state=s3://kops-thi-storage
## Difference between KOPS and Minikube
Both KOPS and minikube is a tool that used for kubernetess cluster management. But used in different cases.   
KOPS: is a tool designed for deploying across multiple nodes and managing *production gradde kubernetess* clusters.  
Minikube is a tool designed for *single node kubernetess cluster* on your local machine.  

# kubernetess Pods
![image](https://github.com/user-attachments/assets/68b5e0a1-7fba-4a59-a46c-5aec3273f5ce)

- pod is a wrapper of typically containing one or more containers that share resources like networking and storage.
- In kubernetess instaed of container we deploy pod
  Advantages of pod:
  - if there is 2 or multiple containers, pod allows you to shared network, shared storage
`access the conatiner using cluster IP address generated by kube proxy` 

# Deploy your first APP in kubernetess
## prerequisites
1. install kubectl
2. install minikube
### kubectl installation
```
curl.exe -k -LO "https://dl.k8s.io/release/v1.32.0/bin/windows/amd64/kubectl.exe"
kubectl version
```

### minikube installation
open powershell as a admin
```
New-Item -Path 'c:\' -Name 'minikube' -ItemType Directory -Force
Invoke-WebRequest -OutFile 'c:\minikube\minikube.exe' -Uri 'https://github.com/kubernetes/minikube/releases/latest/download/minikube-windows-amd64.exe' -UseBasicParsing
minikube
```
```
$oldPath = [Environment]::GetEnvironmentVariable('Path', [EnvironmentVariableTarget]::Machine)
if ($oldPath.Split(';') -inotcontains 'C:\minikube'){
  [Environment]::SetEnvironmentVariable('Path', $('{0};C:\minikube' -f $oldPath), [EnvironmentVariableTarget]::Machine)
}
(start cluster)
minikube start --memory=4096 --driver=hyperkit
```
`kubectl get nodes`
### creating pods
1. Check available nodes: (single-host minikube is already present)  
`kubectl get nodes`
2. creat pod.yml file
```vi pod.yml
i
:wq
```
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```
3. Deploy the pod:
`kubectl create -f pod.yml`
4. Check the pod status:
```kubectl get pods
  kubectl get pods -o wide
(copy IP address where application is exposed)
```
5. Access the Application
   login to kubernetess cluster(minikube)
   ```
   minikube ssh
   curl <ip address>
   #application is running
   ```
**For reference: https://kubernetes.io/pt-br/docs/reference/kubectl/cheatsheet/**
