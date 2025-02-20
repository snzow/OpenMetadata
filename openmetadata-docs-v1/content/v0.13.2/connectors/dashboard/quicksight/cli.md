---
title: Run Quicksight Connector using the CLI
slug: /connectors/dashboard/quicksight/cli
---

# Run Quicksight using the metadata CLI

In this section, we provide guides and references to use the Quicksight connector.

Configure and schedule Quicksight metadata and profiler workflows from the OpenMetadata UI:

- [Requirements](#requirements)
- [Metadata Ingestion](#metadata-ingestion)

## Requirements

{%inlineCallout icon="description" bold="OpenMetadata 0.12 or later" href="/deployment"%}
To deploy OpenMetadata, check the Deployment guides.
{%/inlineCallout%}

To run the Ingestion via the UI you'll need to use the OpenMetadata Ingestion Container, which comes shipped with
custom Airflow plugins to handle the workflow deployment.

### Python Requirements

To run the Quicksight ingestion, you will need to install:

```bash
pip3 install "openmetadata-ingestion[quicksight]"
```

## Metadata Ingestion

All connectors are defined as JSON Schemas.
[Here](https://github.com/open-metadata/OpenMetadata/blob/main/openmetadata-spec/src/main/resources/json/schema/entity/services/connections/dashboard/quickSightConnection.json)
you can find the structure to create a connection to Quicksight.

In order to create and run a Metadata Ingestion workflow, we will follow
the steps to create a YAML configuration able to connect to the source,
process the Entities if needed, and reach the OpenMetadata server.

The workflow is Quicksightled around the following
[JSON Schema](https://github.com/open-metadata/OpenMetadata/blob/main/openmetadata-spec/src/main/resources/json/schema/metadataIngestion/workflow.json)

### 1. Define the YAML Config

This is a sample config for Quicksight:

{% codePreview %}

{% codeInfoContainer %}

#### Source Configuration - Service Connection

{% codeInfo srNumber=1 %}

**awsConfig**
  - **AWS Access Key ID**: Enter your secure access key ID for your Glue connection. The specified key ID should be authorized to read all databases you want to include in the metadata ingestion workflow.
  - **AWS Secret Access Key**: Enter the Secret Access Key (the passcode key pair to the key ID from above).
  - **AWS Region**: Enter the location of the amazon cluster that your data and account are associated with.
  - **AWS Session Token (optional)**: The AWS session token is an optional parameter. If you want, enter the details of your temporary session token.
  - **Endpoint URL (optional)**: Your Glue connector will automatically determine the AWS Glue endpoint URL based on the region. You may override this behavior by entering a value to the endpoint URL.


{% /codeInfo %}

{% codeInfo srNumber=2 %}

**awsAccountId**: AWS Account ID

{% /codeInfo %}

{% codeInfo srNumber=3 %}

**identityType**: The authentication method that the user uses to sign in.

{% /codeInfo %}

{% codeInfo srNumber=4 %}

**namespace**: The Amazon QuickSight namespace that contains the dashboard IDs in this request ( To be provided when identityType is `ANONYMOUS` )

{% /codeInfo %}


#### Source Configuration - Source Config

{% codeInfo srNumber=5 %}

The `sourceConfig` is defined [here](https://github.com/open-metadata/OpenMetadata/blob/main/openmetadata-spec/src/main/resources/json/schema/metadataIngestion/dashboardServiceMetadataPipeline.json):

**dbServiceNames**: Database Service Name for the creation of lineage, if the source supports it.
**dashboardFilterPattern**, **chartFilterPattern**: Note that the they support regex as include or exclude. E.g.,
**includeTags**: Set the Include tags toggle to control whether or not to include tags as part of metadata ingestion.
**markDeletedDashboards**: Set the Mark Deleted Dashboards toggle to flag dashboards as soft-deleted if they are not present anymore in the source system.


{% /codeInfo %}


#### Sink Configuration

{% codeInfo srNumber=6 %}

To send the metadata to OpenMetadata, it needs to be specified as `type: metadata-rest`.

{% /codeInfo %}

#### Workflow Configuration

{% codeInfo srNumber=7 %}

The main property here is the `openMetadataServerConfig`, where you can define the host and security provider of your OpenMetadata installation.

For a simple, local installation using our docker containers, this looks like:

{% /codeInfo %}

{% /codeInfoContainer %}

{% codeBlock fileName="filename.yaml" %}

```yaml
source:
  type: quicksight
  serviceName: local_quicksight
  serviceConnection:
    config:
      type: QuickSight
```
```yaml {% srNumber=1 %}
      awsConfig:
        awsAccessKeyId: KEY
        awsSecretAccessKey: SECRET
        awsRegion: us-east-2
        awsSessionToken: Token
```
```yaml {% srNumber=2 %}
      awsAccountId: <aws-account-id>
```
```yaml {% srNumber=3 %}
      identityType: IAM #QUICKSIGHT, ANONYMOUS
```
```yaml {% srNumber=4 %}
      namespace: #to be provided if identityType is Anonymous
```
```yaml {% srNumber=5 %}
  sourceConfig:
    config:
      type: DashboardMetadata
      markDeletedDashboards: True
      # dbServiceNames:
      #   - service1
      #   - service2
      # dashboardFilterPattern:
      #   includes:
      #     - dashboard1
      #     - dashboard2
      #   excludes:
      #     - dashboard3
      #     - dashboard4
      # chartFilterPattern:
      #   includes:
      #     - chart1
      #     - chart2
      #   excludes:
      #     - chart3
      #     - chart4

```yaml {% srNumber=6 %}
sink:
  type: metadata-rest
  config: {}
```

```yaml {% srNumber=7 %}
workflowConfig:
  openMetadataServerConfig:
    hostPort: "http://localhost:8585/api"
    authProvider: openmetadata
    securityConfig:
      jwtToken: "{bot_jwt_token}"
```

{% /codeBlock %}

{% /codePreview %}

### Workflow Configs for Security Provider

We support different security providers. You can find their definitions [here](https://github.com/open-metadata/OpenMetadata/tree/main/openmetadata-spec/src/main/resources/json/schema/security/client).

## Openmetadata JWT Auth

- JWT tokens will allow your clients to authenticate against the OpenMetadata server. To enable JWT Tokens, you will get more details [here](/deployment/security/enable-jwt-tokens).

```yaml
workflowConfig:
  openMetadataServerConfig:
    hostPort: "http://localhost:8585/api"
    authProvider: openmetadata
    securityConfig:
      jwtToken: "{bot_jwt_token}"
```

- You can refer to the JWT Troubleshooting section [link](/deployment/security/jwt-troubleshooting) for any issues in your JWT configuration. If you need information on configuring the ingestion with other security providers in your bots, you can follow this doc [link](/deployment/security/workflow-config-auth).

### 2. Run with the CLI

First, we will need to save the YAML file. Afterward, and with all requirements installed, we can run:

```bash
metadata ingest -c <path-to-yaml>
```

Note that from connector to connector, this recipe will always be the same. By updating the YAML configuration,
you will be able to extract metadata from different sources.
