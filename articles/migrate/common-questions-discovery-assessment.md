---
title: Questions about discovery, assessment, and dependency analysis in Azure Migrate
description: Get answers to common questions about discovery, assessment, and dependency analysis in Azure Migrate.
ms.topic: conceptual
ms.date: 06/09/2020

---

# Discovery, assessment, and dependency analysis - Common questions

This article answers common questions about discovery, assessment, and dependency analysis in Azure Migrate. If you have other questions, check these resources:

- [General questions](resources-faq.md) about Azure Migrate
- Questions about the [Azure Migrate appliance](common-questions-appliance.md)
- Questions about [server migration](common-questions-server-migration.md)
- Get questions answered in the [Azure Migrate forum](https://aka.ms/AzureMigrateForum)


## What geographies are supported for discovery and assessment with Azure Migrate?

Review the supported geographies for [public](migrate-support-matrix.md#supported-geographies-public-cloud) and [government clouds](migrate-support-matrix.md#supported-geographies-azure-government).


## How many VMs can I discover with an appliance?

You can discover up to 10,000 VMware VMs, up to 5,000 Hyper-V VMs, and up to 1000 physical servers by using a single appliance. If you have more machines, read about [scaling a Hyper-V assessment](scale-hyper-v-assessment.md), [scaling a VMware assessment](scale-vmware-assessment.md), or [scaling a physical server assessment](scale-physical-assessment.md).

## How do I choose the assessment type?

- Use **Azure VM assessments** when you want to assess your on-premises [VMware VMs](how-to-set-up-appliance-vmware.md), [Hyper-V VMs](how-to-set-up-appliance-hyper-v.md), and [physical servers](how-to-set-up-appliance-physical.md) for migration to Azure VMs. [Learn More](concepts-assessment-calculation.md)

- Use **Azure VMware Solution (AVS)** assessments when you want to assess your on-premises [VMware VMs](how-to-set-up-appliance-vmware.md) for migration to [Azure VMware Solution (AVS)](../azure-vmware/introduction.md) using this assessment type. [Learn more](concepts-azure-vmware-solution-assessment-calculation.md)

- You can use a common group with VMware machines only to run both types of assessments. Note that if you are running AVS assessments in Azure Migrate for the first time, it is advisable to create a new group of VMware machines.
 

## Why is performance data missing for some/all VMs in my assessment report?

For "Performance-based" assessment, the assessment report export says 'PercentageOfCoresUtilizedMissing' or 'PercentageOfMemoryUtilizedMissing' when the Azure Migrate appliance cannot collect performance data for the on-premises VMs. Please check:

- If the VMs are powered on for the duration for which you are creating the assessment
- If only memory counters are missing and you are trying to assess Hyper-V VMs, check if you have dynamic memory enabled on these VMs. There is a known issue currently due to which Azure Migrate appliance cannot collect memory utilization for such VMs.
- If all of the performance counters are missing, ensure that outbound connections on ports 443 (HTTPS) is allowed.

Note- If any of the performance counters are missing, Azure Migrate: Server Assessment falls back to the allocated cores/memory on-premises and recommends a VM size accordingly.

## Why is the confidence rating of my assessment low?

The confidence rating is calculated for "Performance-based" assessments based on the percentage of [available data points](./concepts-assessment-calculation.md#ratings) needed to compute the assessment. Below are the reasons why an assessment could get a low confidence rating:

- You did not profile your environment for the duration for which you are creating the assessment. For example, if you are creating an assessment with performance duration set to one week, you need to wait for at least a week after you start the discovery for all the data points to get collected. If you cannot wait for the duration, please change the performance duration to a smaller period and 'Recalculate' the assessment.
 
- Server Assessment is not be able to collect the performance data for some or all the VMs in the assessment period. Please check if the VMs were powered on for the duration of the assessment, outbound connections on ports 443 are allowed. For Hyper-V VMs, if dynamic memory is enabled, memory counters will be missing leading to a low confidence rating. Please 'Recalculate' the assessment to reflect the latest changes in confidence rating. 

- Few VMs were created after discovery in Server Assessment had started. For example, if you are creating an assessment for the performance history of last one month, but few VMs were created in the environment only a week ago. In this case, the performance data for the new VMs will not be available for the entire duration and the confidence rating would be low.

[Learn more](./concepts-assessment-calculation.md#confidence-ratings-performance-based) about confidence rating.

## I can't see some groups when I am creating an Azure VMware Solution (AVS) assessment

- AVS assessment can be done on groups that have only VMware machines. Please remove any non-VMware machine from the group if you intend to perform an AVS assessment.
- If you are running AVS assessments in Azure Migrate for the first time, it is advisable to create a new group of VMware machines.

## I can't see some VM types in Azure Government

VM types supported for assessment and migration depend on availability in Azure Government location. You can [review and compare](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) VM types in Azure Government.

## The size of my VM changed. Can I run an assessment again?

The Azure Migrate appliance continuously collects information about the on-premises environment.  An assessment is a point-in-time snapshot of on-premises VMs. If you change the settings on a VM that you want to assess, use the recalculate option to update the assessment with the latest changes.

## How do I discover VMs in a multitenant environment?

- **VMware**: If an environment is shared across tenants and you don't want to discover a tenant's VMs in another tenant's subscription, create VMware vCenter Server credentials that can access only the VMs you want to discover. Then, use those credentials when you start discovery in the Azure Migrate appliance.
- **Hyper-V**: Discovery uses Hyper-V host credentials. If VMs share the same Hyper-V host, there's currently no way to separate the discovery.  

## Do I need vCenter Server?

Yes, Azure Migrate requires vCenter Server in a VMware environment to perform discovery. Azure Migrate doesn't support discovery of ESXi hosts that aren't managed by vCenter Server.

## What are the sizing options in an Azure VM assessment?

With as-on-premises sizing, Azure Migrate doesn't consider VM performance data for assessment. Azure Migrate assesses VM sizes based on the on-premises configuration. With performance-based sizing, sizing is based on utilization data.

For example, if an on-premises VM has four cores and 8 GB of memory at 50% CPU utilization and 50% memory utilization:
- As-on-premises sizing will recommend an Azure VM SKU that has four cores and 8 GB of memory.
- Performance-based sizing will recommend a VM SKU that has two cores and 4 GB of memory because the utilization percentage is considered.

Similarly, disk sizing depends on sizing criteria and storage type:
- If the sizing criteria is performance-based and the storage type is automatic, Azure Migrate takes the IOPS and throughput values of the disk into account when it identifies the target disk type (Standard or Premium).
- If the sizing criteria is performance-based and the storage type is Premium, Azure Migrate recommends a Premium disk SKU based on the size of the on-premises disk. The same logic is applied to disk sizing when the sizing is as-on-premises and the storage type is Standard or Premium.

## Does performance history and utilization affect sizing in an Azure VM assessment?

Yes, performance history and utilization affect sizing in an Azure VM assessment.

### Performance history

For performance-based sizing only, Azure Migrate collects the performance history of on-premises machines, and then uses it to recommend the VM size and disk type in Azure:

1. The appliance continuously profiles the on-premises environment to gather real-time utilization data every 20 seconds.
2. The appliance rolls up the collected 20-second samples and uses them to create a single data point every 15 minutes.
3. To create the data point, the appliance selects the peak value from all 20-second samples.
4. The appliance sends the data point to Azure.

### Utilization

When you create an assessment in Azure, depending on performance duration and the performance history percentile value that is set, Azure Migrate calculates the effective utilization value, and then uses it for sizing.

For example, if you set the performance duration to one day and the percentile value to 95th percentile, Azure Migrate sorts the 15-minute sample points sent by the collector for the past day in ascending order. It picks the 95th percentile value as the effective utilization.

Using the 95th percentile value ensures that outliers are ignored. Outliers might be included if your Azure Migrate uses the 99th percentile. To pick the peak usage for the period without missing any outliers, set Azure Migrate to use the 99th percentile.


## How are import-based assessments different from assessments with discovery source as appliance?

Import-based Azure VM assessments are assessments created with machines that are imported into Azure Migrate using a CSV file. Only four fields are mandatory to import: Server name, cores, memory, and operating system. Here are some things to note: 
 - The readiness criteria is less stringent in import-based assessments on the boot type parameter. If the boot type isn't provided, it is assumed the machine has BIOS boot type and the machine is not marked as **Conditionally Ready**. In assessments with discovery source as appliance, the readiness is marked as **Conditionally Ready** if the boot type is missing. This difference in readiness calculation is because users may not have all information on the machines in the early stages of migration planning when import-based assessments are done. 
 - Performance-based import assessments use the utilization value provided by the user for right-sizing calculations. Since the utilization value is provided by the user, the **Performance history** and **Percentile utilization** options are disabled in the assessment properties. In assessments with discovery source as appliance, the chosen percentile value is picked from the performance data collected by the appliance.

## Why is the suggested migration tool in import-based AVS assessment marked as unknown?

For machines imported via a CSV file, the default migration tool in an AVS assessment is unknown. Though, for VMware machines, it is recommended to use the VMware Hybrid Cloud Extension (HCX) solution. [Learn More](../azure-vmware/tutorial-deploy-vmware-hcx.md).


## What is dependency visualization?

Dependency visualization can help you assess groups of VMs to migrate with greater confidence. Dependency visualization cross-checks machine dependencies before you run an assessment. It helps ensure that nothing is left behind, and it helps avoid unexpected outages when you migrate to Azure. Azure Migrate uses the Service Map solution in Azure Monitor to enable dependency visualization. [Learn more](concepts-dependency-visualization.md).

> [!NOTE]
> Agent-based dependency analysis isn't available in Azure Government. You can  use agentless dependency analysis

## What's the difference between agent-based and agentless?

The differences between agentless visualization and agent-based visualization are summarized in the table.

**Requirement** | **Agentless** | **Agent-based**
--- | --- | ---
Support | This option is currently in preview, and is only available for VMware VMs. [Review](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) supported operating systems. | In general availability (GA).
Agent | No need to install agents on machines you want to cross-check. | Agents to be installed on each on-premises machine that you want to analyze: The [Microsoft Monitoring agent (MMA)](../azure-monitor/platform/agent-windows.md), and the [Dependency agent](../azure-monitor/platform/agents-overview.md#dependency-agent). 
Prerequisites | [Review](concepts-dependency-visualization.md#agentless-analysis) the prerequisites and deployment requirements. | [Review](concepts-dependency-visualization.md#agent-based-analysis) the prerequisites and deployment requirements.
Log Analytics | Not required. | Azure Migrate uses the [Service Map](../azure-monitor/insights/service-map.md) solution in [Azure Monitor logs](../azure-monitor/log-query/log-query-overview.md) for dependency visualization. [Learn more](concepts-dependency-visualization.md#agent-based-analysis).
How it works | Captures TCP connection data on machines enabled for dependency visualization. After discovery, it gathers data at intervals of five minutes. | Service Map agents installed on a machine gather data about TCP processes and inbound/outbound connections for each process.
Data | Source machine server name, process, application name.<br/><br/> Destination machine server name, process, application name, and port. | Source machine server name, process, application name.<br/><br/> Destination machine server name, process, application name, and port.<br/><br/> Number of connections, latency, and data transfer information are gathered and available for Log Analytics queries. 
Visualization | Dependency map of single server can be viewed over a duration of one hour to 30 days. | Dependency map of a single server.<br/><br/> Map can be viewed over an hour only.<br/><br/> Dependency map of a group of servers.<br/><br/> Add and remove servers in a group from the map view.
Data export | Last 30 days data can be downloaded in a CSV format. | Data can be queried with Log Analytics.


## Do I need to deploy the appliance for agentless dependency analysis?

Yes, the [Azure Migrate appliance](migrate-appliance.md) must be deployed.

## Do I pay for dependency visualization?

No. Learn more about [Azure Migrate pricing](https://azure.microsoft.com/pricing/details/azure-migrate/).

## What do I install for agent-based dependency visualization?

To use agent-based dependency visualization, download and install agents on each on-premises machine that you want to evaluate:

- [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md)
- [Dependency agent](../azure-monitor/platform/agents-overview.md#dependency-agent)
- If you have machines that don't have internet connectivity, download and install the Log Analytics gateway on them.

You need these agents only if you use agent-based dependency visualization.

## Can I use an existing workspace?

Yes, for agent-based dependency visualization you can attach an existing workspace to the migration project and use it for dependency visualization. 

## Can I export the dependency visualization report?

No, the dependency visualization report in agent-based visualization can't be exported. However, Azure Migrate uses Service Map, and you can use the [Service Map REST API](/rest/api/servicemap/machines/listconnections) to retrieve the dependencies in JSON format.

## Can I automate agent installation?

For agent-based dependency visualization:

- Use a [script to install the Dependency agent](../azure-monitor/insights/vminsights-enable-hybrid.md#dependency-agent).
- For MMA, [use the command line or automation](../azure-monitor/platform/log-analytics-agent.md#installation-options), or use a [script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- In addition to scripts, you can use deployment tools like Microsoft Endpoint Configuration Manager and [Intigua](https://www.intigua.com/intigua-for-azure-migration) to deploy the agents.

## What operating systems does MMA support?

- View the list of [Windows operating systems that MMA supports](../azure-monitor/platform/log-analytics-agent.md#installation-options).
- View the list of [Linux operating systems that MMA supports](../azure-monitor/platform/log-analytics-agent.md#installation-options).

## Can I visualize dependencies for more than one hour?

For agent-based visualization, you can visualize dependencies for up to one hour. You can go back as far as one month to a specific date in history, but the maximum duration for visualization is one hour. For example, you can use the time duration in the dependency map to view dependencies for yesterday, but you can view dependencies only for a one-hour window. However, you can use Azure Monitor logs to [query dependency data](./how-to-create-group-machine-dependencies.md) for a longer duration.

For agentless visualization, you can view the dependency map of a single server from a duration of between one hour and 30 days.

## Can I visualize dependencies for groups of more than 10 VMs?

You can [visualize dependencies](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) for groups that have up to 10 VMs. If you have a group that has more than 10 VMs, we recommend that you split the group into smaller groups, and then visualize the dependencies.

## Next steps

Read the [Azure Migrate overview](migrate-services-overview.md).