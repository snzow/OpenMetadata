---
title: BigQuery
slug: /connectors/database/bigquery
---

# BigQuery

{% multiTablesWrapper %}

| Feature            | Status                       |
| :----------------- | :--------------------------- |
| Stage              | PROD                         |
| Metadata           | {% icon iconName="check" /%} |
| Query Usage        | {% icon iconName="check" /%} |
| Data Profiler      | {% icon iconName="check" /%} |
| Data Quality       | {% icon iconName="check" /%} |
| Lineage            | {% icon iconName="check" /%}          |
| DBT                | {% icon iconName="check" /%} |
| Supported Versions | --                           |

| Feature      | Status                       |
| :----------- | :--------------------------- |
| Lineage      | Partially via Views          |
| Table-level  | {% icon iconName="check" /%} |
| Column-level | {% icon iconName="check" /%} |

{% /multiTablesWrapper %}


In this section, we provide guides and references to use the BigQuery connector.

Configure and schedule BigQuery metadata and profiler workflows from the OpenMetadata UI:

- [Requirements](#requirements)
- [Metadata Ingestion](#metadata-ingestion)
- [Query Usage](#query-usage)
- [Data Profiler](#data-profiler)
- [Data Quality](#data-quality)
- [Lineage](#lineage)
- [dbt Integration](#dbt-integration)

If you don't want to use the OpenMetadata Ingestion container to configure the workflows via the UI, then you can check
the following docs to connect using Airflow SDK or with the CLI.

{% tilesContainer %}

{% tile
    title="Ingest with Airflow"
    description="Configure the ingestion using Airflow SDK"
    link="/connectors/database/bigquery/airflow"
  / %}
{% tile
    title="Ingest with the CLI"
    description="Run a one-time ingestion using the metadata CLI"
    link="/connectors/database/bigquery/cli"
  / %}

{% /tilesContainer %}

## Requirements

{%inlineCallout icon="description" bold="OpenMetadata 0.12 or later" href="/deployment"%}
To deploy OpenMetadata, check the Deployment guides.
{%/inlineCallout%}

To run the Ingestion via the UI you'll need to use the OpenMetadata Ingestion Container, which comes shipped with
custom Airflow plugins to handle the workflow deployment.

### Data Catalog API Permissions 

- Go to [https://console.cloud.google.com/apis/library/datacatalog.googleapis.com](https://console.cloud.google.com/apis/library/datacatalog.googleapis.com)
- Select the `GCP Project ID` that you want to enable the `Data Catalog API` on.
- Click on `Enable API` which will enable the data catalog api on the respective project.

### GCP Permissions

To execute metadata extraction and usage workflow successfully the user or the service account should have enough access to fetch required data. Following table describes the minimum required permissions

{% multiTablesWrapper %}

| #    | GCP Permission                | Required For            |
| :--- | :---------------------------- | :---------------------- |
| 1    | bigquery.datasets.get         | Metadata Ingestion      |
| 2    | bigquery.tables.get           | Metadata Ingestion      |
| 3    | bigquery.tables.getData       | Metadata Ingestion      |
| 4    | bigquery.tables.list          | Metadata Ingestion      |
| 5    | resourcemanager.projects.get  | Metadata Ingestion      |
| 6    | bigquery.jobs.create          | Metadata Ingestion      |
| 7    | bigquery.jobs.listAll         | Metadata Ingestion      |
| 8    | datacatalog.taxonomies.get    | Fetch Policy Tags       |
| 9    | datacatalog.taxonomies.list   | Fetch Policy Tags       |
| 10   | bigquery.readsessions.create  | Bigquery Usage & Lineage Workflow |
| 11   | bigquery.readsessions.getData | Bigquery Usage & Lineage Workflow |

{% /multiTablesWrapper %}

{% tilesContainer %}
{% tile
icon="manage_accounts"
title="Create Custom GCP Role"
description="Checkout this documentation on how to create a custom role and assign it to the service account."
link="/connectors/database/bigquery/roles"
  / %}
{% /tilesContainer %}

## Metadata Ingestion

{% stepsContainer %}

{% step srNumber=1 %}

{% stepDescription title="1. Visit the Services Page" %}

The first step is ingesting the metadata from your sources. Under
Settings, you will find a Services link an external source system to
OpenMetadata. Once a service is created, it can be used to configure
metadata, usage, and profiler workflows.

To visit the Services page, select Services from the Settings menu.

{% /stepDescription %}

{% stepVisualInfo %}

{% image
src="/images/openmetadata/connectors/visit-database-service-page.png"
alt="Visit Services Page"
caption="Find Databases option on left panel of the settings page" /%}

{% /stepVisualInfo %}

{% /step %}

{% step srNumber=2 %}

{% stepDescription title="2. Create a New Service" %}

Click on the 'Add New Service' button to start the Service creation.

{% /stepDescription %}

{% stepVisualInfo %}

{% image
src="/images/openmetadata/connectors/create-database-service.png"
alt="Create a new service"
caption="Add a new Service from the Database Services page" /%}

{% /stepVisualInfo %}

{% /step %}

{% step srNumber=3 %}

{% stepDescription title="3. Select the Service Type" %}

Select BigQuery as the service type and click Next.

{% /stepDescription %}

{% stepVisualInfo %}

{% image
  src="/images/openmetadata/connectors/bigquery/select-service.png"
  alt="Select Service"
  caption="Select your service from the list" /%}

{% /stepVisualInfo %}

{% /step %}

{% step srNumber=4 %}

{% stepDescription title="4. Name and Describe your Service" %}

Provide a name and description for your service as illustrated below.

#### Service Name

OpenMetadata uniquely identifies services by their Service Name. Provide
a name that distinguishes your deployment from other services, including
the other {connector} services that you might be ingesting metadata
from.

{% /stepDescription %}

{% stepVisualInfo %}

{% image
  src="/images/openmetadata/connectors/bigquery/add-new-service.png"
  alt="Add New Service"
  caption="Provide a Name and description for your Service" /%}

{% /stepVisualInfo %}

{% /step %}


{% step srNumber=5 %}

{% stepDescription title="5. Configure the Service Connection" %}

In this step, we will configure the connection settings required for
this connector. Please follow the instructions below to ensure that
you've configured the connector to read from your bigquery service as
desired.

{% /stepDescription %}

{% stepVisualInfo %}

{% image
  src="/images/openmetadata/connectors/bigquery/service-connection.png"
  alt="Configure service connection"
  caption="Configure the service connection by filling the form" /%}

{% /stepVisualInfo %}

{% /step %}

{% extraContent parentTagName="stepsContainer" %}

#### Connection Options

- **Host and Port**: This is the BigQuery APIs URL.
- **Username (Optional)**: Specify the User to connect to BigQuery. It should have enough privileges to read all the metadata.
- **Project ID(Optional)**: The BigQuery Project ID is required only if the credentials path is being used instead of values. 
To add multiple Project ID, select `Multiple Project ID` under `Project ID` option.
- **GCS Credentials**: We support two ways of authenticating to BigQuery:
    1. Passing the raw credential values provided by BigQuery. This requires us to provide the following information, all provided by BigQuery:
       - Credentials type, e.g., `service_account`
       - Project ID
          - Single Project ID: To add single ProjectID
          - Multiple Project ID: To add multiple ProjectID
       - Private Key ID
       - Private Key
       - Client Email
       - Client ID
       - Auth URI, https://accounts.google.com/o/oauth2/auth by defaul
       - Token URI, https://oauth2.googleapis.com/token by default
       - Authentication Provider X509 Certificate URL, https://www.googleapis.com/oauth2/v1/certs by default
       - Client X509 Certificate U
    2. Passing a local file path that contains the credentials:
       - GCS Credentials Path
- **Enable Policy Tag Import (Optional)**: Mark as 'True' to enable importing policy tags from BigQuery to OpenMetadata.
- **Classification Name (Optional)**: If the Tag import is enabled, the name of the Classification will be created at OpenMetadata.
- **Database (Optional)**: The database of the data source is an optional parameter, if you would like to restrict the metadata reading to a single database. If left blank, OpenMetadata ingestion attempts to scan all the databases.
- **Connection Options (Optional)**: Enter the details for any additional connection options that can be sent to BigQuery during the connection. These details must be added as Key-Value pairs.
- **Connection Arguments (Optional)**: Enter the details for any additional connection arguments such as security or protocol configs that can be sent to BigQuery during the connection. These details must be added as Key-Value pairs. 
  - In case you are using Single-Sign-On (SSO) for authentication, add the `authenticator` details in the Connection Arguments as a Key-Value pair as follows: `"authenticator" : "sso_login_url"`
  - In case you authenticate with SSO using an external browser popup, then add the `authenticator` details in the Connection Arguments as a Key-Value pair as follows: `"authenticator" : "externalbrowser"`

If you want to use [ADC authentication](https://cloud.google.com/docs/authentication#adc) for BigQuery you can just leave
the GCS credentials empty. This is why they are not marked as required.

{% /extraContent %}

{% step srNumber=6 %}

{% stepDescription title="6. Test the Connection" %}

Once the credentials have been added, click on `Test Connection` and Save
the changes.

{% /stepDescription %}

{% stepVisualInfo %}

{% image
  src="/images/openmetadata/connectors/test-connection.png"
  alt="Test Connection"
  caption="Test the connection and save the Service" /%}

{% /stepVisualInfo %}

{% /step %}

{% step srNumber=7 %}

{% stepDescription title="7. Configure Metadata Ingestion" %}

In this step we will configure the metadata ingestion pipeline,
Please follow the instructions below

{% /stepDescription %}

{% stepVisualInfo %}

{% image
src="/images/openmetadata/connectors/configure-metadata-ingestion-database.png"
alt="Configure Metadata Ingestion"
caption="Configure Metadata Ingestion Page" /%}

{% /stepVisualInfo %}

{% /step %}

{% extraContent parentTagName="stepsContainer" %}

#### Metadata Ingestion Options

- **Name**: This field refers to the name of ingestion pipeline, you can customize the name or use the generated name.
- **Database Filter Pattern (Optional)**: Use to database filter patterns to control whether or not to include database as part of metadata ingestion.
  - **Include**: Explicitly include databases by adding a list of comma-separated regular expressions to the Include field. OpenMetadata will include all databases with names matching one or more of the supplied regular expressions. All other databases will be excluded.
  - **Exclude**: Explicitly exclude databases by adding a list of comma-separated regular expressions to the Exclude field. OpenMetadata will exclude all databases with names matching one or more of the supplied regular expressions. All other databases will be included.
- **Schema Filter Pattern (Optional)**: Use to schema filter patterns to control whether or not to include schemas as part of metadata ingestion.
  - **Include**: Explicitly include schemas by adding a list of comma-separated regular expressions to the Include field. OpenMetadata will include all schemas with names matching one or more of the supplied regular expressions. All other schemas will be excluded.
  - **Exclude**: Explicitly exclude schemas by adding a list of comma-separated regular expressions to the Exclude field. OpenMetadata will exclude all schemas with names matching one or more of the supplied regular expressions. All other schemas will be included.
- **Table Filter Pattern (Optional)**: Use to table filter patterns to control whether or not to include tables as part of metadata ingestion.
  - **Include**: Explicitly include tables by adding a list of comma-separated regular expressions to the Include field. OpenMetadata will include all tables with names matching one or more of the supplied regular expressions. All other tables will be excluded.
  - **Exclude**: Explicitly exclude tables by adding a list of comma-separated regular expressions to the Exclude field. OpenMetadata will exclude all tables with names matching one or more of the supplied regular expressions. All other tables will be included.
- **Include views (toggle)**: Set the Include views toggle to control whether or not to include views as part of metadata ingestion.
- **Include tags (toggle)**: Set the Include tags toggle to control whether or not to include tags as part of metadata ingestion.
- **Enable Debug Log (toggle)**: Set the Enable Debug Log toggle to set the default log level to debug, these logs can be viewed later in Airflow.
- **Auto Tag PII(toggle)**: Auto PII tagging checks for column name to mark PII Sensitive/NonSensitive tag
- **Mark Deleted Tables (toggle)**: Set the Mark Deleted Tables toggle to flag tables as soft-deleted if they are not present anymore in the source system.
- **Mark Deleted Tables from Filter Only (toggle)**: Set the Mark Deleted Tables from Filter Only toggle to flag tables as soft-deleted if they are not present anymore within the filtered schema or database only. This flag is useful when you have more than one ingestion pipelines. For example if you have a schema

{% /extraContent %}

{% step srNumber=8 %}

{% stepDescription title="8. Schedule the Ingestion and Deploy" %}

Scheduling can be set up at an hourly, daily, or weekly cadence. The
timezone is in UTC. Select a Start Date to schedule for ingestion. It is
optional to add an End Date.

Review your configuration settings. If they match what you intended,
click Deploy to create the service and schedule metadata ingestion.

If something doesn't look right, click the Back button to return to the
appropriate step and change the settings as needed.

After configuring the workflow, you can click on Deploy to create the
pipeline.

{% /stepDescription %}

{% stepVisualInfo %}

{% image
src="/images/openmetadata/connectors/schedule.png"
alt="Schedule the Workflow"
caption="Schedule the Ingestion Pipeline and Deploy" /%}

{% /stepVisualInfo %}

{% /step %}

{% step srNumber=9 %}

{% stepDescription title="9. View the Ingestion Pipeline" %}

Once the workflow has been successfully deployed, you can view the
Ingestion Pipeline running from the Service Page.

{% /stepDescription %}

{% stepVisualInfo %}

{% image
src="/images/openmetadata/connectors/view-ingestion-pipeline.png"
alt="View Ingestion Pipeline"
caption="View the Ingestion Pipeline from the Service Page" /%}

{% /stepVisualInfo %}

{% /step %}

{% /stepsContainer %}

## Troubleshooting

 ### Workflow Deployment Error

If there were any errors during the workflow deployment process, the
Ingestion Pipeline Entity will still be created, but no workflow will be
present in the Ingestion container.

- You can then edit the Ingestion Pipeline and Deploy it again.

- From the Connection tab, you can also Edit the Service if needed.

{% image
src="/images/openmetadata/connectors/workflow-deployment-error.png"
alt="Workflow Deployment Error"
caption="Edit and Deploy the Ingestion Pipeline" /%}

## Related

{% tilesContainer %}

{% tile
  title="Usage Workflow"
  description="Learn more about how to configure the Usage Workflow to ingest Query information from the UI."
  link="/connectors/ingestion/workflows/usage" /%}

{% tile
  title="Lineage Workflow"
  description="Learn more about how to configure the Lineage from the UI."
  link="/connectors/ingestion/workflows/lineage" /%}

{% tile
  title="Profiler Workflow"
  description="Learn more about how to configure the Data Profiler from the UI."
  link="/connectors/ingestion/workflows/profiler" /%}

{% tile
  title="Data Quality Workflow"
  description="Learn more about how to configure the Data Quality tests from the UI."
  link="/connectors/ingestion/workflows/data-quality" /%}

{% tile
  icon="mediation"
  title="dbt Integration"
  description="Learn more about how to ingest dbt models' definitions and their lineage."
  link="/connectors/ingestion/workflows/dbt" /%}

{% /tilesContainer %}
