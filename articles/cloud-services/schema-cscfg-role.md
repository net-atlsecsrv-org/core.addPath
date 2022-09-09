---
title: "Azure Cloud Services Role Schema | Microsoft Docs"
ms.custom: ""
ms.date: "12/07/2016"
services: cloud-services
ms.reviewer: ""
ms.service: "cloud-services"
ms.suite: ""
ms.tgt_pltfrm: ""
ms.topic: "reference"
ms.assetid: e4fbffc1-98eb-449c-971c-de415e45ab34
caps.latest.revision: 12
author: "jpconnock"
ms.author: "jeconnoc"
manager: "timlt"
---

# Azure Cloud Services Config Role Schema

The `Role` element of the configuration file specifies the number of role instances to deploy for each role in the service, the values of any configuration settings, and the thumbprints for any certificates associated with a role.

For more information about the Azure Service Configuration Schema, see [Cloud Service (classic) Configuration Schema](schema-cscfg-file.md). For more information about the Azure Service Definition Schema, see [Cloud Service (classic) Definition Schema](schema-csdef-file.md).

##  <a name="Role"></a> Role Element
The following example shows the `Role` element and its child elements.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

The following table describes the attributes for the `Role` element.

| Attribute | Description |
| --------- | ----------- |
| name   | Required. Specifies the name of the role. The name must match the name provided for the role in the service definition file.|
| vmName | Optional. Specifies the DNS name for a Virtual Machine. The name must be 10 characters or less.|

The following table describes the child elements of the `Role` element.

| Element | Description |
| ------- | ----------- |
| Instances | Required. Specifies the number of instances to deploy for the role. The number of instances is defined by an integer for the `count` attribute.|
| Setting   | Optional. Specifies a setting name and value in a collection of settings for a role. The setting name is defined by a string for the `name` attribute and the setting value is defined by a string for the `value` attribute.|
| Certificate | Optional. Specifies the name, thumbprint, and algorithm of a service certificate that is to be associated with the role. The certificate name is defined by a string for the `name` attribute. The certificate thumbprint is defined by a string of hexadecimal numbers containing no spaces for the `thumbprint` attribute. The hexadecimal numbers must be represented using digits and uppercase alpha characters. The certificate algorithm is defined by a string for the `thumbprintAlgorithm` attribute.|

## See Also
[Cloud Service (classic) Configuration Schema](schema-cscfg-file.md)