## Task 1: Create a Service Account

### Step 1: Initialize the gcloud CLI

```sh
gcloud init --no-launch-browser
```

### Step 2: Create a New Service Account

```sh
gcloud iam service-accounts create UNIQUE_IDENTIFIER --display-name "DISPLAY_NAME"
```

## Task 2: Grant IAM Permissions to the Service Account

### Step 1: Retrieve the Email of the Service Account and the Current Project ID

```sh
SA=$(gcloud iam service-accounts list --format="value(email)" --filter "displayName=devops")
PROJECT_ID=$(gcloud config get project)
```

### Step 2: Grant the Required IAM Roles to the Service Account

```sh
gcloud projects add-iam-policy-binding $PROJECT_ID --member serviceAccount:$SA --role=roles/iam.serviceAccountUser
gcloud projects add-iam-policy-binding $PROJECT_ID --member serviceAccount:$SA --role=roles/compute.instanceAdmin
```

## Task 3: Create a Compute Instance with the Service Account Attached

### Step 1: Create a New Compute Instance

```sh
gcloud compute instances create vm-2 --zone=us-east4-b --machine-type=e2-standard-2 --service-account $SA --scopes "https://www.googleapis.com/auth/compute"
```

## Task 4: Create a Custom Role Using a YAML File

### Step 1: Create the YAML File

Create a file named `role-definition.yaml` with the following content:

```yaml
title: "Role Custom"
description: "a custom IAM role"
stage: "ALPHA"
includedPermissions:
- cloudsql.instances.connect
- cloudsql.instances.get
```

### Step 2: Create the Custom Role in Your Project

```sh
gcloud iam roles create editor --project $PROJECT_ID --file role-definition.yaml
```

## Task 5: Use the Client Libraries to Access BigQuery from a Service Account

### Step 1: Install the Necessary Packages

```sh
sudo apt-get update
sudo apt-get install -y git python3-pip
pip3 install --upgrade pip
pip3 install google-cloud-bigquery
pip3 install pyarrow
pip3 install pandas
pip3 install db-dtypes
```

### Step 2: Create a Python Script

Create a file named `query.py` with the following content:

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

### Step 3: Replace Placeholder Values

Replace `YOUR_SERVICE_ACCOUNT` and `YOUR_PROJECT_ID` with the appropriate values using `sed`:

```sh
sed -i -e "s/YOUR_SERVICE_ACCOUNT/bigquery-qwiklab@$(gcloud config get-value project).iam.gserviceaccount.com/g" query.py
```

### Step 4: Run the Script

```sh
python3 query.py
```
