# azuremetadata

#### Table of Contents

1. [Description](#description)
2. [Setup](#setup)
3. [Usage](#usage)
4. [Limitations](#limitations)
5. [Development](#development)

## Description

[Azure now has an instance metadata service that is GA globally !](https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-instancemetadataservice-overview)

To help with further automation, this modules exposes the Azure instance metadata as a structured fact for use in Puppet code.
If you would like to see this end up in Facter Core, please do vote for the JIRA below:
* [FACT-1383 - Azure Instance Metadata ](https://tickets.puppetlabs.com/browse/FACT-1383)

This has been tested with Puppet Enterprise 2018.1 on Windows 2016 and RHEL 7, however it should work on any platform that has a modern Ruby with open-uri and JSON support.

This module is part of a larger part of Puppet and Azure work that I presented at Puppetconf 2017.

* [You can see the presentation video here.](https://www.youtube.com/watch?v=tbWeYvOHvJE)

## Setup

### What azuremetadata affects 
This module deploys a single Ruby file that queries and exposes the Azure metadata as a structured fact.

### Setup Requirements

Include the module in your Puppetfile and let pluginsync do the rest for you.

Puppetfile entries


    # Directly from Git
    mod 'azuremetadata',
        :git => 'https://github.com/keirans/azuremetadata.git',
        :tag => '0.1.8'

    
    # Directly from the forge
    mod 'keirans-azuremetadata', '0.1.8'


## Usage

Once the fact is in place, you can use Facter on your Windows and Linux nodes to access the values as follows:


Returning the full set of metadata from Facter on Windows
```
PS> facter az_metadata
{
  compute => {
    location => "australiasoutheast",
    name => "win2016",
    offer => "WindowsServer",
    osType => "Windows",
    placementGroupId => "",
    platformFaultDomain => "0",
    platformUpdateDomain => "0",
    publisher => "MicrosoftWindowsServer",
    resourceGroupName => "puppettesting",
    sku => "2016-Datacenter",
    subscriptionId => "123451d0-122e-4e2a-ad73-e33d0652e3f7",
    tags => {
      tagname => "tagvalue"
    },
    version => "2016.127.20180412",
    vmId => "38499d6e-45bd-1234-1234-1c1f6c1d439e",
    vmScaleSetName => "",
    vmSize => "Standard_B2s",
    zone => ""
  },
  network => {
    interface => [
      {
        ipv4 => {
          ipAddress => [
            {
              privateIpAddress => "10.3.1.4",
              publicIpAddress => "52.200.212.194"
            }
          ],
          subnet => [
            {
              address => "10.3.1.0",
              prefix => "24"
            }
          ]
        },
        ipv6 => {
          ipAddress => []
        },
        macAddress => "000D2AE0FC69"
      }
    ]
  }
}
```

Returning individual values of the metadata from Facter on Windows


    PS> facter  az_metadata.compute.location
    australiasoutheast
    PS>
    
You can then reference these values in your Puppet code using the facts hash such as:

    notice("The  Azure location for ${facts['networking']['fqdn']} is ${facts['az_metadata']['compute']['location']}")


## Limitations

### Fact containment to Azure only environments
Currently, there is no consistent way to contain the fact to Azure public cloud environments only, as such, the fact is only exposed when the virtual fact is of value  hyperv, however, if you are using both hyperv and azure environments this may cause some issues for you as the API query will time out.

The main challenge here is that the cloud fact would help more effectively doesnt support anything but Linux right now. 
This is issue is tracked in JIRA: 
* [FACT-1441 - Add "cloud" fact that identifies Azure](https://tickets.puppetlabs.com/browse/FACT-1441)

### API version pinned to 2018-02-01
Version 0.1.8 is currently using API version 2018-02-01, I'll update this as new versions become available and bump the module version accordingly.

## Development
Happy to accept pull requests, I'd expect this to end up in Facter Core at some time in the future, then this can be deprecated.
Please do vote for this JIRA for its addition: 
* [FACT-1383 - Azure Instance Metadata ](https://tickets.puppetlabs.com/browse/FACT-1383)
