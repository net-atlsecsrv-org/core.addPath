---
 title: Azure IoT Hub MQTT 5 API reference (preview)
 description: Learn about IoT Hub's MQTT 5 API reference
 services: iot-hub
 author: jlian
 ms.service: iot-fundamentals
 ms.topic: reference
 ms.date: 11/19/2020
 ms.author: jlian
---

# IoT Hub data plane MQTT 5 API reference

This document defines operations available in version 2.0 (api-version: `2020-10-01-preview`) of IoT Hub data plane API.

## Operations

### Get Twin

Get Twin state

#### Request

**Topic name:** `$iothub/twin/get`

**Properties**:
 none

**Payload**: empty

#### Success Response

**Properties**:
 none

**Payload**: Twin

#### Alternative Responses

| Status | Name | Description |
| :----- | :--- | :---------- |
| 0100 |  Bad Request | Operation message is malformed and cannot be processed. |
| 0101 |  Not Authorized | Client is not authorized to perform the operation. |
| 0102 |  Not Allowed | Operation is not allowed. |
| 0501 |  Throttled | request rate is too high per SKU |
| 0502 |  Quota Exceeded | daily quota per current SKU is exceeded |
| 0601 |  Server Error | internal server error |
| 0602 |  Timeout | operation timed out before it could be completed |
| 0603 |  Server Busy | server busy |

#### Pseudo-code Sample

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### Patch Twin Reported

Patch Twin's reported state

#### Request

**Topic name:** `$iothub/twin/patch/reported`

**Properties**:

| Name | Type | Required | Description |
| :--- | :--- | :------- | :---------- |
| if-version | u64 | no |  |

**Payload**: TwinState

#### Success Response

**Properties**:

| Name | Type | Required | Description |
| :--- | :--- | :------- | :---------- |
| version | u64 | yes | Version of reported state after patch was applied |

**Payload**: empty

#### Alternative Responses

| Status | Name | Description |
| :----- | :--- | :---------- |
| 0104 |  Precondition Failed | precondition was not met resulting in request being canceled |
| 0100 |  Bad Request | Operation message is malformed and cannot be processed. |
| 0101 |  Not Authorized | Client is not authorized to perform the operation. |
| 0102 |  Not Allowed | Operation is not allowed. |
| 0501 |  Throttled | request rate is too high per SKU |
| 0502 |  Quota Exceeded | daily quota per current SKU is exceeded |
| 0601 |  Server Error | internal server error |
| 0602 |  Timeout | operation timed out before it could be completed |
| 0603 |  Server Busy | server busy |

#### Pseudo-code Sample

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/reported
    [if-version: <u64>]
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### Receive Commands

Receive and handle commands

#### Message

**Topic name:** `$iothub/commands`

**Properties**:

| Name | Type | Required | Description |
| :--- | :--- | :------- | :---------- |
| sequence-no | u64 | yes | Sequence number of the message |
| enqueued-time | time | yes | Timestamp of when the message entered the system |
| delivery-count | u32 | yes | Number of times the message delivery was attempted |
| creation-time | time | no | Timestamp of when the message was created (provided by sender) |
| message-id | string | no | Message identity (provided by sender) |
| user-id | string | no | User identity (provided by sender) |
| correlation-id | string | no | Correlation identity (provided by sender) |
| Content Type | string | no | determines Content Type of the payload |
| content-encoding | string | no | determines Content Encoding of the payload |

**Payload**: any byte sequence

#### Success Acknowledgment

Indicates command was accepted for handling by the client

**Properties**:
 none

**Payload**: empty

#### Alternative Acknowledgments

| Reason Code | Status | Name | Description |
| :---------- | :----- | :--- | :---------- |
| 131 | 0603 | Abandon | Indicates command will not be processed at this time and should be redelivered in the future. |
| 131 | 0100 | Reject | Indicates command is rejected by the client and should not be attempted again. |

#### Pseudo-code Sample

```

-> SUBSCRIBE
    - Topic: $iothub/commands
      QoS: 1
<- PUBLISH
    QoS: 1
    Topic: $iothub/commands
    sequence-no: <u64>enqueued-time: <time>delivery-count: <u32>[creation-time: <time>][message-id: <string>][user-id: <string>][correlation-id: <string>][Content Type: <string>][content-encoding: <string>]
    Payload: ...

-> PUBACK

```

### Receive Direct Methods

Receive and handle Direct Method calls

#### Request

**Topic name:** `$iothub/methods/{name}`

**Properties**:
 none

**Payload**: any byte sequence

#### Success Response

**Properties**:

| Name | Type | Required | Description |
| :--- | :--- | :------- | :---------- |
| response-code | u32 | yes |  |

**Payload**: any byte sequence

#### Alternative Responses

| Status | Name | Description |
| :----- | :--- | :---------- |
| 06A0 |  Unavailable | Indicates that client is not reachable through this connection. |

#### Pseudo-code Sample

```

-> SUBSCRIBE
    - Topic: methods/{name}
      QoS: 0
<- SUBACK
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/{name}
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### Receive Twin Desired State Changes

Receive updates to Twin's desired state

#### Message

**Topic name:** `$iothub/twin/patch/desired`

**Properties**:

| Name | Type | Required | Description |
| :--- | :--- | :------- | :---------- |
| version | u64 | yes | Version of desired state matching this update |

**Payload**: TwinState

#### Pseudo-code Sample

```

-> SUBSCRIBE
    - Topic: $iothub/twin/patch/desired
      QoS: 0
<- PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/desired
    version: <u64>
    Payload: ...

```

### Send Telemetry

Post message to telemetry channel - EventHubs by default or other endpoint via routing configuration.

#### Message

**Topic name:** `$iothub/telemetry`

**Properties**:

| Name | Type | Required | Description |
| :--- | :--- | :------- | :---------- |
| Content Type | string | no | translates into `content-type` system property on posted message |
| content-encoding | string | no | translates into `content-encoding` system property on posted message |
| message-id | string | no | translates into `message-id` system property on posted message |
| user-id | string | no | translates into `user-id` system property on posted message |
| correlation-id | string | no | translates into `correlation-id` system property on posted message |
| creation-time | time | no | translates into `iothub-creation-time-utc` property on posted message |

**Payload**: any byte sequence

#### Success Acknowledgment

Message has been successfully posted to telemetry channel

**Properties**:
 none

**Payload**: empty

#### Alternative Acknowledgments

| Reason Code | Status | Name | Description |
| :---------- | :----- | :--- | :---------- |
| 131 | 0100 | Bad Request | Operation message is malformed and cannot be processed. |
| 135 | 0101 | Not Authorized | Client is not authorized to perform the operation. |
| 131 | 0102 | Not Allowed | Operation is not allowed. |
| 131 | 0601 | Server Error | internal server error |
| 151 | 0501 | Throttled | request rate is too high per SKU |
| 151 | 0502 | Quota Exceeded | daily quota per current SKU is exceeded |
| 131 | 0602 | Timeout | operation timed out before it could be completed |
| 131 | 0603 | Server Busy | server busy |

#### Pseudo-code Sample

```
-> PUBLISH
    QoS: 1
    Topic: $iothub/telemetry
    [Content Type: <string>]
    [content-encoding: <string>]
    [message-id: <string>]
    [user-id: <string>]
    [correlation-id: <string>]
    [creation-time: <time>]

<- PUBACK

```

## Responses

### Bad Request

Operation message is malformed and cannot be processed.

**Reason Code:** `131`

**Status:** `0100`

**Properties**:

| Name | Type | Required | Description |
| :--- | :--- | :------- | :---------- |
| reason | string | no | contains information on what specifically is not valid about the message |

**Payload**: empty

### Conflict

Operation is in conflict with another ongoing operation.

**Reason Code:** `131`

**Status:** `0103`

**Properties**:

| Name | Type | Required | Description |
| :--- | :--- | :------- | :---------- |
| trace-id | string | no | trace ID for correlation with additional diagnostics for the error |
| reason | string | no | contains information on what specifically is not valid about the message |

**Payload**: empty

### Not Allowed

Operation is not allowed.

**Reason Code:** `131`

**Status:** `0102`

**Properties**:

| Name | Type | Required | Description |
| :--- | :--- | :------- | :---------- |
| reason | string | no | contains information on what specifically is not valid about the message |

**Payload**: empty

### Not Authorized

Client is not authorized to perform the operation.

**Reason Code:** `135`

**Status:** `0101`

**Properties**:

| Name | Type | Required | Description |
| :--- | :--- | :------- | :---------- |
| trace-id | string | no | trace ID for correlation with additional diagnostics for the error |

**Payload**: empty

### Not Found

requested resource does not exist

**Reason Code:** `131`

**Status:** `0504`

**Properties**:

| Name | Type | Required | Description |
| :--- | :--- | :------- | :---------- |
| reason | string | no | contains information on what specifically is not valid about the message |

**Payload**: empty

### Not Modified

Resource was not modified based on provided precondition.

**Reason Code:** `0`

**Status:** `0001`

**Properties**:
 none

**Payload**: empty

### Precondition Failed

Precondition was not met resulting in request being canceled

**Reason Code:** `131`

**Status:** `0104`

**Properties**:
 none

**Payload**: empty

### Quota Exceeded

daily quota per current SKU is exceeded

**Reason Code:** `151`

**Status:** `0502`

**Properties**:
 none

**Payload**: empty

### Resource Exhausted

resource has no capacity to complete the operation

**Reason Code:** `131`

**Status:** `0503`

**Properties**:

| Name | Type | Required | Description |
| :--- | :--- | :------- | :---------- |
| reason | string | no | contains information on what specifically is not valid about the message |

**Payload**: empty

### Server Busy

server busy

**Reason Code:** `131`

**Status:** `0603`

**Properties**:

| Name | Type | Required | Description |
| :--- | :--- | :------- | :---------- |
| trace-id | string | no | trace ID for correlation with additional diagnostics for the error |

**Payload**: empty

### Server Error

internal server error

**Reason Code:** `131`

**Status:** `0601`

**Properties**:

| Name | Type | Required | Description |
| :--- | :--- | :------- | :---------- |
| trace-id | string | no | trace ID for correlation with additional diagnostics for the error |

**Payload**: empty

### Target Failed

Target responded but the response was invalid or malformed

**Reason Code:** `131`

**Status:** `06A2`

**Properties**:

| Name | Type | Required | Description |
| :--- | :--- | :------- | :---------- |
| reason | string | no | contains information on what specifically is not valid about the message |

**Payload**: empty

### Target Timeout

timed out waiting for target to complete the request

**Reason Code:** `131`

**Status:** `06A1`

**Properties**:

| Name | Type | Required | Description |
| :--- | :--- | :------- | :---------- |
| trace-id | string | no | trace ID for correlation with additional diagnostics for the error |
| reason | string | no | contains information on what specifically is not valid about the message |

**Payload**: empty

### Target Unavailable

Target is unreachable to complete the request

**Reason Code:** `131`

**Status:** `06A0`

**Properties**:
 none

**Payload**: empty

### Throttled

request rate is too high per SKU

**Reason Code:** `151`

**Status:** `0501`

**Properties**:
 none

**Payload**: empty

### Timeout

operation timed out before it could be completed

**Reason Code:** `131`

**Status:** `0602`

**Properties**:

| Name | Type | Required | Description |
| :--- | :--- | :------- | :---------- |
| trace-id | string | no | trace ID for correlation with additional diagnostics for the error |

**Payload**: empty

