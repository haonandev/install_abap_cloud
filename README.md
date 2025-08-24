# Install ABAP Cloud Developer Trial on Google Cloud
# Introduction
This is to share how to run ABAP cloud docker image on Google Cloud. \
ABAP Cloud docker image: https://hub.docker.com/r/sapse/abap-cloud-developer-trial

**Prerequisite:**\
Google cloud account\
SAP Logon

# Installation
## Create virtual machine(VM) on Google Cloud
Go to Virtual instances -> Create Instances\
<img width="886" height="258" alt="image" src="https://github.com/user-attachments/assets/8846f2c4-0779-4669-8f51-ddc5cdea878c" />

Create a instances with the parameters below. The best specification is a VM with 32GB RAM, 200GB storage.
Name: abapcloud-a4h\
Region: asia-southeast1 (Singapore)\
Zone: asia-southeast1-a\
<img width="752" height="183" alt="image" src="https://github.com/user-attachments/assets/cbfec377-1939-4c04-93b4-bbbfa7071ffa" />

Machine types: E2\
Cores: 8 vCPU\
Memory 32 GB\
<img width="759" height="608" alt="image" src="https://github.com/user-attachments/assets/1136f50b-e937-4dfc-b790-57c464cb2982" />

Operating system: Ubuntu\
Storage: 200GB\
<img width="461" height="268" alt="image" src="https://github.com/user-attachments/assets/6d4f7e9b-d862-419f-b5c8-7b10b18cc1aa" />

Networking: Allow HTTP traffic and HTTPS traffic\
<img width="594" height="271" alt="image" src="https://github.com/user-attachments/assets/d6ccb6ff-3df2-4777-84a4-8a6f2c473226" />

Create the VM with the specifications above.
## Run VM
Click on the name, run the VM. Terminal window will appear.\
<img width="371" height="195" alt="image" src="https://github.com/user-attachments/assets/fc390661-72be-4a52-8f68-c8830c76784b" />
<img width="604" height="432" alt="image" src="https://github.com/user-attachments/assets/12652ac9-bf0f-4e55-9c1b-089e6f078170" />

## Install docker
Follow the latest installation command on Docker website:\
https://docs.docker.com/engine/install/ubuntu/ \
Or run the commands below:

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

Install the Docker packages.
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
Verify that the installation is successful by running the hello-word image:
```
sudo docker run hello-world
```
Run this command to check active container:
```
sudo docker ps -a
```
<img width="841" height="58" alt="image" src="https://github.com/user-attachments/assets/21644cb8-15db-4867-8bec-ebb5e5dc5cf8" />


## Pull ABAP Cloud docker image
After installing Docker, we will pull ABAP cloud Docker image.
```
sudo docker pull sapse/abap-cloud-developer-trial:2023
```
It will take a while to download:
<img width="774" height="331" alt="image" src="https://github.com/user-attachments/assets/2c5f883b-f2c0-4ec8-84c8-819b757ea77f" />

Once it is completed, run the command below to start the container:
```
sudo docker run \
--sysctl kernel.shmmax=21474836480 \
--sysctl kernel.shmmni=32768 \
  --sysctl kernel.shmall=5242880 \
  --sysctl kernel.msgmni=1024 \
  --sysctl kernel.sem="1250 256000 100 8192" \
  --ulimit nofile=1048576:1048576 \
  --stop-timeout 3600 \
  -it \
  --name a4h \
  -h vhcala4hci \
  -p 3200:3200 \
  -p 3300:3300 \
  -p 8443:8443 \
  -p 30213:30213 \
  -p 50000:50000 \
  -p 50001:50001 \
  sapse/abap-cloud-developer-trial:2023
```
Check if the container is started:
```
sudo docker ps -a
```
Start container:
```
sudo docker start -ai a4h
```


