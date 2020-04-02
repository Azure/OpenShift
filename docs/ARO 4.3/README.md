
# Azure Red Hat Openshift

## Overview

This repo is intended for tracking issues with Azure Red Hat Openshift. It is monitored by the product team. For direct customer support, please take a look at the [Azure Support options](https://azure.microsoft.com/support/options/). Note that the following channels are also monitored and should be used as suggested:

- Updates about the service, including new features and new Azure regions: [OpenShift feed in Azure Updates](https://azure.microsoft.com/updates/?product=openshift)

## Documentation

Below is a table of content for draft documentation of Azure Red Hat OpenShift running on OpenShift 4 during preview.
[PLEASE CHECK OFFICIAL AZURE DOCUMENTATION HERE](https://docs.microsoft.com/en-ca/azure/openshift/intro-openshift).

## Table of content

* Concepts
    * [Network setup [need help]]() - ie: VNet setup, DNS, Private Link, pod-to-pod communication, network plugin, ..
    * [Private vs Public clusters [need help]]() - ie: What's a private cluster exactly, how does dealing with it differ than with public clusters, ..
    * [Minimum privileges [need help]]() - ie: Minimum service principal privileges needed to provision and manage a cluster
    * [Availability Zones [need help]]() - ie: With the cluster deployed against 3 AZs when available, what does this mean in terms of availability, scaling, ..
    * [Cluster patching and upgrades [need help]]() - ie: What goes on when a cluster needs to be patched/upgraded, ..
    * [Monitoring options [need help]]() - ie: What are the built-in monitoring options in OpenShift 4, including integration with Azure Monitor
* Operations
    * Cluster creation
      * [Create a cluster](create/create-cluster.md)
      * [Create a private cluster](create/create-private-cluster.md)
    * Security and authentication
      * [Configure Azure Active Directory authentication using the CLI](secure/configure-azure-ad-cli.md)
      * [Configure Azure Active Directory authentication using the UI](secure/configure-azure-ad-ui.md)
      * [Configure htpasswd authentication [draft]](secure/htpasswd-auth.md)
      * [Limit users which can create projects [draft]](secure/limit-self-provisioning.md)
      * [Configure projects, users and group roles [need help]]() - ie: How to assign users/groups to projects with varying permissions. Bonus if can be connected to Azure AD Groups. 
    * Cluster configuration and management
      * [Configure a custom Certificate Authority (CA) [draft]](configure/custom-ca.md)
      * [Configure DNS forwarding for on-premises DNS servers [draft]](configure/dns-forwarding.md)
      * [Scale a cluster [need help]]() - ie: How to scale a cluster with MachineSet vs Azure constructs, cluster-autoscaler, ..
      * [Configure monitoring using Azure Monitor [draft]](https://docs.microsoft.com/en-us/azure/openshift/howto-azure-monitor-v4) - needs some cleanup

## Known Issues

* If you want to use containers from the Red Hat container catalog, you must setup a [Pull Secret](https://blog.openshift.com/building-rhel-based-containers-on-azure-red-hat-openshift/).
* Azure Active Directory Pod Identity is not currently working as-is.
* **Research** [Key Vault integration](https://github.com/Azure/kubernetes-keyvault-flexvol).  This should work if you do not depend on Pod Identity, but instead pass in the Service Principal.

## Code of conduct

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
