###⚠️ Disclaimer
This script and guide are provided for the educational purposes to help you understand the lab services and boost your career. Before using the script, please open and review it to familiarize yourself with Google Cloud services. Ensure that you follow 'Qwiklabs' terms of service and YouTube’s community guidelines. The goal is to enhance your learning experience, not to bypass it.

```


gcloud auth list

export REGION=$(gcloud compute project-info describe --format="value(commonInstanceMetadata.items[google-compute-default-region])")

export PROJECT_ID=$(gcloud config get-value project)

gcloud services enable dataplex.googleapis.com datacatalog.googleapis.com

gcloud dataplex lakes create customer-info-lake --location=$REGION --display-name="Techcps Info Lake"

gcloud dataplex zones create customer-raw-zone --location=$REGION --display-name="Techcps Raw Zone" --lake=customer-info-lake --type=RAW --resource-location-type=SINGLE_REGION

gcloud dataplex assets create customer-online-sessions \
  --location=$REGION \
  --display-name="Techcps Online Sessions" \
  --lake=customer-info-lake \
  --zone=customer-raw-zone \
  --resource-type=STORAGE_BUCKET \
  --resource-name=projects/$PROJECT_ID/buckets/$PROJECT_ID-bucket


echo
echo -e "\033[1;33mClick Grant access\033[0m \033[1;34mhttps://console.cloud.google.com/dataplex/secure?inv=1&invt=AbyNQg&project=$PROJECT_ID\033[0m"
echo

```
