---
# Mandatory fields.
title: Custom models
titleSuffix: Azure Digital Twins
description: Understand how Azure Digital Twins uses user-defined models to describe entities in your environment.
author: baanders
ms.author: baanders # Microsoft employees only
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins

# Optional fields. Don't forget to remove # if you need a field.
# ms.custom: can-be-multiple-comma-separated
# ms.reviewer: MSFT-alias-of-reviewer
# manager: MSFT-alias-of-manager-or-PM-counterpart
---

# Understand twin models in Azure Digital Twins

A key characteristic of Azure Digital Twins is the ability to define your own vocabulary and build your twin graph in the self-defined terms of your business. This capability is provided through user-defined **models**. You can think of models as the nouns in a description of your world. 

A model is similar to a **class** in an object-oriented programming language, defining a data shape for one particular concept in your real work environment. Models have names (such as *Room* or *TemperatureSensor*), and contain elements such as properties, telemetry/events, and commands that describe what this type of entity in your environment can do. Later, you will use these models to create [**digital twins**](concepts-twins-graph.md) that represent specific entities that meet this type description.

Models are written using the JSON-LD-based **Digital Twin Definition Language (DTDL)**.  

## Digital Twin Definition Language (DTDL) for writing models

Models for Azure Digital Twins are defined using the Digital Twins Definition language (DTDL). DTDL is based on JSON-LD and is programming-language independent. DTDL is not exclusive to Azure Digital Twins, but is also used to represent device data in other IoT services such as [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md). 

Azure Digital Twins uses **DTDL _version 2_**. For more information about this version of DTDL, see its spec documentation in GitHub: [*Digital Twins Definition Language (DTDL) - version 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). Use of DTDL _version 1_ with Azure Digital Twins has now been deprecated.

> [!NOTE] 
> Not all services that use DTDL implement the exact same features of DTDL. For example, IoT Plug and Play does not use the DTDL features that are for graphs, while Azure Digital Twins does not currently implement DTDL commands.
>
> For more information on the DTDL features that are specific to Azure Digital Twins, see the section later in this article on [Azure Digital Twins DTDL implementation specifics](#azure-digital-twins-dtdl-implementation-specifics).

## Elements of a model

Within a model definition, the top-level code item is an **interface**. This encapsulates the entire model, and the rest of the model is defined within the interface. 

A DTDL model interface may contain zero, one, or many of each of the following fields:
* **Property** - Properties are data fields that represent the state of an entity (like the properties in many object-oriented programming languages). Properties have backing storage and can be read at any time.
* **Telemetry** - Telemetry fields represent measurements or events, and are often used to describe device sensor readings. Unlike properties, telemetry is not stored on a digital twin; it is a series of time-bound data events that need to be handled as they occur. For more on the differences between property and telemetry, see the [*Properties vs. telemetry*](#properties-vs-telemetry) section below.
* **Component** - Components allow you to build your model interface as an assembly of other interfaces, if you want. An example of a component is a *frontCamera* interface (and another component interface *backCamera*) that are used in defining a model for a *phone*. You must first define an interface for *frontCamera* as though it were its own model, and then you can reference it when defining *Phone*.

    Use a component to describe something that is an integral part of your solution but doesn't need a separate identity, and doesn't need to be created, deleted, or rearranged in the twin graph independently. If you want entities to have independent existences in the twin graph, represent them as separate digital twins of different models, connected by *relationships* (see next bullet).
    
    >[!TIP] 
    >Components can also be used for organization, to group sets of related properties within a model interface. In this situation, you can think of each component as a namespace or "folder" inside the interface.
* **Relationship** - Relationships let you represent how a digital twin can be involved with other digital twins. Relationships can represent different semantic meanings, such as *contains* ("floor contains room"), *cools* ("hvac cools room"), *isBilledTo* ("compressor is billed to user"), etc. Relationships allow the solution to provide a graph of interrelated entities.

> [!NOTE]
> The [spec for DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) also defines **Commands**, which are methods that can be executed on a digital twin (like a reset command, or a command to switch a fan on or off). However, *commands are not currently supported in Azure Digital Twins.*

### Properties vs. telemetry

Here is some additional guidance on distinguishing between DTDL **property** and **telemetry** fields in Azure Digital Twins.

The difference between properties and telemetry for Azure Digital Twins models is as follows:
* **Properties** are expected to have backing storage. This means that you can read a property at any time and retrieve its value. If the property is writeable, you can also store a value in the property.  
* **Telemetry** is more like a stream of events; it's a set of data messages that have short lifespans. If you don't set up listening for the event and actions to take when it happens, there is no trace of the event at a later time. You can't come back ot it and read it later. 
  - In C# terms, telemetry is like a C# event. 
  - In IoT terms, telemetry is typically a single measurement sent by a device.

**Telemetry** is often used with IoT devices, because many devices are not capable of, or interested in, storing the measurement values they generate. They just send them out as a stream of "telemetry" events. In this case, you can't inquire on the device at any time for the latest value of the telemetry field. Instead, you'll need to listen to the messages from the device and take actions as the messages arrive. 

As a result, when designing a model in Azure Digital Twins, you will probably use **properties** in most cases to model your twins. This allows you to have the backing storage and the ability to read and query the data fields.

Telemetry and properties often work together to handle data ingress from devices. As all ingress to Azure Digital Twins is via [APIs](how-to-use-apis-sdks.md), you will typically use your ingress function to read telemetry or property events from devices, and set a property in ADT in response. 

You can also publish a telemetry event from the Azure Digital Twins API. As with other telemetry, that is a short-lived event that requires a listener to handle.

### Azure Digital Twins DTDL implementation specifics

For a DTDL model to be compatible with Azure Digital Twins, it must meet these requirements.

* All top-level DTDL elements in a model must be of type *interface*. This is because Azure Digital Twins model APIs can receive JSON objects that represent either an interface or an array of interfaces. As a result, no other DTDL element types are allowed at the top level.
* DTDL for Azure Digital Twins must not define any *commands*.
* Azure Digital Twins only allows a single level of component nesting. This means that an interface that's being used as a component can't have any components itself. 
* Interfaces can't be defined inline within other DTDL interfaces; they must be defined as separate top-level entities with their own IDs. Then, when another interface wants to include that interface as a component or through inheritance, it can reference its ID.

Azure Digital Twins also does not observe the `writable` attribute on properties or relationships. Although this can be set as per DTDL specifications, the value isn't used by Azure Digital Twins. Instead, these are always treated as writable by external clients that have general write permissions to the Azure Digital Twins service.

## Example model code

Twin type models can be written in any text editor. The DTDL language follows JSON syntax, so you should store models with the extension *.json*. Using the JSON extension will enable many programming text editors to provide basic syntax checking and highlighting for your DTDL documents. There is also a [DTDL extension](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) available for [Visual Studio Code](https://code.visualstudio.com/).

This section contains an example of a typical model, written as a DTDL interface. The model describes **planets**, each with a name, a mass, and a temperature.
 
Consider that planets may also interact with **moons** that are their satellites, and may contain **craters**. In the example below, the `Planet` model expresses connections to these other entities by referencing two external models—`Moon` and `Crater`. These models are also defined in the example code below, but are kept very simple so as not to detract from the primary `Planet` example.

```json
[
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "Temperature",
        "schema": "double"
      },
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:contoso:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

The fields of the model are:

| Field | Description |
| --- | --- |
| `@id` | An identifier for the model. Must be in the format `dtmi:<domain>:<unique model identifier>;<model version number>`. |
| `@type` | Identifies the kind of information being described. For an interface, the type is *Interface*. |
| `@context` | Sets the [context](https://niem.github.io/json/reference/json-ld/context/) for the JSON document. Models should use `dtmi:dtdl:context;2`. |
| `displayName` | [optional] Allows you to give the model a friendly name if desired. |
| `contents` | All remaining interface data is placed here, as an array of attribute definitions. Each attribute must provide a `@type` (*Property*, *Telemetry*, *Command*, *Relationship*, or *Component*) to identify the sort of interface information it describes, and then a set of properties that define the actual attribute (for example, `name` and `schema` to define a *Property*). |

> [!NOTE]
> Note that the component interface (*Crater* in this example) is defined in the same array as the interface that uses it (*Planet*). Components must be defined this way in API calls in order for the interface to be found.

### Possible schemas

As per DTDL, the schema for *Property* and *Telemetry* attributes can be of standard primitive types—`integer`, `double`, `string`, and `Boolean`—and other types such as `DateTime` and `Duration`. 

In addition to primitive types, *Property* and *Telemetry* fields can have these complex types:
* `Object`
* `Map`
* `Enum`

*Telemetry* fields also support `Array`.

### Model inheritance

Sometimes, you may want to specialize a model further. For example, it might be useful to have a generic model *Room*, and specialized variants *ConferenceRoom* and *Gym*. To express specialization, DTDL supports inheritance: interfaces can inherit from one or more other interfaces. 

The following example re-imagines the *Planet* model from the earlier DTDL example as a subtype of a larger *CelestialBody* model. The "parent" model is defined first, and then the "child" model builds on it by using the field `extends`.

```json
[
  {
    "@id": "dtmi:com:contoso:CelestialBody;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Celestial body",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "extends": "dtmi:com:contoso:CelestialBody;1",
    "contents": [
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

In this example, *CelestialBody* contributes a name, a mass, and a temperature to *Planet*. The `extends` section is an interface name, or an array of interface names (allowing the extending interface to inherit from multiple parent models if desired).

Once inheritance is applied, the extending interface exposes all properties from the entire inheritance chain.

The extending interface cannot change any of the definitions of the parent interfaces; it can only add to them. It also cannot redefine a capability already defined in any of its parent interfaces (even if the capabilities are defined to be the same). For example, if a parent interface defines a `double` property *mass*, the extending interface cannot contain a declaration of *mass*, even if it's also a `double`.

## Validating models

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## Next steps

See how to manage models with the DigitalTwinsModels APIs:
* [*How-to: Manage custom models*](how-to-manage-model.md)

Or, learn about how digital twins are created based on models:
* [*Concepts: Digital twins and the twin graph*](concepts-twins-graph.md)

