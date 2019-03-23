---
title: Create a tenant for your Azure Open Shift cluster | Microsoft Docs
description: Shows you how to create a tenant and Azure application object so that you can use Open Shift on Azure
documentationcenter: .net
author: tylermsft
ms.author: twhitney
ms.service: container-service
manager: jeconnoc
editor: ''
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/22/2019
---

# How to create a tenant

Before you can create an Azure Red Hat OpenShift cluster, you need a tenant in which to create the cluster, and an app registration to allow the cluster to perform functions such as configuring storage.

Follow these instructions to create an Azure tenant if you don't already have one, as well as an app registration that allows authentication for accounts within your tenant.

A tenant is a dedicated instance of Azure Active Directory (AD) that an organization or app developer receives when they create a relationship with Microsoft by signing up for Azure, Microsoft Intune, or Microsoft 365. Each Azure AD tenant is distinct and separate from other Azure AD tenants and has its own work and school identities and app registrations.

## Create a new tenant

To create a tenant:

1. Sign in to the [Azure portal](https://portal.azure.com/) using the account you want to use to create your Azure Red Hat OpenShift cluster.
2. Click this [link](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) to go to the Azure Active Directory blade to create a tenant, or as it is otherwise referred to, an Azure Active Directory.
3. Provide an **Organization name**.
4. Provide an **Initial domain name**. What you put here will have *onmicrosoft.com* appended to it. You can use what you put for **Organization name** here.
5. At the bottom of the page click **Create**.
6. After your tenant (Azure Active Directory) is created, click on **Click here to manage your new directory**. Your tenant will be displayed in the upper right of the Azure portal:
![Tenant display][tenantcallout]
7. We need to get the tenant ID to specify where to create the cluster later. On the portal, you should now see the Azure Active Directory blade for your new tenant. In the **Manage** section of the **Azure Active Directory** blade, click **Properties**. Copy the value for **Directory ID**. We will refer to this value as the `TENANT` value later in this procedure.

Refer to [Set up a dev environment](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) if you want more detailed instructions about setting up an Azure tenant.

## Create a new app registration

Accessing resources that are secured by an Azure Active Directory (AD) tenant requires a security principal.

OpenShift needs permissions to perform tasks on behalf of your cluster in Azure. Now that you have a tenant, create an Azure AD app registration which will serve as the service principal in Azure Active Directory that will grant permissions to resources.

An application that wants to use the capabilities of Azure AD must first be registered in an Azure AD tenant. This registration process involves giving Azure AD details about your application such as the URL where the app is  located, the URL to send replies after a user is authenticated, the URI that identifies the app, and so on.

1. Ensure that your newly created tenant appears under your user name in the top right of the portal:
![Tenant display][tenantcallout]
If the wrong tenant is displayed, click on your user name in the top right, then click **Switch Directory**, and select the directory created above from the **All Directories** list.
2. Click on the following link to go to the [App registrations blade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)
3. The **App registrations** blade appears. Click **+New application registration**.
4. In the **Create** pane, enter a friendly name (can be any name) for your application.
5. Ensure that **Application type** is set to **Web app/API**.
6. Create a **Sign-on URL** comprised of the name you will use when you create your cluster, the location of the cluster, and append `.cloudapp.azure.com` For example, if your cluster name is going to be `mycluster` (note that the cluster name must be all lowercase), and you will be creating it in the `eastus` region, the fully-qualified domain name (FQDN) that you will enter for the **Sign-on URL** would be `https://mycluster.eastus.cloudapp.azure.com`  Remember this URL because you will need it to access the app running on your cluster.  This URL needs to be unique so take that into account when you decide on your cluster name in this step.
7. Press Tab to move focus out of the **Sign-on URL** field to validate the URL.
8. At the bottom of the Create pane, click **Create** to create the Azure Active Directory application object.

For more information about Azure Application Objects, see [Application and service principal objects in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).
Refer to [Register an app with the Azure Active Directory v1.0 endpoint](https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-v1-add-azure-ad-app) if you want more detailed instructions about creating a new Azure AD application.

### Create a client secret

Next, generate a client secret.

1. While still in the portal on the **Registered App** page from the previous step, copy the **Application ID** and save it where you can refer to it later. We will refer to this value as `APPID` later on in the procedure.
![App id][appidimage]
2. Click on **Settings** to open the settings for your registered app.
3. On the **Settings** pane that appears, click **Keys**.  The Keys pane appears.
![Create key][createkeyimage]
4. Provide a **Key description**.
5. Set the expiration duration to **In 2 years**
6. Click **Save** and the key value will appear.
7. Make a copy of the key value. We will refer to this value as `SECRET` in later instructions.

### Create a Reply URL

Azure Active Directory uses the app object reply URL to specify the callback to use when authorizing the app. Even the smallest mismatch (trailing slash missing, different casing) will cause the token-issuance operation to fail and you won't be able to sign in.

1. Back on the **Settings** pane, click **Reply URLs**.  The Reply URLs pane appears.
2. A reply URL that is the FQDN value from step 6 in [Create a new app registration](#create-a-new-app-registration) will already be entered as one of the Reply URLs on the right. Edit it and append `/oauth2callback/Azure%20AD`.  For example, a reply URL will look something like `https://mycluster.eastus.cloudapp.azure.com/oauth2callback/Azure%20AD`
3. Click **Save** to save the Reply URL.

## Create new user in Active Directory

Create a new user in Active Directory that you will use to sign in to the app running on your OSA cluster.

1. Click this [link](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) to go to the **Users - All users** pane.
2. Click **+ New user**. The **User** pane appears.
3. Enter a **Name** that you'd like for this user.
4. Create a **User name** based on the name of the tenant you created with  `.onmicrosoft.com` appended at the end. For example, `chris@yourTenantName.onmicrosoft.com`. Record this for later. This is the user name you will use to  sign in to use the app on your cluster later.
5. Click **Directory role** and select **Global administrator** and then click **Ok** at the bottom of the pane.
6. In the middle of the **User** pane, click **Show Password** and record the temporary password. After you log in the first time, you will be prompted to reset it.
7. At the bottom of the pane, click **Create** to create the user.

## Create an Azure Red Hat OpenShift cluster in your tenant

Open a Bash command window and set the following variables:

> [!IMPORTANT]
> The name of your cluster must be all lowercase or cluster creation will fail.

```BASH
CLUSTER_NAME=<the name of your cluster - lowercase only>
```

 Use the same name for the cluster that you chose in step 6 of [Create new app registration](#create-new-app-registration).

```BASH
LOCATION=<location>
```

Choose a location to create your cluster, for example:
`LOCATION=eastus`

Set `FQDN` to the fully qualified name of your cluster. This is composed of the cluster name, the location, with `.cloudapp.azure.com` appended to the end.  For example:  

```BASH
FQDN=$CLUSTER_NAME.$LOCATION.cloudapp.azure.com
```

Set  `APPID` to the value you saved in step 1 of [Create a client secret](#create-a-client-secret).  
```BASH
APPID=<app ID value>
```

Set `SECRET` to the value you saved in step 7 of [Create a client secret](#create-a-client-secret).  
```BASH
SECRET=<secret value>
```

Set `TENANT` to the value you provided in step 7 of [Create a new tenant](#create-a-new-tenant)  
```BASH
TENANT=<tenant id>
```

Create the resource group for the cluster. Run the following command from the Bash shell that you used to defined the variables above:

```BASH
az group create --name $CLUSTER_NAME --location $LOCATION
```

Now create the cluster:

```BASH
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --fqdn $FQDN --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT
```

## Test your cluster

To test your cluster you need to use a browser instance that hasn't cached the user you are currently logged into the Azure portal as. This is because you need to log in as the new user you created earlier.

1. Open an incognito window (Chrome) or a InPrivate windows (Edge)
2. Navigate to the sign-on URL that you created in step 6 of [Create new app registration](#create-new-app-registration). For example, https://mycluster.eastus.cloudapp.azure.com

For now, you will get message that the site is not secure. Click **Details** > **Go on to the webpage**

Log in with the user and password that you created in [Create new user in Active Directory](#create-new-user-in-active-directory)
When the **Permissions requested** dialog appears, select **Consent on behalf of your organization** and then **Accept**.

You should then see the Openshift container platform catalog in your browser. You now have an Azure Red Hat OpenShift cluster in your tenant.

## Resources

[Applications and service principals](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
[Create a tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)
[Register an app with the Azure Active Directory v1.0 endpoint](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

[appidimage]: ./media/howto-create-tenant/get-app-id.png  
[createkeyimage]: ./media/howto-create-tenant/create-key.png  
[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png  