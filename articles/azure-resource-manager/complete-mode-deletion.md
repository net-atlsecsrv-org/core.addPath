---
title: Azure Resource Manager complete mode deletion
description: Shows how resource types handle complete mode deletion in Azure Resource Manager templates.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/27/2019
ms.author: tomfitz
---

# Deletion of Azure resources for complete mode deployments

This article describes how resource types handle deletion when not in a template that is deployed in complete mode.

The resource types marked with **Yes** are deleted when the type isn't in the template deployed with complete mode.

The resource types marked with **No** aren't automatically deleted when not in the template; however, they're deleted if the parent resource is deleted. For a full description of the behavior, see [Azure Resource Manager deployment modes](deployment-modes.md).

Jump to a resource provider namespace:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.Azconfig](#microsoftazconfig)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | DomainServices | Yes |
> | DomainServices/oucontainer | No |
> | DomainServices/ReplicaSets | Yes |

## Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | supportProviders | No |

## Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | aadsupportcases | No |
> | addsservices | No |
> | agents | No |
> | anonymousapiusers | No |
> | configuration | No |
> | logs | No |
> | reports | No |
> | servicehealthmetrics | No |
> | services | No |

## Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | configurations | No |
> | generateRecommendations | No |
> | metadata | No |
> | recommendations | No |
> | suppressions | No |

## Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | actionRules | Yes |
> | alerts | No |
> | alertsList | No |
> | alertsMetaData | No |
> | alertsSummary | No |
> | alertsSummaryList | No |
> | feedback | No |
> | smartDetectorAlertRules | Yes |
> | smartDetectorRuntimeEnvironments | No |
> | smartGroups | No |

## Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | servers | Yes |

## Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | reportFeedback | No |
> | service | Yes |
> | validateServiceName | No |

## Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | configurationStores | Yes |
> | configurationStores/eventGridFilters | No |

## Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | Spring | Yes |

## Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | attestationProviders | No |

## Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | classicAdministrators | No |
> | dataAliases | No |
> | denyAssignments | No |
> | elevateAccess | No |
> | findOrphanRoleAssignments | No |
> | locks | No |
> | permissions | No |
> | policyAssignments | No |
> | policyDefinitions | No |
> | policySetDefinitions | No |
> | providerOperations | No |
> | roleAssignments | No |
> | roleDefinitions | No |

## Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | automationAccounts | Yes |
> | automationAccounts/configurations | Yes |
> | automationAccounts/jobs | No |
> | automationAccounts/runbooks | Yes |
> | automationAccounts/softwareUpdateConfigurations | No |
> | automationAccounts/webhooks | No |

## Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | configurationStores | Yes |
> | configurationStores/eventGridFilters | No |

## Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | environments | No |
> | environments/accounts | No |
> | environments/accounts/namespaces | No |
> | environments/accounts/namespaces/configurations | No |

## Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | b2cDirectories | Yes |
> | b2ctenants | No |

## Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | hybridDataManagers | Yes |
> | postgresInstances | Yes |
> | sqlBigDataClusters | Yes |
> | sqlInstances | Yes |
> | sqlServerRegistrations | Yes |
> | sqlServerRegistrations/sqlServers | No |

## Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | registrations | Yes |
> | registrations/customerSubscriptions | No |
> | registrations/products | No |

## Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | batchAccounts | Yes |

## Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | billingAccounts | No |
> | billingAccounts/agreements | No |
> | billingAccounts/billingPermissions | No |
> | billingAccounts/billingProfiles | No |
> | billingAccounts/billingProfiles/billingPermissions | No |
> | billingAccounts/billingProfiles/billingRoleAssignments | No |
> | billingAccounts/billingProfiles/billingRoleDefinitions | No |
> | billingAccounts/billingProfiles/billingSubscriptions | No |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | No |
> | billingAccounts/billingProfiles/customers | No |
> | billingAccounts/billingProfiles/invoices | No |
> | billingAccounts/billingProfiles/invoices/pricesheet | No |
> | billingAccounts/billingProfiles/invoiceSections | No |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | No |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | No |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | No |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | No |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | No |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | No |
> | billingAccounts/billingProfiles/invoiceSections/products | No |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | No |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | No |
> | billingAccounts/billingProfiles/invoiceSections/transactions | No |
> | billingAccounts/billingProfiles/invoiceSections/transfers | No |
> | billingAccounts/BillingProfiles/patchOperations | No |
> | billingAccounts/billingProfiles/paymentMethods | No |
> | billingAccounts/billingProfiles/policies | No |
> | billingAccounts/billingProfiles/pricesheet | No |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | No |
> | billingAccounts/billingProfiles/products | No |
> | billingAccounts/billingProfiles/transactions | No |
> | billingAccounts/billingRoleAssignments | No |
> | billingAccounts/billingRoleDefinitions | No |
> | billingAccounts/billingSubscriptions | No |
> | billingAccounts/createBillingRoleAssignment | No |
> | billingAccounts/createInvoiceSectionOperations | No |
> | billingAccounts/customers | No |
> | billingAccounts/customers/billingPermissions | No |
> | billingAccounts/customers/billingSubscriptions | No |
> | billingAccounts/customers/initiateTransfer | No |
> | billingAccounts/customers/policies | No |
> | billingAccounts/customers/products | No |
> | billingAccounts/customers/transactions | No |
> | billingAccounts/customers/transfers | No |
> | billingAccounts/departments | No |
> | billingAccounts/enrollmentAccounts | No |
> | billingAccounts/invoices | No |
> | billingAccounts/invoiceSections | No |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | No |
> | billingAccounts/invoiceSections/billingSubscriptions | No |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | No |
> | billingAccounts/invoiceSections/elevate | No |
> | billingAccounts/invoiceSections/initiateTransfer | No |
> | billingAccounts/invoiceSections/patchOperations | No |
> | billingAccounts/invoiceSections/productMoveOperations | No |
> | billingAccounts/invoiceSections/products | No |
> | billingAccounts/invoiceSections/products/transfer | No |
> | billingAccounts/invoiceSections/products/updateAutoRenew | No |
> | billingAccounts/invoiceSections/transactions | No |
> | billingAccounts/invoiceSections/transfers | No |
> | billingAccounts/lineOfCredit | No |
> | billingAccounts/patchOperations | No |
> | billingAccounts/paymentMethods | No |
> | billingAccounts/products | No |
> | billingAccounts/transactions | No |
> | billingPeriods | No |
> | billingPermissions | No |
> | billingProperty | No |
> | billingRoleAssignments | No |
> | billingRoleDefinitions | No |
> | createBillingRoleAssignment | No |
> | departments | No |
> | enrollmentAccounts | No |
> | invoices | No |
> | transfers | No |
> | transfers/acceptTransfer | No |
> | transfers/declineTransfer | No |
> | transfers/operationStatus | No |
> | transfers/validateTransfer | No |
> | validateAddress | No |

## Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | mapApis | Yes |
> | updateCommunicationPreference | No |

## Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | blockchainMembers | Yes |
> | cordaMembers | Yes |
> | watchers | Yes |

## Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | blueprintAssignments | No |
> | blueprintAssignments/assignmentOperations | No |
> | blueprintAssignments/operations | No |
> | blueprints | No |
> | blueprints/artifacts | No |
> | blueprints/versions | No |
> | blueprints/versions/artifacts | No |

## Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | botServices | Yes |
> | botServices/channels | No |
> | botServices/connections | No |
> | languages | No |
> | templates | No |

## Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | Redis | Yes |
> | RedisConfigDefinition | No |

## Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | appliedReservations | No |
> | calculateExchange | No |
> | calculatePrice | No |
> | calculatePurchasePrice | No |
> | catalogs | No |
> | commercialReservationOrders | No |
> | exchange | No |
> | placePurchaseOrder | No |
> | reservationOrders | No |
> | reservationOrders/calculateRefund | No |
> | reservationOrders/merge | No |
> | reservationOrders/reservations | No |
> | reservationOrders/reservations/revisions | No |
> | reservationOrders/return | No |
> | reservationOrders/split | No |
> | reservationOrders/swap | No |
> | reservations | No |
> | resources | No |
> | validateReservationOrder | No |

## Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No |
> | CdnWebApplicationFirewallPolicies | Yes |
> | edgenodes | No |
> | profiles | Yes |
> | profiles/endpoints | Yes |
> | profiles/endpoints/customdomains | No |
> | profiles/endpoints/origins | No |
> | validateProbe | No |

## Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | certificateOrders | Yes |
> | certificateOrders/certificates | No |
> | validateCertificateRegistrationInformation | No |

## Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | capabilities | No |
> | domainNames | Yes |
> | domainNames/capabilities | No |
> | domainNames/internalLoadBalancers | No |
> | domainNames/serviceCertificates | No |
> | domainNames/slots | No |
> | domainNames/slots/roles | No |
> | domainNames/slots/roles/metricDefinitions | No |
> | domainNames/slots/roles/metrics | No |
> | moveSubscriptionResources | No |
> | operatingSystemFamilies | No |
> | operatingSystems | No |
> | quotas | No |
> | resourceTypes | No |
> | validateSubscriptionMoveAvailability | No |
> | virtualMachines | Yes |
> | virtualMachines/diagnosticSettings | No |
> | virtualMachines/metricDefinitions | No |
> | virtualMachines/metrics | No |

## Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | classicInfrastructureResources | No |

## Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | capabilities | No |
> | expressRouteCrossConnections | No |
> | expressRouteCrossConnections/peerings | No |
> | gatewaySupportedDevices | No |
> | networkSecurityGroups | Yes |
> | quotas | No |
> | reservedIps | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | No |
> | virtualNetworks/virtualNetworkPeerings | No |

## Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | capabilities | No |
> | disks | No |
> | images | No |
> | osImages | No |
> | osPlatformImages | No |
> | publicImages | No |
> | quotas | No |
> | storageAccounts | Yes |
> | storageAccounts/blobServices | No |
> | storageAccounts/fileServices | No |
> | storageAccounts/metricDefinitions | No |
> | storageAccounts/metrics | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/services | No |
> | storageAccounts/services/diagnosticSettings | No |
> | storageAccounts/services/metricDefinitions | No |
> | storageAccounts/services/metrics | No |
> | storageAccounts/tableServices | No |
> | storageAccounts/vmImages | No |
> | vmImages | No |

## Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | accounts | Yes |

## Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | RateCard | No |
> | UsageAggregates | No |

## Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | availabilitySets | Yes |
> | diskEncryptionSets | Yes |
> | disks | Yes |
> | galleries | Yes |
> | galleries/applications | No |
> | galleries/applications/versions | No |
> | galleries/images | No |
> | galleries/images/versions | No |
> | hostGroups | Yes |
> | hostGroups/hosts | Yes |
> | images | Yes |
> | proximityPlacementGroups | Yes |
> | restorePointCollections | Yes |
> | restorePointCollections/restorePoints | No |
> | sharedVMExtensions | Yes |
> | sharedVMExtensions/versions | No |
> | sharedVMImages | Yes |
> | sharedVMImages/versions | No |
> | snapshots | Yes |
> | virtualMachines | Yes |
> | virtualMachines/extensions | Yes |
> | virtualMachines/metricDefinitions | No |
> | virtualMachineScaleSets | Yes |
> | virtualMachineScaleSets/extensions | No |
> | virtualMachineScaleSets/networkInterfaces | No |
> | virtualMachineScaleSets/publicIPAddresses | No |
> | virtualMachineScaleSets/virtualMachines | No |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | No |

## Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | AggregatedCost | No |
> | Balances | No |
> | Budgets | No |
> | Charges | No |
> | CostTags | No |
> | credits | No |
> | events | No |
> | Forecasts | No |
> | lots | No |
> | Marketplaces | No |
> | Pricesheets | No |
> | products | No |
> | ReservationDetails | No |
> | ReservationRecommendations | No |
> | ReservationSummaries | No |
> | ReservationTransactions | No |
> | Tags | No |
> | tenants | No |
> | Terms | No |
> | UsageDetails | No |

## Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | containerGroups | Yes |
> | serviceAssociationLinks | No |

## Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | registries | Yes |
> | registries/builds | No |
> | registries/builds/cancel | No |
> | registries/builds/getLogLink | No |
> | registries/buildTasks | Yes |
> | registries/buildTasks/steps | No |
> | registries/eventGridFilters | No |
> | registries/generateCredentials | No |
> | registries/getBuildSourceUploadUrl | No |
> | registries/GetCredentials | No |
> | registries/importImage | No |
> | registries/queueBuild | No |
> | registries/regenerateCredential | No |
> | registries/regenerateCredentials | No |
> | registries/replications | Yes |
> | registries/runs | No |
> | registries/runs/cancel | No |
> | registries/scheduleRun | No |
> | registries/scopeMaps | No |
> | registries/tasks | Yes |
> | registries/tokens | No |
> | registries/updatePolicies | No |
> | registries/webhooks | Yes |
> | registries/webhooks/getCallbackConfig | No |
> | registries/webhooks/ping | No |

## Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | containerServices | Yes |
> | managedClusters | Yes |
> | openShiftManagedClusters | Yes |

## Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | accounts | Yes |

## Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | Alerts | No |
> | BillingAccounts | No |
> | Budgets | No |
> | CloudConnectors | No |
> | Connectors | Yes |
> | Departments | No |
> | Dimensions | No |
> | EnrollmentAccounts | No |
> | Exports | No |
> | ExternalBillingAccounts | No |
> | ExternalBillingAccounts/Alerts | No |
> | ExternalBillingAccounts/Dimensions | No |
> | ExternalBillingAccounts/Forecast | No |
> | ExternalBillingAccounts/Query | No |
> | ExternalSubscriptions | No |
> | ExternalSubscriptions/Alerts | No |
> | ExternalSubscriptions/Dimensions | No |
> | ExternalSubscriptions/Forecast | No |
> | ExternalSubscriptions/Query | No |
> | Forecast | No |
> | Query | No |
> | register | No |
> | Reportconfigs | No |
> | Reports | No |
> | Settings | No |
> | showbackRules | No |
> | Views | No |

## Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | requests | No |

## Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | associations | No |
> | resourceProviders | Yes |

## Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | jobs | Yes |

## Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Yes |

## Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | workspaces | Yes |
> | workspaces/virtualNetworkPeerings | No |

## Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | catalogs | Yes |
> | datacatalogs | Yes |
> | datacatalogs/datasources | No |
> | datacatalogs/datasources/scans | No |
> | datacatalogs/datasources/scans/datasets | No |
> | datacatalogs/datasources/scans/triggers | No |

## Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | dataFactories | Yes |
> | dataFactories/diagnosticSettings | No |
> | dataFactories/metricDefinitions | No |
> | dataFactorySchema | No |
> | factories | Yes |
> | factories/integrationRuntimes | No |

## Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | accounts | Yes |
> | accounts/dataLakeStoreAccounts | No |
> | accounts/storageAccounts | No |
> | accounts/storageAccounts/containers | No |
> | accounts/transferAnalyticsUnits | No |

## Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | accounts | Yes |
> | accounts/eventGridFilters | No |
> | accounts/firewallRules | No |

## Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | services | Yes |
> | services/projects | Yes |

## Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | accounts | Yes |
> | accounts/shares | No |
> | accounts/shares/datasets | No |
> | accounts/shares/invitations | No |
> | accounts/shares/providersharesubscriptions | No |
> | accounts/shares/synchronizationSettings | No |
> | accounts/sharesubscriptions | No |
> | accounts/sharesubscriptions/consumerSourceDataSets | No |
> | accounts/sharesubscriptions/datasetmappings | No |
> | accounts/sharesubscriptions/triggers | No |

## Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | servers | Yes |
> | servers/advisors | No |
> | servers/privateEndpointConnectionProxies | No |
> | servers/privateEndpointConnections | No |
> | servers/privateLinkResources | No |
> | servers/queryTexts | No |
> | servers/recoverableServers | No |
> | servers/topQueryStatistics | No |
> | servers/virtualNetworkRules | No |
> | servers/waitStatistics | No |

## Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | servers | Yes |
> | servers/advisors | No |
> | servers/privateEndpointConnectionProxies | No |
> | servers/privateEndpointConnections | No |
> | servers/privateLinkResources | No |
> | servers/queryTexts | No |
> | servers/recoverableServers | No |
> | servers/topQueryStatistics | No |
> | servers/virtualNetworkRules | No |
> | servers/waitStatistics | No |

## Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | serverGroups | Yes |
> | servers | Yes |
> | servers/advisors | No |
> | servers/keys | No |
> | servers/privateEndpointConnectionProxies | No |
> | servers/privateEndpointConnections | No |
> | servers/privateLinkResources | No |
> | servers/queryTexts | No |
> | servers/recoverableServers | No |
> | servers/topQueryStatistics | No |
> | servers/virtualNetworkRules | No |
> | servers/waitStatistics | No |
> | serversv2 | Yes |

## Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | artifactSources | Yes |
> | rollouts | Yes |
> | serviceTopologies | Yes |
> | serviceTopologies/services | Yes |
> | serviceTopologies/services/serviceUnits | Yes |
> | steps | Yes |

## Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | applicationgroups | Yes |
> | applicationgroups/applications | No |
> | applicationgroups/desktops | No |
> | applicationgroups/startmenuitems | No |
> | hostpools | Yes |
> | hostpools/sessionhosts | No |
> | hostpools/sessionhosts/usersessions | No |
> | hostpools/usersessions | No |
> | workspaces | Yes |

## Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | ElasticPools | Yes |
> | ElasticPools/IotHubTenants | Yes |
> | IotHubs | Yes |
> | IotHubs/eventGridFilters | No |
> | ProvisioningServices | Yes |
> | usages | No |

## Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | pipelines | Yes |

## Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | controllers | Yes |

## Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | labcenters | Yes |
> | labs | Yes |
> | labs/environments | Yes |
> | labs/serviceRunners | Yes |
> | labs/virtualMachines | Yes |
> | schedules | Yes |

## Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | databaseAccountNames | No |
> | databaseAccounts | Yes |

## Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | domains | Yes |
> | domains/domainOwnershipIdentifiers | No |
> | generateSsoRequest | No |
> | topLevelDomains | No |
> | validateDomainRegistrationInformation | No |

## Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | lcsprojects | No |
> | lcsprojects/clouddeployments | No |
> | lcsprojects/connectors | No |

## Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | services | Yes |

## Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | domains | Yes |
> | domains/topics | No |
> | eventSubscriptions | No |
> | extensionTopics | No |
> | topics | Yes |
> | topicTypes | No |

## Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | clusters | Yes |
> | namespaces | Yes |
> | namespaces/authorizationrules | No |
> | namespaces/disasterrecoveryconfigs | No |
> | namespaces/eventhubs | No |
> | namespaces/eventhubs/authorizationrules | No |
> | namespaces/eventhubs/consumergroups | No |
> | namespaces/networkrulesets | No |

## Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | features | No |
> | providers | No |

## Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | enroll | No |
> | galleryitems | No |
> | generateartifactaccessuri | No |
> | myareas | No |
> | myareas/areas | No |
> | myareas/areas/areas | No |
> | myareas/areas/areas/galleryitems | No |
> | myareas/areas/galleryitems | No |
> | myareas/galleryitems | No |
> | register | No |
> | resources | No |
> | retrieveresourcesbyid | No |

## Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | accounts | Yes |

## Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | autoManagedVmConfigurationProfiles | Yes |
> | configurationProfileAssignments | No |
> | guestConfigurationAssignments | No |
> | software | No |
> | softwareUpdateProfile | No |
> | softwareUpdates | No |

## Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | hanaInstances | Yes |
> | sapMonitors | Yes |

## Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | dedicatedHSMs | Yes |

## Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | clusters | Yes |
> | clusters/applications | No |

## Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | services | Yes |

## Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | machines | Yes |
> | machines/extensions | Yes |

## Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | dataManagers | Yes |

## Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | components | Yes |
> | networkScopes | Yes |

## Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | jobs | Yes |

## Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | appTemplates | No |
> | IoTApps | Yes |

## Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | Graph | Yes |

## Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | deletedVaults | No |
> | hsmPools | Yes |
> | vaults | Yes |
> | vaults/accessPolicies | No |
> | vaults/eventGridFilters | No |
> | vaults/secrets | No |

## Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | clusters | Yes |
> | clusters/attacheddatabaseconfigurations | No |
> | clusters/databases | No |
> | clusters/databases/dataconnections | No |
> | clusters/databases/eventhubconnections | No |
> | clusters/sharedidentities | No |

## Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | labaccounts | Yes |
> | users | No |

## Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | hostingEnvironments | Yes |
> | integrationAccounts | Yes |
> | integrationServiceEnvironments | Yes |
> | integrationServiceEnvironments/managedApis | Yes |
> | isolatedEnvironments | Yes |
> | workflows | Yes |

## Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | commitmentPlans | Yes |
> | webServices | Yes |
> | Workspaces | Yes |

## Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | workspaces | Yes |
> | workspaces/computes | No |
> | workspaces/eventGridFilters | No |

## Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | Identities | No |
> | userAssignedIdentities | Yes |

## Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | No |
> | registrationAssignments | No |
> | registrationDefinitions | No |

## Microsoft.Management

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | getEntities | No |
> | managementGroups | No |
> | resources | No |
> | startTenantBackfill | No |
> | tenantBackfillStatus | No |

## Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | accounts | Yes |
> | accounts/eventGridFilters | No |

## Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | offers | No |
> | offerTypes | No |
> | offerTypes/publishers | No |
> | offerTypes/publishers/offers | No |
> | offerTypes/publishers/offers/plans | No |
> | offerTypes/publishers/offers/plans/agreements | No |
> | offerTypes/publishers/offers/plans/configs | No |
> | offerTypes/publishers/offers/plans/configs/importImage | No |
> | privategalleryitems | No |
> | products | No |
> | publishers | No |
> | publishers/offers | No |
> | publishers/offers/amendments | No |

## Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | classicDevServices | Yes |
> | updateCommunicationPreference | No |

## Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | agreements | No |
> | offertypes | No |

## Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | mediaservices | Yes |
> | mediaservices/accountFilters | No |
> | mediaservices/assets | No |
> | mediaservices/assets/assetFilters | No |
> | mediaservices/contentKeyPolicies | No |
> | mediaservices/eventGridFilters | No |
> | mediaservices/liveEventOperations | No |
> | mediaservices/liveEvents | Yes |
> | mediaservices/liveEvents/liveOutputs | No |
> | mediaservices/liveOutputOperations | No |
> | mediaservices/mediaGraphs | No |
> | mediaservices/streamingEndpointOperations | No |
> | mediaservices/streamingEndpoints | Yes |
> | mediaservices/streamingLocators | No |
> | mediaservices/streamingPolicies | No |
> | mediaservices/transforms | No |
> | mediaservices/transforms/jobs | No |

## Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | appClusters | Yes |

## Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | assessmentProjects | Yes |
> | migrateprojects | Yes |
> | projects | Yes |

## Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Yes |
> | objectUnderstandingAccounts | Yes |
> | remoteRenderingAccounts | Yes |
> | spatialAnchorsAccounts | Yes |
> | surfaceReconstructionAccounts | Yes |

## Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | netAppAccounts | Yes |
> | netAppAccounts/backupPolicies | Yes |
> | netAppAccounts/capacityPools | Yes |
> | netAppAccounts/capacityPools/volumes | Yes |
> | netAppAccounts/capacityPools/volumes/backups | No |
> | netAppAccounts/capacityPools/volumes/mountTargets | Yes |
> | netAppAccounts/capacityPools/volumes/snapshots | Yes |
> | netAppAccounts/vaults | No |
## Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | applicationGateways | Yes |
> | applicationGatewayWebApplicationFirewallPolicies | Yes |
> | applicationSecurityGroups | Yes |
> | azureFirewallFqdnTags | No |
> | azureFirewalls | Yes |
> | bastionHosts | Yes |
> | bgpServiceCommunities | No |
> | connections | Yes |
> | ddosCustomPolicies | Yes |
> | ddosProtectionPlans | Yes |
> | dnsOperationStatuses | No |
> | dnszones | Yes |
> | dnszones/A | No |
> | dnszones/AAAA | No |
> | dnszones/all | No |
> | dnszones/CAA | No |
> | dnszones/CNAME | No |
> | dnszones/MX | No |
> | dnszones/NS | No |
> | dnszones/PTR | No |
> | dnszones/recordsets | No |
> | dnszones/SOA | No |
> | dnszones/SRV | No |
> | dnszones/TXT | No |
> | expressRouteCircuits | Yes |
> | expressRouteCrossConnections | Yes |
> | expressRouteGateways | Yes |
> | expressRoutePorts | Yes |
> | expressRouteServiceProviders | No |
> | firewallPolicies | Yes |
> | frontdoors | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | No |
> | frontdoorWebApplicationFirewallPolicies | Yes |
> | getDnsResourceReference | No |
> | internalNotify | No |
> | loadBalancers | Yes |
> | localNetworkGateways | Yes |
> | natGateways | Yes |
> | networkIntentPolicies | Yes |
> | networkInterfaces | Yes |
> | networkProfiles | Yes |
> | networkSecurityGroups | Yes |
> | networkWatchers | Yes |
> | networkWatchers/connectionMonitors | Yes |
> | networkWatchers/lenses | Yes |
> | networkWatchers/pingMeshes | Yes |
> | p2sVpnGateways | Yes |
> | privateDnsOperationStatuses | No |
> | privateDnsZones | Yes |
> | privateDnsZones/A | No |
> | privateDnsZones/AAAA | No |
> | privateDnsZones/all | No |
> | privateDnsZones/CNAME | No |
> | privateDnsZones/MX | No |
> | privateDnsZones/PTR | No |
> | privateDnsZones/SOA | No |
> | privateDnsZones/SRV | No |
> | privateDnsZones/TXT | No |
> | privateDnsZones/virtualNetworkLinks | Yes |
> | privateEndpoints | Yes |
> | privateLinkServices | Yes |
> | publicIPAddresses | Yes |
> | publicIPPrefixes | Yes |
> | routeFilters | Yes |
> | routeTables | Yes |
> | secureGateways | Yes |
> | serviceEndpointPolicies | Yes |
> | trafficManagerGeographicHierarchies | No |
> | trafficmanagerprofiles | Yes |
> | trafficmanagerprofiles/heatMaps | No |
> | trafficManagerUserMetricsKeys | No |
> | virtualHubs | Yes |
> | virtualNetworkGateways | Yes |
> | virtualNetworks | Yes |
> | virtualNetworkTaps | Yes |
> | virtualWans | Yes |
> | vpnGateways | Yes |
> | vpnSites | Yes |
> | webApplicationFirewallPolicies | Yes |

## Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | namespaces | Yes |
> | namespaces/notificationHubs | Yes |

## Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | osNamespaces | Yes |

## Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | HyperVSites | Yes |
> | ImportSites | Yes |
> | ServerSites | Yes |
> | VMwareSites | Yes |

## Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | clusters | Yes |
> | devices | No |
> | linkTargets | No |
> | storageInsightConfigs | No |
> | workspaces | Yes |
> | workspaces/dataSources | No |
> | workspaces/linkedServices | No |
> | workspaces/query | No |

## Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | managementassociations | No |
> | managementconfigurations | Yes |
> | solutions | Yes |
> | views | Yes |

## Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | legacyPeerings | No |
> | peerAsns | No |
> | peerings | Yes |
> | peeringServiceProviders | No |
> | peeringServices | Yes |

## Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | policyEvents | No |
> | policyMetadata | No |
> | policyStates | No |
> | policyTrackedResources | No |
> | remediations | No |

## Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | consoles | No |
> | dashboards | Yes |
> | userSettings | No |

## Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | workspaceCollections | Yes |

## Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | capacities | Yes |

## Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | backupProtectedItems | No |
> | vaults | Yes |

## Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | namespaces | Yes |
> | namespaces/authorizationrules | No |
> | namespaces/hybridconnections | No |
> | namespaces/hybridconnections/authorizationrules | No |
> | namespaces/wcfrelays | No |
> | namespaces/wcfrelays/authorizationrules | No |

## Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | accounts | No |
> | collections | Yes |
> | collections/applications | No |
> | collections/securityprincipals | No |
> | templateImages | No |

## Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | queries | Yes |
> | resourceChangeDetails | No |
> | resourceChanges | No |
> | resources | No |
> | resourcesHistory | No |
> | subscriptionsStatus | No |

## Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | availabilityStatuses | No |
> | childAvailabilityStatuses | No |
> | childResources | No |
> | events | No |
> | impactedResources | No |
> | metadata | No |
> | notifications | No |

## Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | deployments | No |
> | deployments/operations | No |
> | deploymentScripts | Yes |
> | deploymentScripts/logs | No |
> | links | No |
> | notifyResourceJobs | No |
> | providers | No |
> | resourceGroups | No |
> | resources | No |
> | subscriptions | No |
> | subscriptions/providers | No |
> | subscriptions/resourceGroups | No |
> | subscriptions/resourcegroups/resources | No |
> | subscriptions/resources | No |
> | subscriptions/tagnames | No |
> | subscriptions/tagNames/tagValues | No |
> | tenants | No |

## Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | applications | Yes |
> | saasresources | No |

## Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | jobcollections | Yes |

## Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | resourceHealthMetadata | No |
> | searchServices | Yes |

## Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | No |
> | advancedThreatProtectionSettings | No |
> | alerts | No |
> | allowedConnections | No |
> | applicationWhitelistings | No |
> | assessmentMetadata | No |
> | assessments | No |
> | automations | Yes |
> | AutoProvisioningSettings | No |
> | Compliances | No |
> | dataCollectionAgents | No |
> | deviceSecurityGroups | No |
> | discoveredSecuritySolutions | No |
> | externalSecuritySolutions | No |
> | InformationProtectionPolicies | No |
> | iotSecuritySolutions | Yes |
> | iotSecuritySolutions/analyticsModels | No |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | No |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | No |
> | jitNetworkAccessPolicies | No |
> | networkData | No |
> | playbookConfigurations | Yes |
> | policies | No |
> | pricings | No |
> | regulatoryComplianceStandards | No |
> | regulatoryComplianceStandards/regulatoryComplianceControls | No |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | No |
> | securityContacts | No |
> | securitySolutions | No |
> | securitySolutionsReferenceData | No |
> | securityStatuses | No |
> | securityStatusesSummaries | No |
> | serverVulnerabilityAssessments | No |
> | settings | No |
> | subAssessments | No |
> | tasks | No |
> | topologies | No |
> | workspaceSettings | No |

## Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | aggregations | No |
> | alertRules | No |
> | alertRuleTemplates | No |
> | bookmarks | No |
> | cases | No |
> | dataConnectors | No |
> | entities | No |
> | entityQueries | No |
> | officeConsents | No |
> | settings | No |

## Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | namespaces | Yes |
> | namespaces/authorizationrules | No |
> | namespaces/disasterrecoveryconfigs | No |
> | namespaces/eventgridfilters | No |
> | namespaces/networkrulesets | No |
> | namespaces/queues | No |
> | namespaces/queues/authorizationrules | No |
> | namespaces/topics | No |
> | namespaces/topics/authorizationrules | No |
> | namespaces/topics/subscriptions | No |
> | namespaces/topics/subscriptions/rules | No |
> | premiumMessagingRegions | No |

## Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | applications | Yes |
> | clusters | Yes |
> | clusters/applications | No |
> | containerGroups | Yes |
> | containerGroupSets | Yes |
> | edgeclusters | Yes |
> | edgeclusters/applications | No |
> | networks | Yes |
> | secretstores | Yes |
> | secretstores/certificates | No |
> | secretstores/secrets | No |
> | volumes | Yes |

## Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | applications | Yes |
> | containerGroups | Yes |
> | gateways | Yes |
> | networks | Yes |
> | secrets | Yes |
> | volumes | Yes |

## Microsoft.Services

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | providerRegistrations | No |
> | providerRegistrations/resourceTypeRegistrations | No |
> | rollouts | Yes |

## Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | SignalR | Yes |
> | SignalR/eventGridFilters | No |

## Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | SiteRecoveryVault | Yes |

## Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | hybridUseBenefits | No |

## Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | applicationDefinitions | Yes |
> | applications | Yes |
> | jitRequests | Yes |

## Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | managedInstances | Yes |
> | managedInstances/databases | Yes |
> | managedInstances/databases/backupShortTermRetentionPolicies | No |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | No |
> | managedInstances/databases/vulnerabilityAssessments | No |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | No |
> | managedInstances/encryptionProtector | No |
> | managedInstances/keys | No |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | No |
> | managedInstances/vulnerabilityAssessments | No |
> | servers | Yes |
> | servers/administrators | No |
> | servers/communicationLinks | No |
> | servers/databases | Yes |
> | servers/encryptionProtector | No |
> | servers/firewallRules | No |
> | servers/keys | No |
> | servers/restorableDroppedDatabases | No |
> | servers/serviceobjectives | No |
> | servers/tdeCertificates | No |
> | virtualClusters | No |

## Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Yes |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | No |
> | SqlVirtualMachines | Yes |

## Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | storageAccounts | Yes |
> | storageAccounts/blobServices | No |
> | storageAccounts/fileServices | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/services | No |
> | storageAccounts/services/metricDefinitions | No |
> | storageAccounts/tableServices | No |
> | usages | No |

## Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | caches | Yes |
> | caches/storageTargets | No |
> | usageModels | No |

## Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | replicationGroups | No |

## Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices/syncGroups | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |
> | storageSyncServices/syncGroups/serverEndpoints | No |
> | storageSyncServices/workflows | No |

## Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices/syncGroups | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |
> | storageSyncServices/syncGroups/serverEndpoints | No |
> | storageSyncServices/workflows | No |

## Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices/syncGroups | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |
> | storageSyncServices/syncGroups/serverEndpoints | No |
> | storageSyncServices/workflows | No |

## Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | managers | Yes |

## Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | streamingjobs | Yes |

## Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | cancel | No |
> | CreateSubscription | No |
> | enable | No |
> | rename | No |
> | SubscriptionDefinitions | No |
> | SubscriptionOperations | No |

## Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | environments | Yes |
> | environments/accessPolicies | No |
> | environments/eventsources | Yes |
> | environments/referenceDataSets | Yes |

## Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Yes |
> | dedicatedCloudServices | Yes |
> | virtualMachines | Yes |

## Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | apiManagementAccounts | No |
> | apiManagementAccounts/apiAcls | No |
> | apiManagementAccounts/apis | No |
> | apiManagementAccounts/apis/apiAcls | No |
> | apiManagementAccounts/apis/connectionAcls | No |
> | apiManagementAccounts/apis/connections | No |
> | apiManagementAccounts/apis/connections/connectionAcls | No |
> | apiManagementAccounts/apis/localizedDefinitions | No |
> | apiManagementAccounts/connectionAcls | No |
> | apiManagementAccounts/connections | No |
> | billingMeters | No |
> | certificates | Yes |
> | connectionGateways | Yes |
> | connections | Yes |
> | customApis | Yes |
> | deletedSites | No |
> | functions | No |
> | hostingEnvironments | Yes |
> | hostingEnvironments/multiRolePools | No |
> | hostingEnvironments/workerPools | No |
> | publishingUsers | No |
> | recommendations | No |
> | resourceHealthMetadata | No |
> | runtimes | No |
> | serverFarms | Yes |
> | serverFarms/eventGridFilters | No |
> | sites | Yes |
> | sites/config  | No |
> | sites/eventGridFilters | No |
> | sites/hostNameBindings | No |
> | sites/networkConfig | No |
> | sites/premieraddons | Yes |
> | sites/slots | Yes |
> | sites/slots/eventGridFilters | No |
> | sites/slots/hostNameBindings | No |
> | sites/slots/networkConfig | No |
> | sourceControls | No |
> | validate | No |
> | verifyHostingEnvironmentVnet | No |

## Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | DeviceServices | Yes |

## Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resource type | Complete mode deletion |
> | ------------- | ----------- |
> | components | No |
> | componentsSummary | No |
> | monitorInstances | No |
> | monitorInstancesSummary | No |
> | monitors | No |
> | notificationSettings | No |

## Next steps

To get the same data as a file of comma-separated values, download [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
