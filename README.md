
# Project Title

Go-lang Application with Kubernetes-**kubeadm** Infrastructure Managed by Terraform and CI/CD with Jenkins
## Description:

This project involves the deployment of a Go-lang application on a locally hosted server with infrastructure provisioned on AWS using Terraform. The project includes setting up an EC2 instance, VPC, subnets, and security groups. A Kubernetes cluster is manually created using kubeadm, and the application is containerized using Docker. The CI/CD pipeline is managed through GitHub, Jenkins, DockerHub, and Helm to automate the building, testing, and deployment of the application on Kubernetes. Service exposure is handled via Kubernetes NodePort, with secure access and permissions managed through service accounts, roles, role bindings, and secrets.

## Tools and Technologies Used
- Go-lang
- Terraform
##### AWS (Amazon Web Services):
- EC2
- VPC
- Subnets
- Security Groups
##### Kubernetes:
- kubeadm
- kubelet
- kubectl
- Calico
##### Kubernetes Security Tools:
- Service Accounts
- Roles and Role Bindings
- Secrets
##### Pipeline
- Docker
- DockerHub
- GitHub
- Jenkins
- Helm
- NodePort

## Pipeline Flow Diagram
![P03](https://github.com/user-attachments/assets/9a84674c-fafc-4c5b-a4f4-ec0e423ec60e)




## Components and Tools Used
## Go-lang Application:
A application built using Go-lang, deployed in a Kubernetes cluster, and exposed via a NodePort service for external access.

## Infrastructure Provisioning with Terraform:
- **Terraform** is used to automate the creation of AWS infrastructure components
- **EC2 Instances** Virtual machines for hosting the Kubernetes master and worker nodes.
- **VPC (Virtual Private Cloud)** A custom VPC is created to isolate and manage the network environment.
- **Subnets** Public and private subnets are created within the VPC.
- **Security Groups** Security groups are configured for EC2 instances to control inbound and outbound traffic securely.

## Kubernetes Cluster Setup with kubeadm:
- **kubeadm** is used to initialize the Kubernetes cluster and manage both master and worker nodes.
- **Kubelet** and **kubectl** are installed to manage and communicate with the cluster.
- **Calico** is used as the Container Network Interface (CNI) for network policy management, ensuring secure and efficient pod communication within the Kubernetes cluster.
## Source Code Management with GitHub:
- **GitHub** is used for version control and source code management, allowing team collaboration and triggering automated builds through Jenkins when new code is pushed.

## Containerization with Docker and DockerHub:
- The Go-lang application is containerized using **Docker**, ensuring a consistent deployment environment across various stages.

- The Docker images are built in the CI pipeline and pushed to **DockerHub**, making them available for deployment in Kubernetes.

## CI/CD Pipeline with Jenkins:
- **Jenkins** is used to automate the CI/CD pipeline, including:
- **Building** the Docker image for the Go-lang application.
- **Testing** to ensure code quality and functionality.
- **Pushing** the Docker image to DockerHub.
- **Deploying** the application to the Kubernetes cluster using Helm.
- Jenkins automates the entire build, test, and deploy cycle, ensuring fast, consistent, and error-free deployments.

## Helm for Kubernetes Resource Management:
- **Helm** is used to package and deploy Kubernetes resources for the Go-lang application. Helm charts simplify the Kubernetes deployment process and make it easy to manage, upgrade, and roll back the application.
- Helm charts define all Kubernetes resources (e.g., deployments, services, secrets), making the deployment reproducible and version-controlled.

## Access and Security Management:
- **Service Accounts** Kubernetes service accounts are created for Jenkins and other components to interact securely with the Kubernetes cluster.
- **Roles and Role Bindings** Kubernetes roles and role bindings ensure that only authorized entities (e.g., Jenkins) have access to perform specific actions within the cluster.
- **Secrets** Sensitive data (e.g., API keys, credentials) is stored securely in Kubernetes using secrets, ensuring secure access during application runtime.

## Service Exposure via NodePort:
- The Go-lang application is exposed externally using **NodePort**, allowing users to access the application via a specific port on the Kubernetes nodes.

# Project Workflow:
## Infrastructure Provisioning:
Terraform scripts are used to provision EC2 instances, VPC, subnets, and security groups on AWS, creating the environment required to host the Kubernetes cluster.

## Kubernetes Cluster Setup:
The Kubernetes cluster is set up manually using kubeadm, with the master and worker nodes initialized. Kubelet and kubectl are used to manage cluster operations.
Calico is installed to manage Kubernetes network policies and secure communication between pods.

## Source Code Management and CI/CD:
Developers push changes to GitHub, which triggers the Jenkins pipeline.
Jenkins builds the Docker image for the Go-lang application, performs tests, and pushes the image to DockerHub.

## Application Deployment:
Helm is used to deploy the Go-lang application to the Kubernetes cluster, defining all necessary resources such as deployments, services, and secrets.
The application is exposed via a NodePort service, allowing users to access the application externally.

## Access Control and Security:
Service accounts, roles, and role bindings are configured to ensure secure access between Jenkins and Kubernetes.
Sensitive data is securely managed using Kubernetes secrets.

## Infrastructure Provisioning with Terraform
![Screenshot (415)](https://github.com/user-attachments/assets/73235747-9286-47af-9ab9-e421f05fcb0d)
![Screenshot (414)](https://github.com/user-attachments/assets/96def3c9-6191-4c0d-8447-7f7428487449)
## EC2 Instances
![Screenshot (423)](https://github.com/user-attachments/assets/5410d0da-e61f-461e-af8d-f4937399eadf)
## AWS VPC
![Screenshot (434)](https://github.com/user-attachments/assets/d1d5a365-7a56-4841-9741-a0f1ac2d8264)
## AWS Subnets
![Screenshot (435)](https://github.com/user-attachments/assets/ac73d8bd-ae66-45b1-8fbe-d87a99f69133)
## AWS Route Tables
![Screenshot (436)](https://github.com/user-attachments/assets/992752f8-823a-471c-b205-efb91f75c8e1)
## AWS Internet Gateway
![Screenshot (437)](https://github.com/user-attachments/assets/373fec76-5851-4072-97a0-202b0ba17595)
## AWS Security Groups
![Screenshot (438)](https://github.com/user-attachments/assets/7cc8253e-0ef0-4e95-9104-28a501f69e7b)
![Screenshot (439)](https://github.com/user-attachments/assets/943ae1d4-bbc8-4260-b65a-40d27d1409fb)
## Installation

Install Control Plane and Worker Node 
```bash
  #!/bin/bash
#
# Common setup for all servers (Control Plane and Nodes)

set -euxo pipefail

# Kuernetes Variable Declaration

KUBERNETES_VERSION="1.29.0-1.1"

# disable swap
sudo swapoff -a

# keeps the swaf off during reboot
(crontab -l 2>/dev/null; echo "@reboot /sbin/swapoff -a") | crontab - || true
sudo apt-get update -y


# Install CRI-O Runtime

OS="xUbuntu_22.04"

VERSION="1.28"

# Create the .conf file to load the modules at bootup
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system

cat <<EOF | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list
deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/ /
EOF
cat <<EOF | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable:cri-o:$VERSION.list
deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/$VERSION/$OS/ /
EOF

curl -L https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable:cri-o:$VERSION/$OS/Release.key | sudo apt-key --keyring /etc/apt/trusted.gpg.d/libcontainers.gpg add -
curl -L https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/Release.key | sudo apt-key --keyring /etc/apt/trusted.gpg.d/libcontainers.gpg add -

sudo apt-get update
sudo apt-get install cri-o cri-o-runc -y

sudo systemctl daemon-reload
sudo systemctl enable crio --now

echo "CRI runtime installed susccessfully"

# Install kubelet, kubectl and Kubeadm

sudo apt-get update -y
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-1-28-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-1-28-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes-1.28.list

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-1-29-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-1-29-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes-1.29.list

sudo apt-get update -y
sudo apt-get install -y kubelet="$KUBERNETES_VERSION" kubectl="$KUBERNETES_VERSION" kubeadm="$KUBERNETES_VERSION"
sudo apt-get update -y
sudo apt-mark hold kubelet kubeadm kubectl

sudo apt-get install -y jq

local_ip="$(ip --json addr show eth0 | jq -r '.[0].addr_info[] | select(.family == "inet") | .local')"
cat > /etc/default/kubelet << EOF
KUBELET_EXTRA_ARGS=--node-ip=$local_ip
EOF
```
## Installation

Install control plane only

```bash
  #!/bin/bash
#
# Setup for Control Plane (Master) servers

set -euxo pipefail

# If you need public access to API server using the servers Public IP adress, change PUBLIC_IP_ACCESS to true.

PUBLIC_IP_ACCESS="false"
NODENAME=$(hostname -s)
POD_CIDR="192.168.0.0/16"

# Pull required images

sudo kubeadm config images pull

# Initialize kubeadm based on PUBLIC_IP_ACCESS

if [[ "$PUBLIC_IP_ACCESS" == "false" ]]; then
    
    MASTER_PRIVATE_IP=$(ip addr show eth0 | awk '/inet / {print $2}' | cut -d/ -f1)
    sudo kubeadm init --apiserver-advertise-address="$MASTER_PRIVATE_IP" --apiserver-cert-extra-sans="$MASTER_PRIVATE_IP" --pod-network-cidr="$POD_CIDR" --node-name "$NODENAME" --ignore-preflight-errors Swap

elif [[ "$PUBLIC_IP_ACCESS" == "true" ]]; then

    MASTER_PUBLIC_IP=$(curl ifconfig.me && echo "")
    sudo kubeadm init --control-plane-endpoint="$MASTER_PUBLIC_IP" --apiserver-cert-extra-sans="$MASTER_PUBLIC_IP" --pod-network-cidr="$POD_CIDR" --node-name "$NODENAME" --ignore-preflight-errors Swap

else
    echo "Error: MASTER_PUBLIC_IP has an invalid value: $PUBLIC_IP_ACCESS"
    exit 1
fi

# Configure kubeconfig

mkdir -p "$HOME"/.kube
sudo cp -i /etc/kubernetes/admin.conf "$HOME"/.kube/config
sudo chown "$(id -u)":"$(id -g)" "$HOME"/.kube/config

# Install Claico Network Plugin Network 

kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

## Service Accounts
![Screenshot (493)](https://github.com/user-attachments/assets/364dd84d-b270-44c4-8fed-2dce126b94db)
## Roles
![Screenshot (490)](https://github.com/user-attachments/assets/40b11ee7-5395-4fda-aa61-acdbc49558aa)
## Role Bindings
![Screenshot (492)](https://github.com/user-attachments/assets/a0dd056c-e185-44d0-86a2-27f6430a0c91)
## Secrets
![Screenshot (451)](https://github.com/user-attachments/assets/dc0ababd-bdb5-4ae9-adbb-0114788323b2)
## kubernetes Metrics Server Install
![Screenshot (431)](https://github.com/user-attachments/assets/7589f4fe-0054-483e-bd5b-d0bfa815999d)
![Screenshot (430)](https://github.com/user-attachments/assets/72424bde-c3c8-4730-a427-66dc50956d26)
![Screenshot (433)](https://github.com/user-attachments/assets/73d27d80-4377-499f-a40e-3851b0165d4d)

## kubernetes labels For Nodes
![Screenshot (429)](https://github.com/user-attachments/assets/a7647833-96d2-4849-b5e1-d5aa2358d62d)

## Kubeadm Cluster OUTPUT
![Screenshot (427)](https://github.com/user-attachments/assets/b7451567-e6d1-4c4a-ad05-9572fbe169e4)
![Screenshot (428)](https://github.com/user-attachments/assets/42852bd0-91ee-4b14-9bbc-5d653c4ab660)
![Screenshot (432)](https://github.com/user-attachments/assets/a138b65f-e936-4477-aa78-557ecb625c85)

## Jenkins Setup
![Screenshot (440)](https://github.com/user-attachments/assets/b4acc2ab-95a8-44f1-8724-f096c24ab93c)
## Kubeadm Credentials For Jenkins
![Screenshot (457)](https://github.com/user-attachments/assets/a276f925-1e06-4d77-ae40-c00646913ddd)

## Jenkins OUTPUT
![Screenshot (441)](https://github.com/user-attachments/assets/a687b930-21e3-46c0-84af-29cd1c7d0a90)
![Screenshot (486)](https://github.com/user-attachments/assets/ee548e18-9c2e-4ffa-9f79-9001f7e4eec0)
![Screenshot (485)](https://github.com/user-attachments/assets/d88dfdc4-560e-49dc-9e71-c0bcc76bd35f)
![Screenshot (471)](https://github.com/user-attachments/assets/8240b72b-13a0-4ec9-b12d-5c8732a19632)

## DockerHub OUTPUT
![Screenshot (445)](https://github.com/user-attachments/assets/6fa3866f-d8b5-4ed2-a932-89a35f8b313e)

## Kubeadm OUTPUT
![Screenshot (475)](https://github.com/user-attachments/assets/5f0d98a5-2869-438d-89a5-5e7066a2d11a)
![Screenshot (497)](https://github.com/user-attachments/assets/03167992-79c1-4946-b505-43d2acac066b)

## Application OUTPUT
![Screenshot (498)](https://github.com/user-attachments/assets/43a3473c-ea74-4f9e-b362-82ec38d26280)

## Overall Output
![Screenshot (495)](https://github.com/user-attachments/assets/8d730c04-6013-4b55-b0b4-5877b5019361)
![Screenshot (489)](https://github.com/user-attachments/assets/43b8fb32-1ad2-4900-9e51-9be03697fb4d)

**This project demonstrates a complete DevOps pipeline for deploying a Go-lang application on a Kubernetes cluster, with infrastructure provisioned via Terraform and Docker used for containerization. By integrating GitHub, Jenkins, DockerHub, and Helm, the project ensures automated and scalable CI/CD workflows. Service exposure is handled via Kubernetes NodePort, while access and security are managed through Kubernetes service accounts, roles, role bindings, and secrets. This project delivers a robust, secure, and automated solution for deploying cloud-native applications.**


# **Thank you.......**
