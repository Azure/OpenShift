---
title: Quickstart - Deploying a Managed OpenShift on Azure cluster
description: Quickly learn to create a Managed OpenShift cluster for Linux containers with the Azure CLI.
services: container-service

ms.service: container-service
ms.topic: quickstart
ms.date: 09/24/2018
---

# Quickstart: Deploying a Managed OpenShift on Azure cluster

This quickstart will walk you through deploying a Managed OpenShift on Azure
cluster using the Azure CLI.

## Prerequisites

It is **essential** to follow the following prerequisites before deploying an OSA
cluster for the first time in an Azure subscription.

- Currently, the `Microsoft.ContainerService` `openshiftmanagedcluster` feature,
  and `Microsoft.Solutions` provider must be registered to your subscription manually before deploying your first
  OSA cluster.  Follow the instructions in [known
  issues](known-issues.md#providers-and-features-must-be-registered-manually).

- You can use either the [latest Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) or [Azure Cloud Shell](https://shell.azure.com/)

![](./media/OSA_AZ_CLI.png)

## Step 1: Log in to Azure

If you are running the Azure CLI locally, run `az login` to log in to Azure.

```bash
az login
```

If you have access to multiple subscriptions, run `az account set -s
SUBSCRIPTION_ID` to default to the correct subscription.

## Step 2: Create a Managed OpenShift on Azure cluster

Choose a name and Azure location for your Managed OpenShift on Azure cluster.
The fully qualified domain name (FQDN) of your cluster will be
`$CLUSTER_NAME.$LOCATION.cloudapp.azure.com`.  This FQDN must not already be
taken by any Azure virtual machine, otherwise cluster creation will fail.  Note
that there is a [known
issue](known-issues.md#cannot-create-cluster-with-fqdns-containing-capital-letters)
that FQDNs must not contain capital letters.

This walkthrough will create a Managed OpenShift on Azure cluster in a new
resource group with the same name as the cluster, with the default of 4
Standard_D4s_v3 compute nodes.

It follows the `az openshift create` default behaviour of automatically creating
an Azure AD Application for you.  For more details, see [AAD Application
Configuration](aad-application-configuration.md).

It does not enable VNET peering on the cluster's VNET.  For details on how to do
that, see [VNET Peering](vnet-peering.md).

From the Azure CLI, run:

```bash
CLUSTER_NAME=myuniqueclustername
LOCATION=eastus
FQDN=$CLUSTER_NAME.$LOCATION.cloudapp.azure.com

az group create --name $CLUSTER_NAME --location $LOCATION

az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --fqdn $FQDN
```

From ARM copy the files from arm template folder, run:
```bash
CLUSTER_NAME=myuniqueclustername
LOCATION=eastus
FQDN=$CLUSTER_NAME.$LOCATION.cloudapp.azure.com
AAD_SECRET=theSecretOfTheAADApplication
AAD_ID=theClientIdOfTheAADApplication
AAD_TENANT=theTenantIdOfTheAADTenant
ADMIN_GROUP_ID=theObjectIdOfTheAdminGroup

az group create --name $CLUSTER_NAME --location $LOCATION

az group deployment create \
    --name openshiftmanaged \
    --resource-group $CLUSTER_NAME \
    --template-file "arm/openshift_template.json" \
    --parameters "arm/openshift_parameters.json" \
    --parameters "resourceName=$CLUSTER_NAME" \
        "location=$LOCATION" \
        "fqdn=$FQDN" \
        "servicePrincipalClientId=$AAD_ID" \
        "servicePrincipalClientSecret=$AAD_SECRET" \
        "tenantId=$AAD_TENANT" \
        "customerAdminGroupId=$ADMIN_GROUP_ID"
```

> **List of azure regions that supports OpenShift on Azure : [Supported Regions](supported-resources.md#azure-regions)**.

All being well, after 10-15 minutes `az openshift create` will complete
successfully and return a JSON document containing your cluster details.

## Step 3: Navigate to the OpenShift on Azure cluster console

After `az openshift create` has completed, you should be able to open your
browser and navigate to https://$FQDN/, e.g.
https://myuniqueclustername.eastus.cloudapp.azure.com/.

**Note:** currently the OpenShift console certificate is untrusted, therefore
when navigating to the console, you will need to manually accept the untrusted
certificate in your browser.

To log in to the cluster, click on `Azure AD`.

![](./media/OSA_Auth.png)

You should now be logged into the cluster console.

![](./media/OSA_Console.png)

## Step 4: Use the oc CLI

In the cluster console, click the question mark in the upper right corner and
select `Command Line Tools`.  Follow the `Latest Release` link to download and
install the supported oc CLI for Linux, MacOS or Windows.  If you are unable to login to the Red Hat portal, the oc CLI is also available [here](https://www.okd.io/download.html).

> **NOTE :** 
If you do not see the question mark icon in the upper right corner, select Service Catalog or Application Console from the upper left hand drop-down.

The same page also has a command of the form `oc login
https://myuniqueclustername.eastus.cloudapp.azure.com --token=<hidden>`.  Click
the copy to clipboard button to copy this command.  Paste it in your terminal to
log in to the cluster using the oc CLI.  

Alternatively your token is available here: `https://myuniqueclustername.eastus.cloudapp.azure.com/oauth/token/request`

## Step 5: Scale up to 5 compute nodes

From the Azure CLI, run:

```bash
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

All being well, after a few minutes `az openshift create` will complete
successfully and return a JSON document containing your cluster details.
