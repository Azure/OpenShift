---
title: OpenShift on Azure - Customer Admin
description: Description of the customer admin cluster role
services: cluster management

ms.service: container-service
ms.topic: documentation
ms.date: 3/12/2019
---

### Customer Admin

Inside of the OpenShift on Azure managed cluster there exists a role called
customer-admin.  This role allows the customer to harness a subset of
the cluster-admin responsibilities.  This allows customers to self-service
their project needs.

The customer-admin's membership is managed by an Azure Active Directory (AAD)
group.  The AAD group ID is specified at install time.  This group ID will then
be added to a customer-admin controller that operates in-cluster to sync members
from the AAD group to a designated cluster group. This group is called 
`osa-customer-admin`.

For each customer's project, members of the `osa-customer-admin` group can have
access and can manage the project's needs. As of the current release, this 
includes the following resources:
- cluster service brokers
- limit ranges
- resource quotas
