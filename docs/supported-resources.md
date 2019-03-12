# Managed OpenShift on Azure cluster - Supported Resources

## Azure Regions

The following is a list of currently supported Azure regions around the globe
where you can deploy your OpenShift managed clusters:

|Region|CLI code|
|-|-|
|🇦🇺 Australia East|`australiaeast`|
|🇨🇦 Canada Central|`canadacentral`|
|🇨🇦 Canada East|`canadaeast`|
|🇺🇸 East US|`eastus`|
|🇺🇸 West US|`westus`|
|🇪🇺 West Europe|`westeurope`|
|🇪🇺 North Europe|`northeurope`|

## Azure  VM Sizes

The following is a list of supported virtual machine sizes you can specify for
the compute nodes in your OpenShift managed cluster. There is a couple of caveats
you must be aware of:

1. Each  VM has a different number of drives that can be attached. This may not
   be as immediately clear as memory or CPU size
2. Not all  VM sizes are available in _all_ regions. So even if the API may support
   the size you are sending, if the size is not available in the region, an error
   will be thrown

> **For more information check the [Current list of supported  VM sizes per region](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=virtual-machines)**

List of compute node sizes supported by the API:

|Size|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16G|
|Standard D8s v3|8|32G|
|Standard D16s v3|16|64G|
|Standard D32s v3|32|128G|
|-|-|-|
|Standard E4s v3|4|32G|
|Standard E8s v3|8|64G|
|Standard E16s v3|16|128G|
|Standard E32s v3|32|256G|
|-|-|-|
|Standard F8s v2|8|16G|
|Standard F16s v2|16|32G|
|Standard F32s v2|32|64G|

List of valid infra/master nodes supported by the API:

|Size|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16G|
|Standard D8s v3|8|32G|
|Standard D16s v3|16|64G|
|Standard D32s v3|32|128G|
