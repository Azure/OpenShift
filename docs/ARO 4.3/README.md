# Create an ARO 4.3 Private Cluster

## Overview

This document will walk you thorugh the steps to deploy a fully private Azure Red Hat OpenShift 4.3 cluster during the *preview of ARO 4.3* which includes:

- Bring your own private Azure VNET
- Private API Server
- Private Router (ingress controller)
- Custom DNS forarded to on-premises DNS Server
- Azure Active Directory Integration
- Restricted User Access and RBAC for project creation

## Creating an ARO Cluster 

### Prerequisites

ARO 4.3 is currently in *preview* and as such is subject to change.  Please follow the documentation to [create an Azure Red Hat OpenShift 4.3 Cluster](https://docs.microsoft.com/en-us/azure/openshift/howto-using-azure-redhat-openshift), but do not run the command to create a cluster yet.

When following the step to [Create a cluster](https://docs.microsoft.com/en-us/azure/openshift/howto-using-azure-redhat-openshift#create-a-cluster), you will need to add these additional parameters to create a private cluster.  Optionally, you can specify a custom DNS to be used by the router, including the console.

```bash
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet "dev-vnet" \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --apiserver-visibility Private \ 
  --ingress-visibility Private
  # [OPTIONAL] custom domain:
  # --domain aro.example.com
```

### Custom DNS
If you choose to specify a custom domain, the openshift console will be available at a URL such as `https://console-openshift-console.apps.aro.example.com`, instead of the built-in domain `https://console-openshift-console.apps.<random>.<location>.aroapp.io`.

By default OpenShift uses self-signed certificates for all of the routes created on `*.apps.<random>.<location>.aroapp.io`.  If you choose Custom DNS, after connecting to the cluster, you will need to follow the OpenShift documentation to [configure a custom CA for your ingress controller](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) and [custom CA for your API server](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).  

## Connecting to the ARO Cluster 

In order to connect to a private ARO cluster, you will need to perform the following steps from a host that is either in the VNET you created or in a VNET that is [peered](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview) with the VNET ARO was deployed to.

1. Get the API server address:
```bash
KUBEAPI=$(az aro show -n $CLUSTER -g $RESOURCEGROUP -o tsv --query apiserverProfile.url)
```

2. Get the kubeadmin password:
```bash
KUBEPWD=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o tsv --query kubeadminPassword)
```

3. Install the 4.x oc cli:
```bash
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/4.3.1/openshift-client-linux-4.3.1.tar.gz
tar -xf openshift-client-linux-4.3.1.tar.gz
``` 

4. Login to the cluster using kubeadmin with the oc cli:
```bash 
./oc login $KUBEAPI -u kubeadmin -p $KUBEPWD
```

5. Display the URL of the cluster web console:
```bash 
az aro show -n $CLUSTER -g $RESOURCEGROUP -o tsv --query consoleProfile.url
```

## Next Steps

- [AAD Integration](AADIntegration.md)
- [Limit Project Creation](LimitSelfProvisioning.md)
- [Configure DNS Forwarding](DNSForwarding.md)
- [Configure Azure Monitor](AzureMonitor.md)

## Known Issues
- You must setup a [Pull Secret](https://blog.openshift.com/building-rhel-based-containers-on-azure-red-hat-openshift/) in order to use the Red Hat container catalog.
- AAD Pod Identity is not currently working as-is.
- **Research** [Key Vault integration](https://github.com/Azure/kubernetes-keyvault-flexvol).  This should work if you do not depend on Pod Identity, but instead pass in the Service Principal.

## Code of conduct

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
