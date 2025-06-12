Phase 1 – Local Environment Setup

This document guides you through preparing a local Linux (Ubuntu 24.04 LTS) environment to host your three-tier DevOps demo project. Follow each step carefully and verify before proceeding.

1. Host Preparation

1.1. Install Ubuntu 24.04 LTS

Option A: Bare-metal install on a spare machine.

Option B: VM install in VMware or VirtualBox:

Download the Ubuntu 24.04 ISO from ubuntu.com.

Create a new VM with at least 2 vCPUs, 4 GB RAM, 20 GB disk.

Boot from ISO and follow defaults.

1.2. Create a Non-Root Sudo User

# Log in as root or the initial user
adduser devopsuser           # interactively set password and info
usermod -aG sudo devopsuser  # add to sudo group
su - devopsuser              # switch to new user

1.3. Secure SSH Access

Edit SSH config:

sudo vim /etc/ssh/sshd_config

Change or add the following,

Port 2222
PermitRootLogin no
PasswordAuthentication no     # if using key-pairs

Restart SSH:

sudo systemctl restart sshd

Note: Ensure your SSH key is installed:

# On your workstation:
ssh-keygen -t ed25519
ssh-copy-id -i ~/.ssh/id_ed25519.pub "devopsuser@<host-ip> -p 2222"

2. Install Essential Tools

Switch to your sudo user before proceeding.

sudo apt update && sudo apt upgrade -y
sudo apt install -y \
  git \
  curl \
  wget \
  vim \
  build-essential \
  apt-transport-https \
  ca-certificates \
  gnupg \
  lsb-release

2.1. Docker & Docker Compose

Install Docker:

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io

Manage Docker as non-root:

sudo usermod -aG docker devopsuser
newgrp docker

Install Docker Compose (v2 plugin):

mkdir -p ~/.docker/cli-plugins
curl -SL https://github.com/docker/compose/releases/download/v2.18.1/docker-compose-linux-$(uname -m) \
  -o ~/.docker/cli-plugins/docker-compose
chmod +x ~/.docker/cli-plugins/docker-compose

2.2. Kubernetes (K3s)

For a lightweight local cluster, use K3s:

curl -sfL https://get.k3s.io | sh -
# Verify:
sudo k3s kubectl get nodes
# Enable kubectl without sudo for devopsuser:
sudo cp /etc/rancher/k3s/k3s.yaml touch
sudo chown $(id -u):$(id -g) ~/.kube/config

Alternative: Install Minikube if preferred:

curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
chmod +x minikube && sudo mv minikube /usr/local/bin/
minikube start --driver=docker

3. Directory & Git Repository Layout

Create a project scaffold to keep code, configs, and docs organized.

cd ~
git init devops-demo
cd devops-demo
mkdir -p docs ansible terraform k8s jenkins src/{frontend,api,db}

docs/ – this documentation and design artifacts

ansible/ – playbooks & roles for configuration management

terraform/ – future cloud IaC

k8s/ – Kubernetes manifests & Helm charts

jenkins/ – Pipeline scripts (Jenkinsfiles)

src/ – application source code

Commit your scaffold:

git add .
git commit -m "chore: project scaffold for DevOps demo"

4. Verification & Next Steps

Verify Tools:

docker version

docker-compose version -- not working

kubectl version --client

ansible --version (install later)

Document: In docs/phase1.md, copy these steps and add any deviations you encountered.

Once everything is green, we’ll move on to Phase 2: Dockerization & Compose.

End of Phase 1 setup guide.
