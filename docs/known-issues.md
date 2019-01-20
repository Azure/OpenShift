# Known Issues

This page outlines known issues with Managed OpenShift on Azure (OSA), including
workarounds if known.

## Providers and features must be registered manually

Currently, the `Microsoft.ContainerService` `openshiftmanagedcluster` feature and `Microsoft.Solutions` provider must
be registered to your subscription manually before deploying your first OSA
cluster.

- Run a Cloud Shell (PowerShell) session from the Azure portal.

- If you have access to multiple subscriptions, specify the relevant
  subscription ID.

```powershell
Set-AzureRmContext -SubscriptionId "<SUBSCRIPTION_ID>"
```

- Register the Microsoft.ContainerService openshiftmanagedcluster feature.

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.ContainerService -FeatureName openshiftmanagedcluster
```

- Wait until the feature is showing RegistrationState `Registered`.

```powershell
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.ContainerService -FeatureName openshiftmanagedcluster | select RegistrationState
```

- Register the Microsoft.Solutions provider.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Solutions
```

- Wait until the provider is showing RegistrationState `Registered`.

```powershell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Solutions | select RegistrationState
```

## May not be possible to retry creation of a failed cluster

Currently, in many circumstances, if creation of a cluster using the `az` CLI
fails, retrying creation will always fail.  In this case, use `az openshift
delete` to delete the failed cluster and attempt to create an entirely new
cluster.

## OpenShift server certificate is untrusted

Note that currently the OpenShift console certificate is untrusted, therefore
when navigating to the console, you will need to manually accept the untrusted
certificate in your browser.

## OpenShift Managed Cluster resource is hidden by default

Currently, the `Microsoft.ContainerService/openShiftManagedClusters` resource
created by the `az` CLI is hidden in the Azure portal.  In the relevant
`Resource group` view, check `Show hidden types` to view the resource.

![Hidden Type](./media/OSA_Portal_HiddenType.png)

## Only available cluster update is scale up / scale down

Currently, no modifications are permitted to the
`Microsoft.ContainerService/openShiftManagedClusters` resource after creation,
except for scaling up or down the number of compute nodes.  Currently the
maximum number of compute nodes is limited to 20.

## Limited VM sizes

Managed OpenShift on Azure only permits certain VM sizes to be used.  You can
find the permitted sizes [here](./supported-resources.md#azure--vm-sizes).
