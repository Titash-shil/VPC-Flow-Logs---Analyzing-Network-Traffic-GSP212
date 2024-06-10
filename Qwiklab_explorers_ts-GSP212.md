# VPC Flow Logs - Analyzing Network Traffic || [GSP212](https://www.cloudskillsboost.google/focuses/1236?parent=catalog) ||

# # Like, comment, share & Don't forget to subscribe [Qwiklab_Explorers_ts](https://youtube.com/@titashshil?si=RgamNu1dc9jVIbJN) 👍😄🤝

### Run the following Commands in CloudShell
```
export ZONE=
```
```
#!/bin/bash
# Define color variables

BLACK=`tput setaf 0`
RED=`tput setaf 1`
GREEN=`tput setaf 2`
YELLOW=`tput setaf 3`
BLUE=`tput setaf 4`
MAGENTA=`tput setaf 5`
CYAN=`tput setaf 6`
WHITE=`tput setaf 7`

BG_BLACK=`tput setab 0`
BG_RED=`tput setab 1`
BG_GREEN=`tput setab 2`
BG_YELLOW=`tput setab 3`
BG_BLUE=`tput setab 4`
BG_MAGENTA=`tput setab 5`
BG_CYAN=`tput setab 6`
BG_WHITE=`tput setab 7`

BOLD=`tput bold`
RESET=`tput sgr0`
#----------------------------------------------------start--------------------------------------------------#

echo "${YELLOW}${BOLD}Starting${RESET}" "${GREEN}${BOLD}Execution${RESET}"

export REGION="${ZONE%-*}"

gcloud compute networks create vpc-net --project=$DEVSHELL_PROJECT_ID --description=subscribe\ to\ quicklab --subnet-mode=custom --mtu=1460 --bgp-routing-mode=regional && gcloud compute networks subnets create vpc-subnet --project=$DEVSHELL_PROJECT_ID --range=10.1.3.0/24 --stack-type=IPV4_ONLY --network=vpc-net --region=$REGION --enable-flow-logs --logging-aggregation-interval=interval-5-sec --logging-flow-sampling=0.5 --logging-metadata=include-all

gcloud compute --project=$DEVSHELL_PROJECT_ID firewall-rules create allow-http-ssh --direction=INGRESS --priority=1000 --network=vpc-net --action=ALLOW --rules=tcp:80,tcp:22 --source-ranges=0.0.0.0/0 --target-tags=http-server

gcloud compute instances create web-server --project=$DEVSHELL_PROJECT_ID --zone=$ZONE --machine-type=e2-micro --network-interface=network-tier=PREMIUM,stack-type=IPV4_ONLY,subnet=vpc-subnet --metadata=enable-oslogin=true --maintenance-policy=MIGRATE --provisioning-model=STANDARD --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --tags=http-server --create-disk=auto-delete=yes,boot=yes,device-name=web-server,image=projects/debian-cloud/global/images/debian-12-bookworm-v20240515,mode=rw,size=10,type=projects/$DEVSHELL_PROJECT_ID/zones/$ZONE/diskTypes/pd-balanced --no-shielded-secure-boot --no-shielded-vtpm --no-shielded-integrity-monitoring --labels=goog-ec-src=vm_add-gcloud --reservation-affinity=any

sleep 120

gcloud compute ssh --zone "$ZONE" "web-server" --project "$DEVSHELL_PROJECT_ID" --quiet --command 'sudo apt-get update && sudo apt-get install apache2 -y && echo "<!doctype html><html><body><h1>Hello World!</h1></body></html>" | sudo tee /var/www/html/index.html'

bq mk bq_vpcflows

echo "${YELLOW}${BOLD}NOW${RESET}" "${WHITE}${BOLD}FOLLOW${RESET}" "${GREEN}${BOLD}VIDEO'S INSTRUCTIONS${RESET}"

#-----------------------------------------------------end----------------------------------------------------------#
```

* Go to `allow-http-ssh` Firewall from [here](https://console.cloud.google.com/net-security/firewall-manager/firewall-policies/details/allow-http-ssh?)

* Go to `Create sink` from [here](https://console.cloud.google.com/logs/router/sink?)

* For `Sink Name`, type or paste `vpc-flows` 

* Paste the following in `Build inclusion filter` and Change `PROJECT_ID`

```
resource.type="gce_subnetwork"
log_name="projects/PROJECT_ID/logs/compute.googleapis.com%2Fvpc_flows"
```

### Run again the following Commands in CloudShell

```
export MY_SERVER=$(gcloud compute instances describe web-server --zone=$ZONE \
  --format='get(networkInterfaces[0].accessConfigs[0].natIP)')

for ((i=1;i<=50;i++)); do curl $MY_SERVER; done
```

# Congratulations ..!! You completed the lab shortly..😃💯

# *Well done..!* 👏

# Thank you for visiting.... :) 🗯️

# [Qwiklab_Explorers_ts](https://youtube.com/@titashshil?si=RgamNu1dc9jVIbJN)
