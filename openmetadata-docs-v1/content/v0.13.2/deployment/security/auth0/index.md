---
title: Auth0 SSO
slug: /deployment/security/auth0
---

# Auth0 SSO

Follow the sections in this guide to set up Auth0 SSO.

<Important>

Security requirements for your **production** environment:

- **DELETE** the admin default account shipped by OM in case you had [Basic Authentication](/deployment/security/basic-auth)
  enabled before configuring the authentication with Auth0 SSO.
- **UPDATE** the Private / Public keys used for the [JWT Tokens](/deployment/security/enable-jwt-tokens). The keys we provide
  by default are aimed only for quickstart and testing purposes. They should NEVER be used in a production installation.

</Important>

## Create Server Credentials

### Step 1: Create the Account

- If you don't have an account, [Sign up](https://auth0.com/signup) to create one.
- Select the Account Type, i.e., Company or Personal
- Click I need advanced settings and click next.

<Image src="/images/deployment/security/auth0/create-account-1.png" alt="create-account"/>

- Provide the Tenant Domain, select the region and click on Create Account.

<Image src="/images/deployment/security/auth0/create-account-2.png" alt="create-account"/>

- Once done, you will land on the dashboard page.

<Image src="/images/deployment/security/auth0/create-account-3.png" alt="create-account"/>

### Step 2: Create a New Application

- Once you are on the Dashboard page, click on `Applications > Applications` available on the left-hand side panel.

<Image src="/images/deployment/security/auth0/create-new-app-1.png" alt="create-app"/>

- Click on `Create Application`.

<Image src="/images/deployment/security/auth0/create-new-app-2.png" alt="create-app"/>

- Enter the Application name.
- Choose an application type and click on `Create`.

<Image src="/images/deployment/security/auth0/create-new-app-3.png" alt="create-app"/>

### Step 3: Where to Find the Credentials

- Navigate to the Settings tab.
- You will find your `Client ID`, `Client Secret` and `Domain`.

<Image src="/images/deployment/security/auth0/credentials.png" alt="credentials"/>

## Create Service Account (optional)

This is a guide to create ingestion bot service account. This step is optional if you configure the ingestion-bot with
the JWT Token, you can follow the documentation of [Enable JWT Tokens](/deployment/security/enable-jwt-tokens).

### Step 1: Enable Client-Credential

- Go to your project dashboard.

<Image src="/images/deployment/security/auth0/enable-client-credential-1.png" alt="client"/>

- Navigate to `Applications > Applications`

<Image src="/images/deployment/security/auth0/enable-client-credential-2.png" alt="client"/>

- Select your application from the list.

<Image src="/images/deployment/security/auth0/enable-client-credential-3.png" alt="client"/>

- Once selected, scroll down until you see the `Application Properties` section.
- Change the Token Endpoint `Authentication Method` from `None` to `Basic`.

<Image src="/images/deployment/security/auth0/enable-client-credential-4.png" alt="client"/>

- Now scroll further down to the section on `Advanced Settings`.
- Click on it and select `Grant Types`.
- In the `Grant Types`, check the option for `Client Credentials`.

<Image src="/images/deployment/security/auth0/enable-client-credential-5.png" alt="client"/>

- Once done, click on `Save Changes`.

### Step 2: Authorize the API with our Application.

- Navigate to `Applications > APIs` from the left menu.

<Image src="/images/deployment/security/auth0/authorize-api-1.png" alt="auth"/>

- You will see the `Auth0 Management API`.

<Image src="/images/deployment/security/auth0/authorize-api-2.png" alt="auth"/>

- Click on the `Auth0 Management API`.

<Image src="/images/deployment/security/auth0/authorize-api-3.png" alt="auth"/>

- Click on the `Machine to Machine Applications` tab.
- You will find your application listed below.

<Image src="/images/deployment/security/auth0/authorize-api-4.png" alt="auth"/>

- Click on the toggle to authorize.
- Once done you will find a down arrow, click on it.

<Image src="/images/deployment/security/auth0/authorize-api-5.png" alt="auth"/>

- Select the permissions (scopes) that should be granted to the client.
- Click on `Update`.

<Image src="/images/deployment/security/auth0/authorize-api-6.png" alt="auth"/>

After the applying these steps, you can update the configuration of your deployment:

{%inlineCalloutContainer%}

{%inlineCallout
    icon="celebration"
    bold="Docker Security"
    href="/deployment/security/auth0/docker" %}
Configure Auth0 SSO for your Docker Deployment.
{%/inlineCallout%}

{%inlineCallout
    icon="storage"
    bold="Bare Metal Security"
    href="/deployment/security/auth0/bare-metal" %}
Configure Auth0 SSO for your Bare Metal Deployment.
{%/inlineCallout%}

{%inlineCallout
    icon="fit_screen"
    bold="Kubernetes Security"
    href="/deployment/security/auth0/kubernetes" %}
Configure Auth0 SSO for your Kubernetes Deployment.
{%/inlineCallout%}

{%/inlineCalloutContainer%}

## Configure Ingestion

After everything has been set up, you will need to configure your workflows if you are running them via the
`metadata` CLI or with any custom scheduler.

When setting up the YAML config for the connector, update the `workflowConfig` as follows:

```yaml
workflowConfig:
  openMetadataServerConfig:
    hostPort: "http://localhost:8585/api"
    authProvider: auth0
    securityConfig:
      clientId: "{your_client_id}"
      secretKey: "{your_client_secret}"
      domain: "{your_domain}"
```
