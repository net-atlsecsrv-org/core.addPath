---
title: 'Azure AD Connect cloud sync deep dive - how it works'
description: This topic provides deep dive information on how cloud sync works.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
---

# Cloud sync deep dive - how it works

## Overview of components

![How it works](media/concept-how-it-works/how-1.png)

Cloud sync is built on top of the Azure AD services and has 2 key components:

- **Provisioning agent**: The Azure AD Connect cloud provisioning agent is the same agent as Workday inbound and built on the same server-side technology as app proxy and Pass Through Authentication. It requires and outbound connection only and agents are auto-updated. 
- **Provisioning service**: Same provisioning service as outbound provisioning and Workday inbound provisioning which uses a scheduler-based model. In case of cloud sync, the changes are provisioned every 2 mins.


## Initial setup
During initial setup, the a few things are done that makes cloud sync happen.  These are: 

- **During agent installation**: You configure the agent for the AD domains you want to provision from.  This configuration registers the domains in the hybrid identity service and establishes an outbound connection to the service bus listening for requests.
- **When you enable provisioning**: You select the AD domain and enable provisioning which runs every 2 mins. Optionally you may deselect password hash sync and define notification email. You can also manage attribute transformation using Microsoft Graph APIs.


## Agent installation
The following is a walk-through of what occurs when the cloud provisioning agent is installed.

- First, the Installer installs the Agent binaries and the Agent Service running under the Virtual Service Account (NETWORK SERVICE\AADProvisioningAgent).  A virtual service account is a special type of account that does not have a password and is managed by Windows.
- The Installer then starts the Wizard.
- The Wizard will prompt for Azure AD credentials, will then authenticate, and retrieve a token.
- The wizard then asks for the current machine Domain Administrators credentials.
- Using these credentials, the agent general managed service account (GMSA) for this domain is either created or located and reused if it already exists.
- The agent service is now reconfigured to run under the GMSA.
- The wizard now asks for domain configuration along with the Enterprise Admin (EA)/Domain Admin(DA) Account for each domain you want the agent to service.
- The GMSA account is then updated with permissions that enable it access to each domain entered above.
- Next, the wizard triggers agent registration
- The agent creates a certificate and using the Azure AD token, registers itself and the certificate with the Hybrid Identity Service(HIS) Registration Service
- The Wizard triggers an AgentResourceGrouping call. This call to HIS Admin Service is to assign the agent to one or more AD Domains in the HIS configuration.
- The wizard now restarts the agent service.
- The agent calls a Bootstrap Service on restart (and every 10 mins afterwards) to check for configuration updates.  The bootstrap service validates the agent identity.  It also updates the last bootstrap time.  This is important because if agents don't bootstrap, they are not getting updated Service Bus endpoints and may not be able to receive requests. 


## What is System for Cross-domain Identity Management (SCIM)?

The [SCIM specification](https://tools.ietf.org/html/draft-scim-core-schema-01) is a standard that is used to automate the exchanging of user or group identity information between identity domains such as Azure AD. SCIM is becoming the de facto standard for provisioning and, when used in conjunction with federation standards like SAML or OpenID Connect, provides administrators an end-to-end standards-based solution for access management.

The Azure AD Connect cloud provisioning agent uses SCIM with Azure AD to provision and deprovision users and groups.

## Synchronization flow
![provisioning](media/concept-how-it-works/provisioning-4.png)
Once you have installed the agent and enabled provisioning, the following flow occurs.

1.  Once configured, the Azure AD Provisioning service calls the Azure AD hybrid service to add a request to the Service bus. The agent constantly maintains an outbound connection to the Service Bus listening for requests and picks up the System for Cross-domain Identity Management (SCIM) request immediately. 
2.  The agent breaks up the request into separate queries based on object type. 
3.  AD returns the result to the agent and the agent filters this data before sending it to Azure AD.  
4.  Agent returns the SCIM response to Azure AD.  These responses are based on the filtering that happened within the agent.  The agent uses scoping to filter the results. 
5.  The provisioning service writes the changes to Azure AD.
6. If this is a delta Sync as opposed to a full sync, then cookie/watermark is used. New queries will get changes from that cookie/watermark onwards.

## Supported scenarios:
The following scenarios are supported for cloud sync.


- **Existing hybrid customer with a new forest**: Azure AD Connect sync is used for primary forests. Cloud sync is used for provisioning from an AD forest (including disconnected). For more information see the tutorial [here](tutorial-existing-forest.md).

 ![Existing hybrid](media/tutorial-existing-forest/existing-forest-new-forest-2.png)
- **New hybrid customer**:      Azure AD Connect sync is not used. Cloud sync is used for provisioning from an AD forest.  For more information see the tutorial [here](tutorial-single-forest.md).
 
 ![New customers](media/tutorial-single-forest/diagram-2.png)

- **Existing hybrid customer**: Azure AD Connect sync is used for primary forests. Cloud sync is piloted for a small set of users in the primary forests [here](tutorial-existing-forest.md).

 ![Existing pilot](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

For more information, see [Supported topologies](plan-cloud-sync-topologies.md).



## Next steps 

- [What is provisioning?](what-is-provisioning.md)
- [What is Azure AD Connect cloud sync?](what-is-cloud-sync.md)
