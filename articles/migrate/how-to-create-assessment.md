---
title: Create an Azure VM assessment with Azure Migrate Discovery and assessment tool | Microsoft Docs
description: Describes how to create an Azure VM assessment with the Azure Migrate Discovery and assessment tool
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019

---



# Create an Azure VM assessment

This article describes how to create an Azure VM assessment for on-premises servers in you VMware, Hyper-V or physical/other cloud environment with Azure Migrate: Discovery and assessment.

[Azure Migrate](migrate-services-overview.md) helps you to migrate to Azure. Azure Migrate provides a centralized hub to track discovery, assessment, and migration of on-premises infrastructure, applications, and data to Azure. The hub provides Azure tools for assessment and migration, as well as third-party independent software vendor (ISV) offerings. 

## Before you start

- Make sure you've [created](./create-manage-projects.md) an Azure Migrate project.
- If you've already created a project, make sure you've [added](how-to-assess.md) the Azure Migrate: Discovery and assessment tool.
- To create an assessment, you need to set up an Azure Migrate appliance for [VMware](how-to-set-up-appliance-vmware.md) or [Hyper-V](how-to-set-up-appliance-hyper-v.md). The appliance discovers on-premises servers, and sends metadata and performance data to Azure Migrate: Discovery and assessment. [Learn more](migrate-appliance.md).


## Azure VM Assessment overview
There are two types of sizing criteria you can use to create an Azure VM assessment using Azure Migrate: Discovery and assessment.

**Assessment** | **Details** | **Data**
--- | --- | ---
**Performance-based** | Assessments based on collected performance data | **Recommended VM size**: Based on CPU and memory utilization data.<br/><br/> **Recommended disk type (standard or premium managed disk)**: Based on the IOPS and throughput of the on-premises disks.
**As on-premises** | Assessments based on on-premises sizing. | **Recommended VM size**: Based on the on-premises VM size<br/><br> **Recommended disk type**: Based on the storage type setting you select for the assessment.

[Learn more](concepts-assessment-calculation.md) about assessments.

## Run an assessment

Run an assessment as follows:

1. On the **Overview** page > **Windows, Linux and SQL Server**, click **Assess and migrate servers**.

   ![Location of Assess and migrate servers button](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. In **Azure Migrate: Discovery and assessment**, click **Assess** and select **Azure VM**

    ![Location of the Assess button](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. In **Assess servers** > **Assessment type**
4. In **Discovery source**:

    - If you discovered servers using the appliance, select **Servers discovered from Azure Migrate appliance**.
    - If you discovered servers using an imported CSV file, select **Imported servers**. 
    
1. Click **Edit** to review the assessment properties.

    ![Location of the View all button to review assessment properties](./media/tutorial-assess-vmware-azure-vm/assessment-name.png)

1. In **Assessment properties** > **Target Properties**:
    - In **Target location**, specify the Azure region to which you want to migrate.
        - Size and cost recommendations are based on the location that you specify. Once you change the target location from default, you will be prompted to specify **Reserved Instances** and **VM series**.
        - In Azure Government, you can target assessments in [these regions](migrate-support-matrix.md#supported-geographies-azure-government)
    - In **Storage type**,
        - If you want to use performance-based data in the assessment, select **Automatic** for Azure Migrate to recommend a storage type, based on disk IOPS and throughput.
        - Alternatively, select the storage type you want to use for VM when you migrate it.
    - In **Reserved Instances**, specify whether you want to use reserve instances for the VM when you migrate it.
        - If you select to use a reserved instance, you can't specify  '**Discount (%)**, or **VM uptime**. 
        - [Learn more](https://aka.ms/azurereservedinstances).
 1. In **VM Size**:
     - In **Sizing criterion**, select if you want to base the assessment on server configuration data/metadata, or on performance-based data. If you use performance data:
        - In **Performance history**, indicate the data duration on which you want to base the assessment
        - In **Percentile utilization**, specify the percentile value you want to use for the performance sample. 
    - In **VM Series**, specify the Azure VM series you want to consider.
        - If you're using performance-based assessment, Azure Migrate suggests a value for you.
        - Tweak settings as needed. For example, if you don't have a production environment that needs A-series VMs in Azure, you can exclude A-series from the list of series.
    - In **Comfort factor**, indicate the buffer you want to use during assessment. This accounts for issues like seasonal usage, short performance history, and likely increases in future usage. For example, if you use a comfort factor of two:
    
        **Component** | **Effective utilization** | **Add comfort factor (2.0)**
        --- | --- | ---
        Cores | 2  | 4
        Memory | 8 GB | 16 GB
   
1. In **Pricing**:
    - In **Offer**, specify the [Azure offer](https://azure.microsoft.com/support/legal/offer-details/) if you're enrolled. The assessment estimates the cost for that offer.
    - In **Currency**, select the billing currency for your account.
    - In **Discount (%)**, add any subscription-specific discounts you receive on top of the Azure offer. The default setting is 0%.
    - In **VM Uptime**, specify the duration (days per month/hour per day) that VMs will run.
        - This is useful for Azure VMs that won't run continuously.
        - Cost estimates are based on the duration specified.
        - Default is 31 days per month/24 hours per day.
    - In **EA Subscription**, specify whether to take an Enterprise Agreement (EA) subscription discount into account for cost estimation. 
    - In **Azure Hybrid Benefit**, specify whether you already have a Windows Server license. If you do and they're covered with active Software Assurance of Windows Server Subscriptions, you can apply for the [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/) when you bring licenses to Azure.

1. Click **Save** if you make changes.

    ![Assessment properties](./media/tutorial-assess-vmware-azure-vm/assessment-properties.png)

1. In **Assess Servers** > click **Next**.

1. In **Select servers to assess** > **Assessment name** > specify a name for the assessment. 

1. In **Select or create a group** > select **Create New** and specify a group name. 
    
     ![Add VMs to a group](./media/tutorial-assess-vmware-azure-vm/assess-group.png)


1. Select the appliance, and select the VMs you want to add to the group. Then click **Next**.


1. In **Review + create assessment**, review the assessment details, and click **Create Assessment** to create the group and run the assessment.

1. After the assessment is created, view it in **Servers** > **Azure Migrate: Discovery and assessment** > **Assessments**.

1. Click **Export assessment**, to download it as an Excel file.
    > [!NOTE]
    > For performance-based assessments, we recommend that you wait at least a day after starting discovery before you create an assessment. This provides time to collect performance data with higher confidence. Ideally, after you start discovery, wait for the performance duration you specify (day/week/month) for a high-confidence rating.

## Review an Azure VM assessment

An Azure VM assessment describes:

- **Azure readiness**: Whether servers are suitable for migration to Azure.
- **Monthly cost estimation**: The estimated monthly compute and storage costs for running the VMs in Azure.
- **Monthly storage cost estimation**: Estimated costs for disk storage after migration.

### View an Azure VM assessment

1. In **Windows, Linux and SQL Server** > **Azure Migrate: Discovery and assessment**, click the number next to **Azure VM assessment**.
2. In **Assessments**, select an assessment to open it. As an example (estimations and costs for example only): 

    ![Assessment summary](./media/how-to-create-assessment/assessment-summary.png)

### Review Azure readiness

1. In **Azure readiness**, verify whether servers are ready for migration to Azure.
2. Review the server status:
    - **Ready for Azure**: Azure Migrate recommends a VM size and cost estimates for VMs in the assessment.
    - **Ready with conditions**: Shows issues and suggested remediation.
    - **Not ready for Azure**: Shows issues and suggested remediation.
    - **Readiness unknown**: Used when Azure Migrate can't assess readiness, due to data availability issues.

3. Click on an **Azure readiness** status. You can view server readiness details, and drill down to see server details, including compute, storage, and network settings.



### Review cost details

This view shows the estimated compute and storage cost of running VMs in Azure.

1. Review the monthly compute and storage costs. Costs are aggregated for all servers in the assessed group.

    - Cost estimates are based on the size recommendations for a server, and its disks and properties.
    - Estimated monthly costs for compute and storage are shown.
    - The cost estimation is for running the on-premises servers as IaaS VMs. Azure VM assessment doesn't consider PaaS or SaaS costs.

2. You can review monthly storage cost estimates. This view shows aggregated storage costs for the assessed group, split over different types of storage disks.
3. You can drill down to see details for specific servers.


### Review confidence rating

When you run performance-based assessments, a confidence rating is assigned to the assessment.

![Confidence rating](./media/how-to-create-assessment/confidence-rating.png)

- A rating from 1-star (lowest) to 5-star (highest) is awarded.
- The confidence rating helps you estimate the reliability of the size recommendations provided by the assessment.
- The confidence rating is based on the availability of data points needed to compute the assessment.

Confidence ratings for an assessment are as follows.

**Data point availability** | **Confidence rating**
--- | ---
0%-20% | 1 Star
21%-40% | 2 Star
41%-60% | 3 Star
61%-80% | 4 Star
81%-100% | 5 Star




## Next steps

- Learn how to use [dependency mapping](how-to-create-group-machine-dependencies.md) to create high confidence groups.
- [Learn more](concepts-assessment-calculation.md) about how assessments are calculated.