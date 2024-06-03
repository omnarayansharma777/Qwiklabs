Sure! Here is a well-formatted `README.md` file for your Google Cloud Platform Identity and Access Management (IAM) guide:

```markdown
# Google Cloud Platform Identity and Access Management (IAM)

## Overview

1. **IAM** lets you access cloud resources that you actually need and prevents access to other resources.
2. **IAM** follows the least privilege principle, meaning nobody should have more permissions than they actually need.
3. Do not give permissions directly to end users. Instead, group permissions into roles and grant them to authenticated principals (members).
4. **Authentication** (verifying user identity) -> **Authorization** (granting resource access).

## Principals

1. Google Accounts
2. Service Accounts
3. Google Groups
4. Google Workspace Accounts (formerly G Suite Accounts)
5. Cloud Identity Domain
6. All Authenticated Users
7. All Users

## Roles

1. **Basic**
    - Viewer
    - Editor
    - Owner
    - Billing Admin
2. **Predefined**
3. **Custom**

## Service Accounts

You can treat a service account either as a resource or as an identity.

### Create a Service Account

```sh
gcloud iam service-accounts create UNIQUE_IDENTIFIER --display-name "DISPLAY_NAME"
```

### Grant IAM Policy Binding to the Service Account

```sh
gcloud projects add-iam-policy-binding PROJECT_ID --member serviceAccount:UNIQUE_IDENTIFIER@PROJECT_ID.iam.gserviceaccount.com --role roles/editor
```

## Use Client Libraries to Access BigQuery

### Install Necessary Packages

```sh
sudo apt-get update
sudo apt-get install -y git python3-pip
pip3 install --upgrade pip
pip3 install google-cloud-bigquery
pip3 install pyarrow
pip3 install pandas
pip3 install db-dtypes
```

### Create and Run the Python Script

Create a Python script named `query.py` with the following content:

```python
from google.auth import compute_engine
from google.cloud import bigquery

credentials = compute_engine.Credentials(
    service_account_email='YOUR_SERVICE_ACCOUNT'
)

query = '''
SELECT
  year,
  COUNT(1) as num_babies
FROM
  publicdata.samples.natality
WHERE
  year > 2000
GROUP BY
  year
'''

client = bigquery.Client(
    project='YOUR_PROJECT_ID',
    credentials=credentials
)
print(client.query(query).to_dataframe())
```

Replace `YOUR_SERVICE_ACCOUNT` and `YOUR_PROJECT_ID` with the appropriate values.

Run the script:

```sh
python3 query.py
```

## Configuration

### Verify the Zone Written to the Configuration File

```sh
cat ~/.config/gcloud/configurations/config_default
```

### Initialize gcloud CLI

```sh
gcloud init --no-launch-browser
```

### Change Back to Your First User's Configuration

```sh
gcloud config configurations activate default[configuration_name]
```

## IAM Roles Management

### View All Roles

```sh
gcloud iam roles list | grep "name:"
gcloud iam roles describe roles/compute.instanceAdmin
```

### Switch to Another User's Configuration

```sh
gcloud config configurations activate user2
```

### Add IAM Policy Binding for a User

```sh
gcloud projects add-iam-policy-binding $PROJECTID2 --member user:$USERID2 --role=projects/$PROJECTID2/roles/devops
```

### Add IAM Policy Binding for a Service Account

```sh
gcloud projects add-iam-policy-binding $PROJECTID2 --member serviceAccount:$SA --role=roles/compute.instanceAdmin
```

### List Testable Permissions

```sh
gcloud iam list-testable-permissions //cloudresourcemanager.googleapis.com/projects/$DEVSHELL_PROJECT_ID
```

### Describe a Role

```sh
gcloud iam roles describe [ROLE_NAME]
```

### List Grantable Roles

```sh
gcloud iam list-grantable-roles //cloudresourcemanager.googleapis.com/projects/$DEVSHELL_PROJECT_ID
```

## Custom Roles

### Create Custom Roles Using a YAML File

Create a file named `role-definition.yaml` with the following content:

```yaml
title: "Role Editor"
description: "Edit access for App Versions"
stage: "ALPHA"
includedPermissions:
- appengine.versions.create
- appengine.versions.delete
```

Create the custom role:

```sh
gcloud iam roles create editor --project $DEVSHELL_PROJECT_ID --file role-definition.yaml
```

### Create Custom Roles Using Flags

```sh
gcloud iam roles create viewer --project $DEVSHELL_PROJECT_ID \
--title "Role Viewer" --description "Custom role description." \
--permissions compute.instances.get,compute.instances.list --stage ALPHA
```

### List Custom Roles

```sh
gcloud iam roles list --project $DEVSHELL_PROJECT_ID
```

## Updating Roles

### Describe the Current State of a Role

```sh
gcloud iam roles describe [ROLE_ID] --project $DEVSHELL_PROJECT_ID
```

### Update a Role Using a File

Create a file named `new-role-definition.yaml` with the updated role definition and run:

```sh
gcloud iam roles update [ROLE_ID] --project $DEVSHELL_PROJECT_ID --file new-role-definition.yaml
```

### Update a Role Using Flags

```sh
gcloud iam roles update viewer --project $DEVSHELL_PROJECT_ID \
--add-permissions storage.buckets.get,storage.buckets.list \
--remove-permissions --stage DISABLED
```

### Restore a Deleted Role

Deleted roles are in a DISABLED state and can be restored within a 7-day window.

```sh
gcloud iam roles update [ROLE_ID] --project $DEVSHELL_PROJECT_ID --stage ENABLED
```
