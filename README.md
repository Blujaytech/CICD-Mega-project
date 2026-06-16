CICD-Mega-Project
----------------------
# Project-Overview


# Tech-stack

Server(Rhel9)..1 [Take high configure]

take. volume memory 70GB

# Deployment Stages:

   
# Installation- process.

# Docker-Installation

vi docker.sh 

```
#!/bin/bash

#check whether root user or not
R="\e[31m"
N="\e[0m"

yum install -y yum-utils
yum-config-manager --add-repo https://download.docker.com/linux/rhel/docker-ce.repo
yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
systemctl start docker
systemctl enable docker
usermod -aG docker ec2-user
echo -e "$R Logout and Login again $N"

```
```
sh docker.sh
```

```
curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b /usr/local/bin

```
```
sudo chmod 777 /var/run/docker.sock
```

# Extende Volume-Memory
```
growpart /dev/nvme0n1 4
lvextend -l +50%FREE /dev/RootVG/rootVol
lvextend -l +50%FREE /dev/RootVG/varVol
xfs_growfs /xfs_growfs /var
lsblk
```


```
docker run -d -p 9000:9000 --name sonarqube   -v sonarqube_data:/opt/sonarqube/data   -v sonarqube_logs:/opt/sonarqube/logs   -v sonarqube_extensions:/opt/sonarqube/extensions   sonarqube:lts-community
```

```
docker ps
```

```
docker logs -f sonarqube
```

# Jenkins-Installation.

```
curl -L -o /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
```

```
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
```

```
du -sh /var/lib/* 2>/dev/null | sort -rh | head -10
df -h /var
vgdisplay RootVG | grep -E "Free|Size"
lvextend -L +3G /dev/mapper/RootVG-varVol
xfs_growfs /var
echo "cachedir=/var/tmp/dnf-cache" >> /etc/dnf/dnf.conf
dnf clean all
```
```
yum clean allyum makecache

dnf install java-21-openjdk java-21-openjdk-devel -y

java --version
```
```
yum install -y jenkins
```

```
systemctl enable jenkins
```

```
systemctl start jenkins
```

```
systemctl status jenkins
```

```
cat /var/lib/jenkins/secrets/initialAdminPassword
```
# GitLeaks-Installation

Download GitLeaks
```
wget https://github.com/gitleaks/gitleaks/releases/latest/download/gitleaks_linux_x64.tar.gz
```
```
tar -xzf gitleaks_linux_x64.tar.gz
```

```
sudo mv gitleaks /usr/local/bin/
```
Verify installation

```
gitleaks version

```

# Trivy-Installation

```
cat << EOF | sudo tee -a /etc/yum.repos.d/trivy.repo
[trivy]
name=Trivy repository
baseurl=https://aquasecurity.github.io/trivy-repo/rpm/releases/\$basearch/
gpgcheck=1
enabled=1
gpgkey=https://aquasecurity.github.io/trivy-repo/rpm/public.key
EOF
```

```
sudo yum -y install trivy
```

```
http: <PlubicIP:8080> ---Jenkins-server
http: <PlubicIP:9000> ---SonarQube
```
# aws-eksctl-Installation

IAM ROLE also Create for EKS-Cluster creation. 
------------------------------kubectl------------------------------

```
   curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
   curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
   curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
  chmod +x kubectl
   mv kubectl /usr/local/bin/
   kubectl version --client
```
-------------------------eksctl------------------------------------
```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
   mv /tmp/eksctl /usr/local/bin
   eksctl version
   eksctl create cluster --name blujay-cluster --region us-east-1 --nodegroup-name demo-nodes --node-type t3.small --nodes 2
   eksctl delete cluster --name blujay-cluster --region us-east-1
   kubectl get nodes
```
# Jenkins and k8s Authendication
1. Check Current Permissions
Commands.

```
kubectl auth can-i --list \
--as=system:serviceaccount:dev:jenkins
```

```
kubectl auth can-i → Checks what actions a user or service account is allowed to perform.
--list → Displays all permissions available.
--as=system:serviceaccount:dev:jenkins → Tests permissions of the jenkins service account in the dev namespace.
```

2. Create jenkins-cluster-admin.yaml

```   
vi jenkins-cluster-admin.yaml
```
``` 
YAML File
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: jenkins-cluster-admin

subjects:
- kind: ServiceAccount
  name: jenkins
  namespace: dev

roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
  ```

3. Apply the YAML File
Command

```
kubectl apply -f jenkins-cluster-admin.yaml
```

4. Verify Full Access
Command

```
kubectl auth can-i '*' '*' \
--as=system:serviceaccount:dev:jenkins
```

5. Verify Access to CRDs
Command
```
kubectl auth can-i get crd \
--as=system:serviceaccount:dev:jenkins
```

Solution 2: Install cert-manager Outside Jenkins

```

kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.14.4/cert-manager.yaml
```
```
kubectl get pods -n cert-manager
```
# Jenkins-Plugin

```
nodejs
sonarqube scanner
sonarqube Qualitygate
Docker pipeline
Docker Comman
Docker
kubernetes
Pipeline StageView
```
