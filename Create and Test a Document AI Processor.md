### ⚠️ Disclaimer
- **This script and guide are provided for  the educational purposes to help you understand the lab services and boost your career. Before using the script, please open and review it to familiarize yourself with Google Cloud services. Ensure that you follow 'Qwiklabs' terms of service and YouTube’s community guidelines. The goal is to enhance your learning experience, not to bypass it.**

### ©Credit
- **DM for credit or removal request (no copyright intended) ©All rights and credits for the original content belong to Google Cloud [Google Cloud Skill Boost website](https://www.cloudskillsboost.google/)** 🙏

```

gcloud auth list

gcloud services enable documentai.googleapis.com --project=$DEVSHELL_PROJECT_ID

export PROJECT_ID=$(gcloud config get-value project)

echo ""

echo -e "\033[1;33mCreate an "form-parser" Processor:\033[0m \033[1;34mhttps://console.cloud.google.com/ai/document-ai/processor-library?inv=1&invt=Ab2Kyg&project=$DEVSHELL_PROJECT_ID\033[0m"

echo ""

echo -e "\033[1;33mClick here to Download the form.pdf:\033[0m \033[1;34mhttps://storage.googleapis.com/cloud-training/document-ai/generic/form.pdf\033[0m"

echo ""

export ZONE=$(gcloud compute instances list document-ai-dev --format='csv[no-heading](zone)')

gcloud compute ssh --zone "$ZONE" "document-ai-dev" --project "$DEVSHELL_PROJECT_ID" --quiet

```
---

```


# Set text styles
YELLOW=$(tput setaf 3)
BOLD=$(tput bold)
RESET=$(tput sgr0)

read -p "${YELLOW}${BOLD}Enter the PROCESSOR_ID: ${RESET}" PROCESSOR_ID

echo Your processor ID is:$PROCESSOR_ID

sudo apt-get update && sudo apt-get install jq -y && sudo apt-get install python3-pip -y

export PROJECT_ID=$(gcloud config get-value core/project)

export SA_NAME="document-ai-service-account"
gcloud iam service-accounts create $SA_NAME --display-name $SA_NAME

gcloud projects add-iam-policy-binding ${PROJECT_ID} \
--member="serviceAccount:$SA_NAME@${PROJECT_ID}.iam.gserviceaccount.com" \
--role="roles/documentai.apiUser"


gcloud iam service-accounts keys create key.json \
--iam-account  $SA_NAME@${PROJECT_ID}.iam.gserviceaccount.com


export GOOGLE_APPLICATION_CREDENTIALS="$PWD/key.json"
echo $GOOGLE_APPLICATION_CREDENTIALS

gsutil cp gs://cloud-training/gsp924/health-intake-form.pdf .


echo '{"inlineDocument": {"mimeType": "application/pdf","content": "' > temp.json
base64 health-intake-form.pdf >> temp.json
echo '"}}' >> temp.json
cat temp.json | tr -d \\n > request.json

# Sleep for 65 seconds.
sleep 65


export LOCATION="us"
export PROJECT_ID=$(gcloud config get-value core/project)
curl -X POST \
-H "Authorization: Bearer "$(gcloud auth application-default print-access-token) \
-H "Content-Type: application/json; charset=utf-8" \
-d @request.json \
https://${LOCATION}-documentai.googleapis.com/v1beta3/projects/${PROJECT_ID}/locations/${LOCATION}/processors/${PROCESSOR_ID}:process > output.json

# Sleep for 65 seconds.
sleep 65

cat output.json | jq -r ".document.text"

gsutil cp gs://cloud-training/gsp924/synchronous_doc_ai.py .

python3 -m pip install --upgrade google-cloud-documentai google-cloud-storage prettytable

export PROJECT_ID=$(gcloud config get-value core/project)
export GOOGLE_APPLICATION_CREDENTIALS="$PWD/key.json"

python3 synchronous_doc_ai.py \
--project_id=$PROJECT_ID \
--processor_id=$PROCESSOR_ID \
--location=us \
--file_name=health-intake-form.pdf | tee results.txt

export LOCATION="us"
export PROJECT_ID=$(gcloud config get-value core/project)
curl -X POST \
-H "Authorization: Bearer "$(gcloud auth application-default print-access-token) \
-H "Content-Type: application/json; charset=utf-8" \
-d @request.json \
https://${LOCATION}-documentai.googleapis.com/v1beta3/projects/${PROJECT_ID}/locations/${LOCATION}/processors/${PROCESSOR_ID}:process > output.json

```
