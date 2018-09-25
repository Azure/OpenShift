---
title: Quickstart - Deploy a Managed OpenShift cluster
description: Quickly learn to create a Managed OpenShift cluster for Linux containers with the Azure CLI.
services: container-service
author: juliens
manager: qik

ms.service: container-service
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: juliens
---

# Quickstart: Deploy a Managed OpenShift cluster

In this quickstart, an Managed OpenShift cluster is deployed using the Azure CLI.


This quickstart requires that you are running the Azure CLI version 2.0.46 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI][azure-cli-install].

## Step 0: Create a resource group

Create a resource group with the [az group create][az-group-create] command. An Azure resource group is a logical group in which Azure resources are deployed and managed. When you create a resource group, you are asked to specify a location. This location is where your resources run in Azure.

The following example creates a resource group named *myOSACluster* in the *eastus* location.

```azurecli-interactive
export OSA_CLUSTER_NAME=myOSACluster
export LOCATION=eastus

az group create --name $OSA_CLUSTER_NAME --location $LOCATION
```

Output:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myOSACluster",
  "location": "eastus",
  "managedBy": null,
  "name": "myOSACluster",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## Step 1: Create a Managed Application Credentials

Use the [az ad app create][az-ad-app-create] commnand to create a Managed Application credentials will allow the cluster to run the authentication against Azure AD. We will have to pass some settings such as :
- The `display-name` to identify the application.
- The `password` this could be set directly from the `create` command.
- A unique `--identifier-uris` **this have to be unique**. This could be : `https://microsoft.onmicrosoft.com/<ClusterName>`
- A unique `--reply-urls` this have to match the `fqdn` of your cluster. The format have to be : `https://<ClusterName>.<Location>.cloudapp.azure.com/oauth2callback/Azure%20AD`

The following example creates a managed application named `myOSACluster` with the password `myOSACluster` with a unique identifier `https://microsoft.onmicrosoft.com/myOSACluster` and the following reply url : `https://myOSACluster.eastus.cloudapp.azure.comoauth2callback/Azure%20AD`.

```azurecli-interactive
export OSA_CLUSTER_NAME=myOSACluster
export OSA_AAD_SECRET=MyAw3s0meP@ssw0rd!
export OSA_AAD_IDENTIFIER=https://microsoft.onmicrosoft.com/myOSACluster123
export OSA_AAD_REPLY_URL=https://myOSACluster.eastus.cloudapp.azure.com/oauth2callback/Azure%20AD

az ad app create --display-name $OSA_CLUSTER_NAME \
                 --key-type Password --password $OSA_AAD_SECRET \
                 --identifier-uris $OSA_AAD_IDENTIFIER \
                 --reply-urls $OSA_AAD_REPLY_URL
```

Snippet Output :

```json
{
  "acceptMappedClaims": null,
  "addIns": [],
  "appId": "57b4f673-af45-1223-1234-efb12fc0cd16",
  ...
  "identifierUris": [
    "https://microsoft.onmicrosoft.com/juosaclitest2"
  ],
  ...
}
```

Take a note of the `appId` from the output.

## Step 2: Create OpenShift cluster

Use the [az openshift create][az-openshift-create] command to create an OpenShift cluster. 
The following example creates a cluster named *myOSACluster* with four nodes.

```azurecli-interactive
export OSA_CLUSTER_NAME=myOSACluster
export OSA_RG_NAME=myOSACluster
export OSA_LOCATION=eastus
export OSA_FQDN=myOSACluster.eastus.cloudapp.azure.com
export OSA_AAD_ID=57b4f673-af45-1223-1234-efb12fc0cd16
export OSA_AAD_SECRET=MyAw3s0meP@ssw0rd!
export OSA_AAD_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47


az openshift create --resource-group $myOSACluster --name $OSA_CLUSTER_NAME \
                    -l $eastus --node-count 4 --fqdn $OSA_FQDN \
                    --aad-client-app-id $OSA_AAD_ID \ 
                    --aad-client-app-secret $OSA_AAD_SECRET \
                    --aad-tenant-id $OSA_AAD_TENANT
```

> Note : To get the tenant ID of your current subscription you can run the following command `az account list`

After several minutes, the command completes and returns JSON-formatted information about the cluster.

## Step 3: Verify / Update Reply URLs in AAD app

If AAD app was created using Step 1 you can skip this step. If you have an existing Web app/API type AAD application you can update the reply URLs in AAD app with FQDN of your newly created OSA cluster. 

Do a search for `App registrations` in the search section located at the tep and navigate to it.

![](./medias/OSA_APP_Portal.png)

Search for your `AAD name` with the `All apps` filter on, and click on it to get more informations.

![](./medias/OSA_APP_Infos.png)

Click on `Settings` and go in the `Reply URLs` section. 

Change or add a value. 

![](./medias/OSA_ReplyURL.png)

> Reminder, this should be using this format : `https://<YOUR_FQDN>/oauth2callback/Azure%20AD`

## Step 4: Connect to the cluster

After your deployment is done, you should be able to open your browser to the `fqdn` that you choose during the creation of your cluster.

For example : `https://myOSACluster.eastus.cloudapp.azure`

> You will have a `NET::ERR_CERT_AUTHORITY_INVALID` error from your browser, you will have to validate the connexion manually.

Click on `Azure AD`

![](./medias/OSA_Auth.png)

![](./medias/OSA_Console.png)

## Step 5: Using OC CLI
Click on the upper right corner (profile name) to get the CLI login information. 

![](./medias/OSA_CLI.png)

You need the OC CLI which can be downloaded from https://github.com/openshift/origin/releases

> Using MacOS, you can easly install it with homebrew `brew install openshift-cli
`
 
Login using OC CLI by copying the command above:
```
oc login <FQDN> --token=<YOUR_TOKEN>
```


<!-- LINKS - external -->
[OpenShift CLI]: https://github.com/openshift/origin/releases

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com