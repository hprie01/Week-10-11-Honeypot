# Week-10-11-Honeypot
Time Spent: **6** hours total

## Overview & Setup
### Milestone 0: To the Cloud
1.  
Initialize Google SDK
```
gcloud init
gcloud projects list
gcloud config set project honeypot-258220
```

Enable billing
```
gcloud alpha billings accounts list
gcloud alpha billing projects link honeypot-258220 --billing-account 01B787-37BD8D-D1CE2C
```

Enable Compute API
```
gcloud services list
gcloud services list --available
gcloud services list --available | findstr compute
gcloud services enable compute
```

Find and SEt region and zone
```
gcloud compute regions list
gcloud compute zones list

cloud config set compute/zone us-central1-f
gcloud compute project-info add-metadata \
    --metadata google-compute-default-region=us-central1,google-compute-default-zone=us-central1-f
```

Configure Firewall
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

Verify Firewall  in windows CMD
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
Server base url ["          "]:
Honeymap url ["                "]:
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

Honeypot
```

```


Any issues you encountered
A summary of the data collected: number of attacks, number of malware samples, etc.
Any unresolved questions raised by the data collected

Additionally, include a json export of the data you collected in the repo, instructions for which can be found in the next section.

## Required: Demonstration
(Which Honeypot's you deployed
