---
title: Tableau
slug: /connectors/dashboard/tableau
---

# Tableau

In this section, we provide guides and references to use the Tableau connector.

Configure and schedule Tableau metadata and profiler workflows from the OpenMetadata UI:

- [Requirements](#requirements)
- [Metadata Ingestion](#metadata-ingestion)

If you don't want to use the OpenMetadata Ingestion container to configure the workflows via the UI, then you can check
the following docs to connect using Airflow SDK or with the CLI.

{% tilesContainer %}

{% tile
    title="Ingest with Airflow"
    description="Configure the ingestion using Airflow SDK"
    link="/connectors/dashboard/tableau/airflow"
  / %}
{% tile
    title="Ingest with the CLI"
    description="Run a one-time ingestion using the metadata CLI"
    link="/connectors/dashboard/tableau/cli"
  / %}

{% /tilesContainer %}

## Requirements

To ingest tableau metadata, minimum `Site Role: Viewer` is requried for the tableau user.

{%inlineCallout icon="description" bold="OpenMetadata 0.12 or later" href="/deployment"%}
To deploy OpenMetadata, check the Deployment guides.
{%/inlineCallout%}

To run the Ingestion via the UI you'll need to use the OpenMetadata Ingestion Container, which comes shipped with
custom Airflow plugins to handle the workflow deployment.

To create lineage between tableau dashboard and any database service via the queries provided from Tableau Metadata API, please enable the Tableau Metadata API for your tableau server.
For more information on enabling the Tableau Metadata APIs follow the link [here](https://help.tableau.com/current/api/metadata_api/en-us/docs/meta_api_start.html)

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
src="/images/openmetadata/connectors/visit-services.png"
alt="Visit Services Page"
caption="Find Dashboard option on left panel of the settings page" /%}

{% /stepVisualInfo %}

{% /step %}

{% step srNumber=2 %}

{% stepDescription title="2. Create a New Service" %}

Click on the 'Add New Service' button to start the Service creation.

{% /stepDescription %}

{% stepVisualInfo %}

{% image
src="/images/openmetadata/connectors/create-service.png"
alt="Create a new service"
caption="Add a new Service from the Dashboard Services page" /%}

{% /stepVisualInfo %}

{% /step %}



{% step srNumber=3 %}

{% stepDescription title="3. Select the Service Type" %}

Select Tableau as the service type and click Next.

{% /stepDescription %}

{% stepVisualInfo %}

{% image
  src="/images/openmetadata/connectors/tableau/select-service.png"
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
  src="/images/openmetadata/connectors/tableau/add-new-service.png"
  alt="Add New Service"
  caption="Provide a Name and description for your Service" /%}

{% /stepVisualInfo %}

{% /step %}

{% step srNumber=5 %}

{% stepDescription title="5. Configure the Service Connection" %}

In this step, we will configure the connection settings required for
this connector. Please follow the instructions below to ensure that
you've configured the connector to read from your tableau service as
desired.


#### 1. Service Connection for Tableau Cloud

If you're connecting to a cloud Tableau instance, add the `Site Name` and `Site Url` with your site name.

#### 2. Service Connection for a default tableau site
For a default tableau site `Site Name` and `Site Url` fields should be kept empty as shown in the below image 


#### 3. Service Connection for a non-default tableau site
For a non-default tableau site `Site Name` and `Site Url` fields are required.

**Note**: If `https://xxx.tableau.com/#/site/sitename/home` represents the homepage url for your tableau site, the `sitename` from the url should be entered in the `Site Name` and `Site Url` fields.


{% /stepDescription %}

{% stepVisualInfo %}

{% image
  src="/images/openmetadata/connectors/tableau/service-connection-default-site.png"
  alt="Configure service connection"
  caption="Configure the service connection for default site by filling the form" /%}


{% image
  src="/images/openmetadata/connectors/tableau/service-connection-non-default-site.png"
  alt="Configure service connection"
  caption="Configure the service connection for a non-default site by filling the form" /%}

{% /stepVisualInfo %}

{% /step %}

{% extraContent parentTagName="stepsContainer" %}

#### Connection Options

- **Host and Port**: URL to the Tableau instance.
- **Username**: Specify the User to connect to Tableau. It should have enough privileges to read all the metadata.
- **Password**: Password for Tableau.
- **API Version**: Tableau API version. 
- **Site Name**: Tableau Site Name. To be kept empty if you are using the default Tableau site
- **Site Url**: Tableau Site Url. To be kept empty if you are using the default Tableau site
- **Personal Access Token**: Access token. To be used if not logging in with user/password.
- **Personal Access Token Secret**: Access token Secret. To be used if not logging in with user/password.
- **Environment**: Tableau Environment.


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
src="/images/openmetadata/connectors/configure-metadata-ingestion-dashboard.png"
alt="Configure Metadata Ingestion"
caption="Configure Metadata Ingestion Page" /%}

{% /stepVisualInfo %}

{% /step %}

{% extraContent parentTagName="stepsContainer" %}

#### Metadata Ingestion Options

- **Name**: This field refers to the name of ingestion pipeline, you can customize the name or use the generated name.
- **Dashboard Filter Pattern (Optional)**: Use to dashboard filter patterns to control whether or not to include dashboard as part of metadata ingestion.
    - **Include**: Explicitly include dashboards by adding a list of comma-separated regular expressions to the Include field. OpenMetadata will include all dashboards with names matching one or more of the supplied regular expressions. All other dashboards will be excluded.
    - **Exclude**: Explicitly exclude dashboards by adding a list of comma-separated regular expressions to the Exclude field. OpenMetadata will exclude all dashboards with names matching one or more of the supplied regular expressions. All other dashboards will be included.
- **Chart Pattern (Optional)**: Use to chart filter patterns to control whether or not to include charts as part of metadata ingestion.
    - **Include**: Explicitly include charts by adding a list of comma-separated regular expressions to the Include field. OpenMetadata will include all charts with names matching one or more of the supplied regular expressions. All other charts will be excluded.
    - **Exclude**: Explicitly exclude charts by adding a list of comma-separated regular expressions to the Exclude field. OpenMetadata will exclude all charts with names matching one or more of the supplied regular expressions. All other charts will be included.
- **Database Service Name (Optional)**: Enter the name of Database Service which is already ingested in OpenMetadata to create lineage between dashboards and database tables.
- **Enable Debug Log (toggle)**: Set the Enable Debug Log toggle to set the default log level to debug, these logs can be viewed later in Airflow.

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
