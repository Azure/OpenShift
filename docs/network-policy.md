---
title: Network Policy
description: Information regarding management of network policies
services: cluster-management

ms.service: cluster-management
ms.topic: management
ms.date: 3/06/2019
---

# Network Policy

## Plugin

OpenShift on Azure uses the ovs-networkpolicy plugin which enables the use of
NetworkPolicies.  More information on enabling and 
managing network policies can be found 
[here](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_networking.html#admin-guide-networking-networkpolicy).

## Network Policies

Network policies can be managed by the project admin or the customer-admin 
feature granted to designated users.

The default project has been labeled `default` so that users can use routes in 
NetworkPolicies. 

## Egress Traffic

Customer admins can setup egress firewalls via [EgressNetworkPolicies](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_networking.html#admin-guide-limit-pod-access-egress)

Egress routers and multicast are currently not supported.
