---
title: Vnet Peering
description: Creating a Managed OpenShift on Azure cluster using Vnet Peering
services: container-service

ms.service: container-service
ms.topic: quickstart
ms.date: 10/04/2018
---

### VNET Peering

The `az openshift create` command can create a Managed OpenShift on Azure
cluster and peer its VNET to another VNET.  This feature is enabled with the
`--vnet-peer-id $VNET_ID` flag.

First, obtain the identifier of the VNET you want the cluster to peer with.  The
identifier will be of the form
`/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$VNET_RESOURCEGROUP/providers/Microsoft.Network/virtualNetworks/$VNET_NAME`.

```bash
PEER_VNET_ID=$(az network vnet show -n $VNET_NAME -g $VNET_RESOURCEGROUP --query id -o tsv)
```

Append the `--vnet-peer-id $VNET_ID` flag to the `az openshift create` command
to peer the Managed OpenShift on Azure cluster's VNET to `$VNET_ID`.
