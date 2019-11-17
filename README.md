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
[!] Projects_List.png

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

#### Find and SEt region and zone
```
gcloud compute regions list
gcloud compute zones list

cloud config set compute/zone us-central1-f
gcloud compute project-info add-metadata \
    --metadata google-compute-default-region=us-central1,google-compute-default-zone=us-central1-f
```

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

#### Verify Firewall in windows CMD
```
gcloud compute firewall-rules list --format="table(name,network,direction,priority,sourceRanges.list():label=SRC_RANGES,allowed[].map().firewall_rule().list():label=ALLOW,targetTags.list():label=TARGET_TAGS,disabled)"
```

MHN Admin
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

Setup SSH
```
gcloud compute config-ssh
```

### Milestone 2: Install the MHN Admin Application

Install
```
sudo apt update && sudo apt install git python-magic -y

cd /opt/
sudo git clone https://github.com/pwnlandia/mhn.git
cd mhn/

sudo ./install.sh
```

Configure
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

Confirm successful installation
```
sudo /etc/init.d/nginx status
sudo /etc/init.d/supervisor status
sudo supervisorctl status
```

### Milestone 3: Create a MHN Honeypot VM
Honeypot
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

Setup SSH
```
gcloud compute config-ssh
```

### Milestone 4: Install the Honeypot Application

Connect to the MHN Admin IP address in Chrome
`http://35.232.198.247/`

### Milestone 5: Attack!

- In web browser, click Deplot and Select Script Ubuntu/Raspberry Pi-Dionaea
- Copy the Deploy Command and enter into the command in the honeypot terminal and confirm successful deployment.

Deploy Honeypot Sensors
```
curl ipinfo.io.ip <----if i forgot the public IP of the MHN admin
```

### Exporting Data
Dump Sensor Database
```
mongoexport --db mnemosyne --collection session > session.json
```

### Cleanup
Clean Up
```
gcloud projects list
gcloud projects delete PROJECT-ID
```

Any issues you encountered
A summary of the data collected: number of attacks, number of malware samples, etc.
Any unresolved questions raised by the data collected

Additionally, include a json export of the data you collected in the repo, instructions for which can be found in the next section.

## Required: Demonstration
(Which Honeypot's you deployed
