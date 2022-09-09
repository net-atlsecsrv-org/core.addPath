---
# Mandatory fields.
title: Converting ontologies
titleSuffix: Azure Digital Twins
description: Understand the process of converting industry-standard models into DTDL for Azure Digital Twins
author: baanders
ms.author: baanders # Microsoft employees only
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins

# Optional fields. Don't forget to remove # if you need a field.
# ms.custom: can-be-multiple-comma-separated
# ms.reviewer: MSFT-alias-of-reviewer
# manager: MSFT-alias-of-manager-or-PM-counterpart
---

# Convert industry-standard ontologies to DTDL for Azure Digital Twins

Most [ontologies](concepts-ontologies.md) are based on semantic web standards such as [OWL](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF), and [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

To use a model with Azure Digital Twins, it must be in DTDL format. This articles describes general design guidance in the form of a **conversion pattern** for converting RDF-based models to DTDL so that they can be used with Azure Digital Twins. 

The article also contains [**sample converter code**](#converter-samples) for RDF and OWL converters, which can be extended for other schemas in the building industry.

## Conversion pattern

There are several third-party libraries that can be used when converting RDF-based models to DTDL. Some of these libraries allow you to load your model file into a graph. You can loop through the graph looking for specific RDFS and OWL constructs, and convert these to DTDL.   

The following table is an example of how RDFS and OWL constructs can be mapped to DTDL. 

| RDFS/OWL concept | RDFS/OWL construct | DTDL concept | DTDL construct |
| --- | --- | --- | --- |
| Classes | `owl:Class`<br>IRI suffix<br>``rdfs:label``<br>``rdfs:comment`` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Subclasses | `owl:Class`<br>IRI suffix<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Datatype Properties | `owl:DatatypeProperty`<br>`rdfs:label` or `INode`<br>`rdfs:label`<br>`rdfs:range` | Interface Properties | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Object Properties | `owl:ObjectProperty`<br>`rdfs:label` or `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relationship | `type:Relationship`<br>`name`<br>`target` (or omitted if no `rdfs:range`)<br>`comment`<br>`displayName`<br>

The following C# code snippet shows how an RDF model file is loaded into a graph and converted to DTDL, using the [**dotNetRDF**](https://www.dotnetrdf.org/) library. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

## Converter samples

### RDF converter application 

There is a sample application available that converts an RDF-based model file to [DTDL (version 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) for use by the Azure Digital Twins service. It has been validated for the [Brick](https://brickschema.org/ontology/) schema, and can be extended for other schemas in the building industry (such as [Building Topology Ontology (BOT)](https://w3c-lbd-cg.github.io/bot/), [Semantic Sensor Network](https://www.w3.org/TR/vocab-ssn/), or [buildingSmart Industry Foundation Classes (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

The sample is a .NET Core command-line application called **RdfToDtdlConverter**.

You can get the sample here: [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

To download the code to your machine, hit the *Download ZIP* button underneath the title on the sample landing page. This will download a *ZIP* file under the name *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip*, which you can then unzip and explore.

You can use this sample to see the conversion patterns in context, and to have as a building block for your own applications performing model conversions according to your own specific needs.

### OWL2DTDL converter 

The [**OWL2DTDL Converter**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL) is a sample that translates an OWL ontology into a set of DTDL interface declarations, which can be used with the Azure Digital Twins service. It also works for ontology networks, made of one root ontology reusing other ontologies through `owl:imports` declarations.

This converter was used to translate the [Real Estate Core Ontology](https://doc.realestatecore.io/3.1/full.html) to DTDL and can be used for any OWL-based ontology.

## Next steps 

* Learn more about extending industry-standard ontologies to meet your specifications: [*Concepts: Extending industry ontologies*](concepts-ontologies-extend.md).

* Or, continue on the path for developing models based on ontologies: [*Using ontology strategies in a model development path*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).