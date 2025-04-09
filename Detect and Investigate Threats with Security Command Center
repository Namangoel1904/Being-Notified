#!/bin/bash

# Define color variables
BLACK_TEXT=$'\033[0;90m'
RED_TEXT=$'\033[0;91m'
GREEN_TEXT=$'\033[0;92m'
YELLOW_TEXT=$'\033[0;93m'
BLUE_TEXT=$'\033[0;94m'
MAGENTA_TEXT=$'\033[0;95m'
CYAN_TEXT=$'\033[0;96m'
WHITE_TEXT=$'\033[0;97m'

NO_COLOR=$'\033[0m'
RESET_FORMAT=$'\033[0m'
BOLD_TEXT=$'\033[1m'
UNDERLINE_TEXT=$'\033[4m'

# Clear the screen
clear

# Print the welcome message
echo "${BLUE_TEXT}${BOLD_TEXT}=======================================${RESET_FORMAT}"
echo "${BLUE_TEXT}${BOLD_TEXT}         INITIATING EXECUTION...  ${RESET_FORMAT}"
echo "${BLUE_TEXT}${BOLD_TEXT}=======================================${RESET_FORMAT}"
echo

# Step 1: Get Compute Zone & Region
echo "${YELLOW_TEXT}${BOLD_TEXT}Step 1: Fetching Compute Zone & Region...${RESET_FORMAT}"
export ZONE=$(gcloud compute project-info describe \
--format="value(commonInstanceMetadata.items[google-compute-default-zone])")

export REGION=$(gcloud compute project-info describe \
--format="value(commonInstanceMetadata.items[google-compute-default-region])")

# Step 2: Get IAM Policy and Save to JSON
echo "${BLUE_TEXT}${BOLD_TEXT}Step 2: Retrieving IAM Policy...${RESET_FORMAT}"
echo "${CYAN_TEXT}The current IAM policy will be saved to a JSON file for further updates.${RESET_FORMAT}"
gcloud projects get-iam-policy $(gcloud config get-value project) \
    --format=json > policy.json

# Step 3: Update IAM Policy
echo "${GREEN_TEXT}${BOLD_TEXT}Step 3: Updating IAM Policy...${RESET_FORMAT}"
echo "${CYAN_TEXT}Modifying the IAM policy to include audit configurations.${RESET_FORMAT}"
jq '{ 
  "auditConfigs": [ 
    { 
      "service": "cloudresourcemanager.googleapis.com", 
      "auditLogConfigs": [ 
        { 
          "logType": "ADMIN_READ" 
        } 
      ] 
    } 
  ] 
} + .' policy.json > updated_policy.json

# Step 4: Set Updated IAM Policy
echo "${RED_TEXT}${BOLD_TEXT}Step 4: Applying Updated IAM Policy...${RESET_FORMAT}"
gcloud projects set-iam-policy $(gcloud config get-value project) updated_policy.json

# Step 5: Enable Security Center API
echo "${CYAN_TEXT}${BOLD_TEXT}Step 5: Enabling Security Center API...${RESET_FORMAT}"
gcloud services enable securitycenter.googleapis.com --project=$DEVSHELL_PROJECT_ID

# Step 6: Wait for 20 seconds
echo "${YELLOW_TEXT}${BOLD_TEXT}Step 6: Waiting for API to be enabled...${RESET_FORMAT}"
echo "${CYAN_TEXT}Please wait while the API is being enabled. This may take a few seconds.${RESET_FORMAT}"
sleep 20

# Step 7: Add IAM Binding for BigQuery Admin
echo "${MAGENTA_TEXT}${BOLD_TEXT}Step 7: Granting BigQuery Admin Role...${RESET_FORMAT}"
gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID \
--member=user:demouser1@gmail.com --role=roles/bigquery.admin

# Step 8: Remove IAM Binding for BigQuery Admin
echo "${BLUE_TEXT}${BOLD_TEXT}Step 8: Revoking BigQuery Admin Role...${RESET_FORMAT}"
gcloud projects remove-iam-policy-binding $DEVSHELL_PROJECT_ID \
--member=user:demouser1@gmail.com --role=roles/bigquery.admin

# Step 9: Add IAM Binding for IAM Admin
echo "${GREEN_TEXT}${BOLD_TEXT}Step 9: Granting IAM Admin Role...${RESET_FORMAT}"
gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID \
  --member=user:$USER_EMAIL \
  --role=roles/cloudresourcemanager.projectIamAdmin 2>/dev/null

# Step 10: Create Compute Instance
echo "${BLUE_TEXT}${BOLD_TEXT}Step 10: Creating Compute Instance...${RESET_FORMAT}"
echo "${CYAN_TEXT}A new Compute Engine instance will be created in the specified zone.${RESET_FORMAT}"
gcloud compute instances create instance-1 \
--zone=$ZONE \
--machine-type=e2-medium \
--network-interface=network-tier=PREMIUM,stack-type=IPV4_ONLY,subnet=default \
--metadata=enable-oslogin=true --maintenance-policy=MIGRATE --provisioning-model=STANDARD \
--scopes=https://www.googleapis.com/auth/cloud-platform --create-disk=auto-delete=yes,boot=yes,device-name=instance-1,image=projects/debian-cloud/global/images/debian-11-bullseye-v20230912,mode=rw,size=10,type=projects/$DEVSHELL_PROJECT_ID/zones/$ZONE/diskTypes/pd-balanced

# Step 11: Create DNS Policy
echo "${CYAN_TEXT}${BOLD_TEXT}Step 11: Creating DNS Policy...${RESET_FORMAT}"
gcloud dns --project=$DEVSHELL_PROJECT_ID policies create dns-test-policy --description="arcadecrew" --networks="default" --private-alternative-name-servers="" --no-enable-inbound-forwarding --enable-logging

# Step 12: Wait for 30 seconds
echo "${YELLOW_TEXT}${BOLD_TEXT}Step 12: Waiting for DNS Policy to take effect...${RESET_FORMAT}"
echo "${CYAN_TEXT}Please wait while the DNS policy is being applied. This may take a few seconds.${RESET_FORMAT}"
sleep 30

# Step 13: SSH into Compute Instance and Execute Commands
echo "${MAGENTA_TEXT}${BOLD_TEXT}Step 13: Connecting to Compute Instance...${RESET_FORMAT}"
echo "${CYAN_TEXT}Establishing an SSH connection to the Compute Engine instance and executing commands.${RESET_FORMAT}"
gcloud compute ssh instance-1 --zone=$ZONE --tunnel-through-iap --project "$DEVSHELL_PROJECT_ID" --quiet --command "gcloud projects get-iam-policy \$(gcloud config get project) && curl etd-malware-trigger.goog"
echo
echo
echo "${GREEN_TEXT}${BOLD_TEXT}Please check progress of  ${YELLOW_TEXT}TASK 1 & TASK 2${RESET_FORMAT} before proceeding.${RESET_FORMAT}"
echo
# Function to prompt user to check their progress
function check_progress {
    while true; do
        echo
        echo -n "${YELLOW_TEXT}${BOLD_TEXT}Have you checked your progress for ${YELLOW_TEXT}TASK 1 & TASK 2${RESET_FORMAT}? (Y/N): ${RESET_FORMAT}"
        read -r user_input
        if [[ "$user_input" == "Y" || "$user_input" == "y" ]]; then
            echo
            echo "${GREEN_TEXT}${BOLD_TEXT}Great! Moving on to the next steps...${RESET_FORMAT}"
            echo
            break
        elif [[ "$user_input" == "N" || "$user_input" == "n" ]]; then
            echo
            echo "${RED_TEXT}${BOLD_TEXT}Please review your progress for ${YELLOW_TEXT}TASK 1 & TASK 2${RESET_FORMAT} and then press Y to continue.${RESET_FORMAT}"
        else
            echo
            echo "${MAGENTA_TEXT}${BOLD_TEXT}Invalid input. Please type Y or N.${RESET_FORMAT}"
        fi
    done
}

# Call function to check progress before proceeding
check_progress

# Step 14: Delete Compute Instance
echo "${BLUE_TEXT}${BOLD_TEXT}Step 14: Deleting Compute Instance...${RESET_FORMAT}"
echo "${CYAN_TEXT}The Compute Engine instance created earlier will now be deleted.${RESET_FORMAT}"
gcloud compute instances delete instance-1 --zone=$ZONE --quiet

echo

# Completion Message
echo
echo "${GREEN_TEXT}${BOLD_TEXT}=======================================================${RESET_FORMAT}"
echo "${GREEN_TEXT}${BOLD_TEXT}              LAB COMPLETED SUCCESSFULLY!              ${RESET_FORMAT}"
echo "${GREEN_TEXT}${BOLD_TEXT}=======================================================${RESET_FORMAT}"
echo ""
echo -e "${RED_TEXT}${BOLD_TEXT}Subscribe to my Channel (Arcade Crew):${RESET_FORMAT} ${BLUE_TEXT}${BOLD_TEXT}https://www.youtube.com/@Arcade61432${RESET_FORMAT}"
echo
