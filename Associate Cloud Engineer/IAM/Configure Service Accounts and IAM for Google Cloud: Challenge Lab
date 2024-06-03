Sure! Here's the Markdown for the GitHub `README.md` file, including all the tasks described:

```markdown
# Google Cloud Platform Setup Guide

This guide provides step-by-step instructions to perform various tasks using Google Cloud Platform (GCP) and the `gcloud` CLI.

## Task 1: Create a Service Account

First, initialize the gcloud CLI:

```sh
gcloud init --no-launch-browser
```

Create a new service account with a unique identifier and a display name:

```sh
gcloud iam service-accounts create UNIQUE_IDENTIFIER --display-name "DISPLAY_NAME"
```

## Task 2: Grant IAM Permissions to the Service Account

Retrieve the email of the service account and the current project ID:

```sh
SA=$(gcloud iam service-accounts list --format="value(email)" --filter "displayName=devops")
PROJECT_ID=$(gcloud config get project)
```

Grant the required IAM roles to the service account:

```sh
gcloud projects add-iam-policy-binding $PROJECT_ID --member serviceAccount:$SA --role=roles/iam.serviceAccountUser
gcloud projects add-iam-policy-binding $PROJECT_ID --member serviceAccount:$SA --role=roles/compute.instanceAdmin
```

## Task 3: Create a Compute Instance with the Service Account Attached

Create a new compute instance in the specified zone with the service account attached:

```sh
gcloud compute instances create vm-2 --zone=us-east4-b --machine-type=e2-standard-2 --service-account $SA --scopes "https://www.googleapis.com/auth/compute"
```

## Task 4: Create a Custom Role Using a YAML File

Create a YAML file named `role-definition.yaml` with the following content:

```yaml
title: "Role Custom"
description: "a custom IAM role"
stage: "ALPHA"
includedPermissions:
- cloudsql.instances.connect
- cloudsql.instances.get
```

Create the custom role in your project:

```sh
gcloud iam roles create editor --project $PROJECT_ID --file role-definition.yaml
```

## Task 5: Use the Client Libraries to Access BigQuery from a Service Account

Install the necessary packages:

```sh
sudo apt-get update
sudo apt-get install -y git python3-pip
pip3 install --upgrade pip
pip3 install google-cloud-bigquery
pip3 install pyarrow
pip3 install pandas
pip3 install db-dtypes
```

Create a Python script named `query.py` with the following content:

```python
from google.auth import compute_engine
from google.cloud import bigquery

credentials = compute_engine.Credentials(
    service_account_email='YOUR_SERVICE_ACCOUNT'
)

query = '''
SELECT name, SUM(number) as total_people
FROM `bigquery-public-data.usa_names.usa_1910_2013`
WHERE state = 'TX'
GROUP BY name, state
ORDER BY total_people DESC
LIMIT 20
'''

client = bigquery.Client(
    project='YOUR_PROJECT_ID',
    credentials=credentials
)

print(client.query(query).to_dataframe())
```

Replace `YOUR_SERVICE_ACCOUNT` and `YOUR_PROJECT_ID` with the appropriate values using `sed`:

```sh
sed -i -e "s/YOUR_SERVICE_ACCOUNT/bigquery-qwiklab@$(gcloud config get-value project).iam.gserviceaccount.com/g" query.py
```

Run the script:

```sh
python3 query.py
```

## Conclusion

You have now completed all the tasks required to create and manage resources on Google Cloud Platform using the `gcloud` CLI and Python client libraries.
```

This README.md file provides a clear and detailed guide to setting up and managing Google Cloud resources, including creating a service account, granting IAM permissions, creating a compute instance, creating a custom role, and using the BigQuery client library with a service account.
