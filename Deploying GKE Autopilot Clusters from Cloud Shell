#!/bin/bash

# Define color variables
YELLOW_TEXT=$'\033[0;33m'
MAGENTA_TEXT=$'\033[0;35m'
NO_COLOR=$'\033[0m'
GREEN_TEXT=$'\033[0;32m'
RED_TEXT=$'\033[0;31m'
CYAN_TEXT=$'\033[0;36m'
BOLD_TEXT=$'\033[1m'
RESET_FORMAT=$'\033[0m'
BLUE_TEXT=$'\033[0;34m'

# Start of the script
echo
echo "${CYAN_TEXT}${BOLD_TEXT}Starting the process...${RESET_FORMAT}"
echo

# Prompt user for region
echo "${YELLOW_TEXT}${BOLD_TEXT}Enter REGION:${RESET_FORMAT}"
read -r REGION
echo "${GREEN_TEXT}You have selected region: $REGION${RESET_FORMAT}"

export my_region=$REGION
export my_cluster=autopilot-cluster-1

# Create GKE cluster
echo "${MAGENTA_TEXT}${BOLD_TEXT}Creating an Autopilot GKE cluster...${RESET_FORMAT}"
gcloud container clusters create-auto $my_cluster --region $my_region

echo "${CYAN_TEXT}${BOLD_TEXT}Fetching cluster credentials...${RESET_FORMAT}"
gcloud container clusters get-credentials $my_cluster --region $my_region

echo "${BLUE_TEXT}${BOLD_TEXT}Displaying Kubernetes configuration...${RESET_FORMAT}"
kubectl config view

kubectl cluster-info
kubectl config current-context
kubectl config get-contexts

echo "${YELLOW_TEXT}${BOLD_TEXT}Switching to the correct Kubernetes context...${RESET_FORMAT}"
kubectl config use-context gke_${DEVSHELL_PROJECT_ID}_us-central1_autopilot-cluster-1

# Deploy nginx
echo "${GREEN_TEXT}${BOLD_TEXT}Deploying Nginx...${RESET_FORMAT}"
kubectl create deployment --image nginx nginx-1

sleep 30

echo "${CYAN_TEXT}${BOLD_TEXT}Fetching Nginx pod name...${RESET_FORMAT}"
my_nginx_pod=$(kubectl get pods -o=jsonpath='{.items[0].metadata.name}')

echo "${BLUE_TEXT}${BOLD_TEXT}Displaying node resource usage...${RESET_FORMAT}"
kubectl top nodes

# Create a test HTML file
echo "${MAGENTA_TEXT}${BOLD_TEXT}Creating a test HTML file...${RESET_FORMAT}"
cat > test.html <<EOF_END
<html> <header><title>This is title</title></header>
<body> Hello world </body>
</html>
EOF_END

# Copy test.html to nginx pod
echo "${CYAN_TEXT}${BOLD_TEXT}Copying test.html to Nginx pod...${RESET_FORMAT}"
kubectl cp ~/test.html $my_nginx_pod:/usr/share/nginx/html/test.html

# Expose the pod
echo "${YELLOW_TEXT}${BOLD_TEXT}Exposing the Nginx pod...${RESET_FORMAT}"
kubectl expose pod $my_nginx_pod --port 80 --type LoadBalancer

echo "${GREEN_TEXT}${BOLD_TEXT}Fetching service details...${RESET_FORMAT}"
kubectl get services

# Clone the training repository
echo "${BLUE_TEXT}${BOLD_TEXT}Cloning the training-data-analyst repository...${RESET_FORMAT}"
git clone https://github.com/GoogleCloudPlatform/training-data-analyst

ln -s ~/training-data-analyst/courses/ak8s/v1.1 ~/ak8s
cd ~/ak8s/GKE_Shell/

# Apply new nginx pod configuration
echo "${MAGENTA_TEXT}${BOLD_TEXT}Applying new Nginx pod configuration...${RESET_FORMAT}"
kubectl apply -f ./new-nginx-pod.yaml

rm new-nginx-pod.yaml

echo "${CYAN_TEXT}${BOLD_TEXT}Creating a new Nginx pod YAML file...${RESET_FORMAT}"
cat > new-nginx-pod.yaml <<EOF_END
apiVersion: v1
kind: Pod
metadata:
  name: new-nginx
  labels:
    name: new-nginx
spec:
  containers:
  - name: new-nginx
    image: nginx
    ports:
    - containerPort: 80
EOF_END

kubectl apply -f ./new-nginx-pod.yaml
echo



# Safely delete the script if it exists
SCRIPT_NAME="arcadecrew.sh"
if [ -f "$SCRIPT_NAME" ]; then
    echo -e "${BOLD_TEXT}${RED_TEXT}Deleting the script ($SCRIPT_NAME) for safety purposes...${RESET_FORMAT}${NO_COLOR}"
    rm -- "$SCRIPT_NAME"
fi

echo
echo
# Completion message
echo -e "${MAGENTA_TEXT}${BOLD_TEXT}Lab Completed Successfully!${RESET_FORMAT}"
echo -e "${GREEN_TEXT}${BOLD_TEXT}Subscribe our Channel:${RESET_FORMAT} ${BLUE_TEXT}${BOLD_TEXT}https://www.youtube.com/@Arcade61432${RESET_FORMAT}"
echo
