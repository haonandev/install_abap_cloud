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

Create a instances with the parameters below. The best specification is a VM with 32GB RAM, 200GB storage. \
Name: abapcloud-a4h \
Region: asia-southeast1 (Singapore) \
Zone: asia-southeast1-a \
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
Follow the latest installation command on Docker website.\
https://docs.docker.com/engine/install/ubuntu/ \
Or run the commands below:

```
# Add Docker's official GPG key.
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources.
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
Verify that the installation is successful by running the hello-word image.
```
sudo docker run hello-world
```
Run this command to check active container.
```
sudo docker ps -a
```
<img width="841" height="58" alt="image" src="https://github.com/user-attachments/assets/21644cb8-15db-4867-8bec-ebb5e5dc5cf8" />


## Pull ABAP Cloud docker image
After installing Docker, we will pull ABAP cloud Docker image.
```
sudo docker pull sapse/abap-cloud-developer-trial:2023
```
It will take a while to download.\
<img width="774" height="331" alt="image" src="https://github.com/user-attachments/assets/2c5f883b-f2c0-4ec8-84c8-819b757ea77f" />

Once it is completed, run the command below to start the container.
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
If you see this error, run the commands.\
<img width="594" height="105" alt="image" src="https://github.com/user-attachments/assets/12407c9d-32bd-4441-86f6-c588733838d6" />
```
sudo sysctl vm.max_map_count=2147483647
sudo sysctl fs.aio-max-nr=18446744073709551615
```

# Setup connection
The ABAP cloud is installed on Docker in the Ubuntu system. Therefore, we need to allow the connection from external network.

Add firewall rule on Google Cloud to allow external connection.
<img width="872" height="405" alt="image" src="https://github.com/user-attachments/assets/02a115ad-292d-4468-ad9f-60895607d083" />

Detail of the firewall rule.\
<img width="461" height="799" alt="image" src="https://github.com/user-attachments/assets/316ba112-6cb1-44c1-a411-a031ba12bb94" />

Go back to the terminal, run the commands below.
```
sudo apt update
sudo apt install ufw
sudo ufw enable
sudo ufw allow 3200/tcp
sudo ufw allow 3300/tcp
sudo ufw allow 8443/tcp
sudo ufw allow 30213/tcp
sudo ufw allow 50000/tcp
sudo ufw allow 50001/tcp
```
Check status.
```
sudo ufw status
```
<img width="453" height="270" alt="image" src="https://github.com/user-attachments/assets/d647c8ab-2c53-48b5-83e0-db50b3691b7c" />

# Access ABAP cloud system.
Get your public IP, it will return an IP address.
```
curl ifconfig.me
```
Start the container
```
sudo docker start -ai a4h
```
System is started. \
<img width="534" height="150" alt="image" src="https://github.com/user-attachments/assets/e304d5f1-f5b9-42e7-a6df-cd2b005f1788" />

You can access to the system with the properties below: \
Application URL: public IP \
System ID: A4H \
Instance: 00 \
Default login credential for developer: \
Client: 001 \
Username: DEVELOPER \
Password: ABAPtr2023#00 

<img width="717" height="244" alt="image" src="https://github.com/user-attachments/assets/0dc7dda7-c51e-4f79-b75b-435c04d303ac" />

# Post installation step
Update license. \
Login to client 000 with user ID: SAP*, password: ABAPtr2023#00. \
Go to Tcode SLICENSE, get the hardware key. \
Go to this link: https://go.support.sap.com/minisap/#/minisap \
<img width="1414" height="411" alt="image" src="https://github.com/user-attachments/assets/5613bbe0-24f1-4c0a-8d2a-5f2a70d57b64" />
Enter all required information and generate the license.\
<img width="1288" height="527" alt="image" src="https://github.com/user-attachments/assets/07cff787-8d0d-499e-a367-560e18786bd9" />

Go to Tcode SLICENSE again, upload the generated key to system. \
<img width="384" height="98" alt="image" src="https://github.com/user-attachments/assets/e9437182-7ea8-4ae7-bb5c-e2acf65c80c6" />

Once the license is uploaded to the system, then A4H system will be ready to use.

Run tcode SGEN to pre-compile all objects. \
<img width="856" height="735" alt="image" src="https://github.com/user-attachments/assets/36ea3c4f-b3cd-4b8b-9d00-bb1b52f1c807" />
<img width="1297" height="236" alt="image" src="https://github.com/user-attachments/assets/4aa85b6a-d6e3-4268-9fbf-9b391bac6a94" />

Schedule a background job to pre-compile all objects.\
<img width="632" height="228" alt="image" src="https://github.com/user-attachments/assets/c79fbe7b-ba3f-4fcc-985e-66b654a17a5b" />

Go to SM37, check the job status. It will take few hours to pre-compile all objects.\
<img width="1117" height="389" alt="image" src="https://github.com/user-attachments/assets/4a40ba6d-f3f4-4b68-b104-7ee21811b203" />

# System is ready
Once the SGEN job is completed, you can start using the system smoothly.









