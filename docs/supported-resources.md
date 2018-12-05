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

## Azure  vM Sizes

The following is a list of supported virtual machine sizes you can specify for
the compute nodes in your OpenShift managed cluster. There is a couple of caveats
you must be aware of:

1. Each  vM has a different number of drives that can be attached. This may not
   be as immediately clear as memory or CPU size
2. Not all  vM sizes are available in _all_ regions. So even if the API may support
   the size you are sending, if the size is not available in the region, an error
   will be thrown

> **For more information check the [Current list of supported  vM sizes per region](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=virtual-machines)**

List of compute node sizes supported by the API:

|Size|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16G|
|Standard D8s v3|8|32G|
|Standard D16s v3|16|64G|
|Standard D32s v3|32|128G|
|Standard D64s v3|64|256G|
|-|-|-|
|Standard DS4 v2|8|28G|
|Standard DS5 v2|16|56G|
|-|-|-|
|Standard F8s v2|8|16G|
|Standard F16s v2|16|32G|
|Standard F32s v2|32|64G|
|Standard F64s v2|64|128G|
|Standard F72s v2|72|144G|
|-|-|-|
|Standard E4s v3|4|32G|
|Standard E8s v3|8|64G|
|Standard E16s v3|16|128G|
|Standard E20s v3|20|160G|
|Standard E32s v3|32|256G|
|Standard E64s v3|64|512G|
|-|-|-|
|Standard Gs2|4|56G|
|Standard Gs3|8|112G|
|Standard Gs4|16|224G|
|Standard Gs5|32|448G|
|-|-|-|
|Standard  DS12 v2|4|28G|
|Standard  DS13 v2|8|56G|
|Standard  DS14 v2|16|112G|
|Standard  DS15 v2|32|140G|
|-|-|-|
Standard L4|4|32G|
Standard L8|8|64G|
Standard L16|16|128G|
Standard L32|32|256G|

