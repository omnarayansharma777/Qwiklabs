
## Task 1: Create and Manage Service Accounts

### Create a Service Account

```sh
gcloud iam service-accounts create UNIQUE_IDENTIFIER --display-name "DISPLAY_NAME"
```

### Grant IAM Policy Binding to the Service Account

```sh
gcloud projects add-iam-policy-binding PROJECT_ID --member serviceAccount:UNIQUE_IDENTIFIER@PROJECT_ID.iam.gserviceaccount.com --role roles/editor
```

## Task 2: Use Client Libraries to Access BigQuery

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

### Create the Example Python File

Create a file named `query.py` with the following content:

```python
from google.auth import compute_engine
from google.cloud import bigquery

credentials = compute_engine.Credentials(
    service_account_email='YOUR_SERVICE_ACCOUNT')

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
    credentials=credentials)
print(client.query(query).to_dataframe())
```

Replace `'YOUR_SERVICE_ACCOUNT'` and `'YOUR_PROJECT_ID'` with the appropriate values.

```sh
sed -i -e "s/YOUR_PROJECT_ID/$(gcloud config get-value project)/g" query.py
```

Run the script:

```sh
python3 query.py
```
