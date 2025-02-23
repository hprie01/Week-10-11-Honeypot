# Week-10-11-Honeypot
Time Spent: **6** hours total

## Overview & Setup
### Milestone 0: To the Cloud
#### Initialize Google SDK
```
gcloud init
gcloud projects list
gcloud config set project honeypot-258220
```
![](Honeypot/Projects_List.png)

#### Enable billing
```
gcloud alpha billings accounts list
gcloud alpha billing projects link honeypot-258220 --billing-account 01B787-37BD8D-D1CE2C
```

#### Enable Compute API
```
gcloud services list
gcloud services list --available
gcloud services list --available | findstr compute
gcloud services enable compute
```
![](Honeypot/Services_List.png)
#### Find and SEt region and zone
```
gcloud compute regions list
gcloud compute zones list

cloud config set compute/zone us-central1-f
gcloud compute project-info add-metadata \
    --metadata google-compute-default-region=us-central1,google-compute-default-zone=us-central1-f
```
![](Honeypot/Compute_Regions_Zones.png)
### Mileston 1: Create MHN Admin VM
#### Configure Firewall
```
gcloud compute firewall-rules list

gcloud compute firewall-rules create http ^
    --allow tcp:80 ^
    --description="Allow HTTP from Anywhere" ^
    --direction ingress ^
    --target-tags="mhn-admin"
    
gcloud compute firewall-rules create honeymap ^
    --allow tcp:3000 ^
    --description="Allow HoneyMap Feature from Anywhere" ^
    --direction ingress ^
    --target-tags="mhn-admin"
```
![](Honeypot/Configure_Firewall.png)
```
gcloud compute firewall-rules create hpfeeds ^
    --allow tcp:10000 ^
    --description="Allow HPFeeds from Anywhere" ^
    --direction ingress ^
    --target-tags="mhn-admin"

gcloud compute firewall-rules create wideopen ^
    --description="Allow TCP and UDP from Anywhere" ^
    --direction ingress ^
    --priority=1000 ^
    --network=default ^
    --action=allow ^
    --rules=tcp,udp ^
    --source-ranges=0.0.0.0/0 ^
    --target-tags="honeypot"
```
![](Honeypot/Configure_Firewall_1.png)

#### Verify Firewall in windows CMD
```
gcloud compute firewall-rules list --format="table(name,network,direction,priority,sourceRanges.list():label=SRC_RANGES,allowed[].map().firewall_rule().list():label=ALLOW,targetTags.list():label=TARGET_TAGS,disabled)"
```
![](Honeypot/Configure_Firewall_Windows_CMD.png)

#### MHN Admin
```
gcloud compute images list | findstr ubuntu-minimal

gcloud compute instances create "mhn-admin" ^
    --machine-type "n1-standard-1" ^
    --subnet "default" ^
    --maintenance-policy "MIGRATE" ^
    --tags "mhn-admin" ^
    --image "ubuntu-minimal-1804-bionic-v20191024" ^
    --image-project "ubuntu-os-cloud" ^
    --boot-disk-size "10" ^
    --boot-disk-type "pd-standard" ^
    --boot-disk-device-name "mhn-admin"
```
![](Honeypot/MHN_Admin.png)
#### Setup SSH
```
gcloud compute config-ssh
```

### Milestone 2: Install the MHN Admin Application

#### Install
```
sudo apt update && sudo apt install git python-magic -y

cd /opt/
sudo git clone https://github.com/pwnlandia/mhn.git
cd mhn/

sudo ./install.sh
```
![](Honeypot/Install_MHN.png)
![](Honeypot/Install_MHN_1.png)

#### Configure
```
===========================================================
MHN Configuration
===========================================================
Do you wish to run in Debug mode?: y/n n
Superuser email: polarber@diamond.com
Superuser password: deliverance
Server base url ["http://35.232.198.247"]:
Honeymap url ["http://35.232.198.247:3000"]:
Mail server address ["localhost"]: 
Mail server port [25]: 
Use TLS for email?: y/n n
Use SSL for email?: y/n n
Mail server username [""]: 
Mail server password [""]: 
Mail default sender [""]: 
Path for log file ["mhn.log"]:
```
![](Honeypot/MHN_Configuration.png)

#### Confirm successful installation
```
sudo /etc/init.d/nginx status
sudo /etc/init.d/supervisor status
sudo supervisorctl status
```
![](Honeypot/Successful_Installations.png)

### Milestone 3: Create a MHN Honeypot VM
#### Honeypot
```
gcloud compute instances create "honeypot" \
    --machine-type "n1-standard-1" \
    --subnet "default" \
    --maintenance-policy "MIGRATE" \
    --tags "honeypot" \
    --image "ubuntu-minimal-1804-bionic-v20191024" \
    --image-project "ubuntu-os-cloud" \
    --boot-disk-size "10" \
    --boot-disk-type "pd-standard" \
    --boot-disk-device-name "honeypot"
```
![](Honeypot/Create_Honeypot.png)

#### Setup SSH
```
gcloud compute config-ssh
```

### Milestone 4: Install the Honeypot Application

#### Connect to the MHN Admin IP address in Chrome
`http://35.232.198.247/`

## Deploy Honeypot
### Milestone 5: Attack!
#### Deploy Honeypot Sensors
- In web browser, click Deploy and Select Script Ubuntu/Raspberry Pi-Dionaea
- Copy the Deploy Command and enter into the command in the honeypot terminal and confirm successful deployment.
![](Honeypot/Deploy_Script.JPG)
![](Honeypot/Deploy_Honeypot.JPG)
![](Honeypot_Attack.gif)
### Exporting Data
#### Dump Sensor Database
```
mongoexport --db mnemosyne --collection session > session.json
```

### Cleanup
```
gcloud projects list
gcloud projects delete PROJECT-ID
```
## Summary
Honeypot deployed: Ubuntu/Raspberry Pi-Dionaea
Issues Encountered:
    - Some issues encountered included how to use the cloud service. After figuring out how to use the service everything ran smoothly.
Summary of Data:
    - According to the data, it states that there has only been one attack made, but there are attacks occuring in the map.
![](Attacks.JPG)
