---
title: Quickstart - Deploy a Managed Openshift cluster
description: Quickly learn to create a Managed OpenShift cluster for Linux containers with the Azure CLI.
services: container-service
author: juliens
manager: qik

ms.service: container-service
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: juliens
---

# Quickstart: Deploy a Managed Openshift cluster

In this quickstart, an Managed OpenShift cluster is deployed using the Azure CLI.


This quickstart requires that you are running the Azure CLI version 2.0.46 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI][azure-cli-install].

## Create a resource group

Create a resource group with the [az group create][az-group-create] command. An Azure resource group is a logical group in which Azure resources are deployed and managed. When you create a resource group, you are asked to specify a location. This location is where your resources run in Azure.

The following example creates a resource group named *myOSACluster* in the *eastus* location.

```azurecli-interactive
az group create --name myOSACluster --location eastus
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

## Create a Managed Application Credentials

Use the [az ad app create][az-ad-app-create] commnand to create a Managed Application credentials will allow the cluster to run the authentication against Azure AD. We will have to pass some settings such as :
- The `display-name` to identify the application.
- The `password` this could be set directly from the `create` command.
- A unique `--identifier-uris` this have to be unique. The could be : `https://microsoft.onmicrosoft.com/<ClusterName>`
- A unique `--reply-urls` this have to match the `fqdn` of your cluster. The format have to be : `https://<ClusterName>.<Location>.cloudapp.azure.com/oauth2callback/Azure%20AD`

The following example creates a managed application named `myOSACluster` with the password `myOSACluster` with a unique identifier `https://microsoft.onmicrosoft.com/myOSACluster123` and the following reply url : `https://myOSACluster.eastus.cloudapp.azure.comoauth2callback/Azure%20AD`.

```azurecli-interactive
az ad app create --display-name myOSACluster --key-type Password --password myOSACluster --identifier-uris https://microsoft.onmicrosoft.com/myOSACluster123 --reply-urls https://juosacli71.eastus.cloudapp.azure.comoauth2callback/Azure%20AD`
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

## Create OpenShift cluster

Use the [az openshift create][az-openshift-create] command to create an OpenShift cluster. The following example creates a cluster named *myOSACluster* with four nodes.

```azurecli-interactive
az openshift create --resource-group myOSACluster --name myOSACluster -l eastus --node-count 4 --fqdn 'myOSACluster.eastus.cloudapp.azure.com' --aad-client-app-secret 'myOSACluster' --aad-tenant-id '72f988bf-86f1-41af-91ab-2d7cd011db47' --aad-client-app-id '57b4f673-af45-1223-1234-efb12fc0cd16'
```

> Note : To get the tenant ID of your current subscription you can run the following command `az account list`

After several minutes, the command completes and returns JSON-formatted information about the cluster.

## Connect to the cluster

# TODO

> [!div class="nextstepaction"]
> [OSA tutorial][osa-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com