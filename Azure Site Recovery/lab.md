# Lab: Implement Azure Site Recovery between Azure regions

All tasks in this lab are performed from the Azure portal

Lab files:

 -  **deploy.json**

 -  **deploy.parameters.json**


### Objectives

After completing this lab, you will be able to implement Azure Site Recovery Vault, configure replication of Azure VMs between Azure regions by using Azure Site Recovery.


### Exercise 1: Implement prerequisites for migration of Azure VMs by using Azure Site Recovery

The main tasks for this exercise are as follows:

 1. Deploy an Azure VM to be migrated by using an Azure Resource Manager template

 1. Create an Azure Recovery Services vault


#### Task 1: Deploy an Azure VM to be migrated by using an Azure Resource Manager template

1. Go to the Azure portal at [**http://portal.azure.com**](http://portal.azure.com) and sign in by using an account you were provided.

1. In the Azure portal, navigate to the **New** blade.

1. From the **New** blade, search Azure Marketplace for **Template deployment**.

1. Use the list of search results to navigate to the **Deploy a custom template** blade.

1. On the **Custom deployment** blade, click the **Build your own template in the editor** link. If you do not see this link, click **Edit template** instead.

2. From the **Edit template** blade, load the template file **deploy.json**.

    > **Note**: Review the content of the template and note that it defines deployment of an Azure VM hosting Windows Server 2016 Datacenter.

3. Save the template and return to the **Custom deployment** blade.

4. From the **Custom deployment** blade, navigate to the **Edit parameters** blade.

5. From the **Edit parameters** blade, load the parameters file **deploy.parameters.json**.

6. Save the parameters and return to the **Custom deployment** blade.

7. From the **Custom deployment** blade, initiate a template deployment with the following settings:

    - Subscription: **YOUR SUBSCRIPTION**

    - Resource group: the name of a new resource group **YOUR RESOURCE GROUP**

    - Location: **West Europe**

    - Vm Name: **azLab-vm-[YOUR STUDENT NUMBER]**

    - Admin Username: **Student**

    - Admin Password: **Pa55w.rd1234**

    - Image Publisher: **MicrosoftWindowsServer**

    - Image Offer: **WindowsServer**

    - Image SKU: **2016-Datacenter-Server-Core-smalldisk**

    - Vm Size: **Standard_DS1_v2**

   > **Note**: Do not wait for the deployment to complete but proceed to the next task. You will use this virtual machine in the second exercise of this lab.


#### Task 2: Implement an Azure Site Recovery vault

1. In the Azure portal, navigate to the **New** blade.

1. From the **New** blade, search Azure Marketplace for **Backup and Site Recovery**.

1. Use the list of search results to navigate to the **Recovery Services vault** blade.

1. Use the **Recovery Services vault** blade, to create a Site Recovery vault with the following settings:

    - Subscription: the same Azure subscription you used in the previous task of this exercise

    - Resource group: the name of a new resource group **YOUR RESOURCE GROUP**

    - Vault name: **azvaultchm[YOUR STUDENT NUMBER]**

    - Region: **North Europe**

    > **Note**: Wait for the provisioning to complete. This should take about a minute.

1. In the Azure portal, navigate to the blade of the newly provisioned Azure Recovery Services vault **azvaultchm[YOUR STUDENT NUMBER]**.

1. From the **azvaultchm[YOUR STUDENT NUMBER]** blade, navigate to its **Properties** blade and then to the **Security Settings** blade.

1. On the **Security Settings** blade, disable **Soft Delete** and save the change.

> **Result**: After you completed this exercise, you have initiated deployment of an Azure VM by using an Azure Resource Manager template and created an Azure Site Recovery vault that will be used to replicate content of the Azure VM disk files.



### Exercise 2: Migrate an Azure VM between Azure regions by using Azure Site Recovery

The main tasks for this exercise are as follows:

 1. Configure Azure VM replication

 1. Review Azure VM replication settings


#### Task 1: Configure Azure VM replication

   > **Note**: Before you start this task, ensure that the template deployment you started in the first exercise has completed.

1. In the Azure portal, navigate to the **Overview** blade of the newly provisioned Azure Recovery Services vault **azvaultchm[YOUR STUDENT NUMBER]**.

1. From the **azvaultchm[YOUR STUDENT NUMBER]** blade, click **+ Enable Site Recovery** 
 
1. Under **Azure virtual machines​** click **Enable replication** and configure the following replication settings:

    - Source location: **West Europe** - the same Azure region into which you deployed the Azure VM in the previous exercise of this lab 

    - Azure virtual machine deployment model: **Resource Manager**

    - Source subscription: the same Azure subscription you used in the previous exercise of this lab

    - Source resource group: **YOUR RESOURCE GROUP**

    - Disaster recovery between Availability Zones: **No**

1. Press **Next** and select virtual machine **zLab-vm-[YOUR STUDENT NUMBER]** created in previous task. Press **Next** at the bottom.

1. Verify replication setting on the next tab:

    - Target location: the name of an **Azure region** that is available in your subscription and which is **different from the region you deployed an Azure VM** in the previous task. If possible, use the same Azure region into which you deployed the Azure Site Recovery vault.

    - Target resource group: **(new) [YOUR RESOURCE GROUP]-asr**

    - Target virtual network: **(new) azLab-vm-vnet-asr**

    - Cache storage account: accept the default setting

    - Replica managed disks: **(new) 1 premium disk(s), 0 standard disk(s)**

    - Target availability sets: **Not Applicable**

    - Replication policy: the name of a new replication policy **12-hour-retention-policy**

    - Recovery point retention: **12 Hours**

    - App consistent snapshot frequency: **6 Hours**

    - Multi-VM consistency: **No**

2. From the **Configure settings** blade, initiate creation of target resources and wait until you are redirected to the **Enable replication** blade.

3. From the **Enable replication** blade, enable the replication and wait for the changes to be applied.

    > **Note:** It may take up to few minutes before replication will be enabled.


#### Task 2: Review Azure VM replication settings

1. In the Azure portal, navigate to the **azvaultchm[YOUR STUDENT NUMBER] - Replicated items** blade.

1. On the **azvaultchm[YOUR STUDENT NUMBER] - Replicated items** blade, ensure that there is an entry representing the **azLab-vm-vm** Azure VM and verify that its **REPLICATION HEALTH** is **Healthy** and that its **STATUS** is **Enabling protection**.

    > **Note**: You might need to wait a few minutes until the **azLab-vm-vm** entry appears on the **azvaultchm[YOUR STUDENT NUMBER] - Replicated items** blade.

1. From the **azvaultchm[YOUR STUDENT NUMBER] - Replicated items** blade, display the replicated item blade of the **azLab-vm-vm** Azure VM.

1. On the **azLab-vm-vm** replicated item blade, review the **Health and status**, **Failover readiness**, **Latest recovery points**, and **Infrastructure view** sections. Note the **Failover** and **Test Failover** toolbar icons.

    > **Note**: The remaining steps of this task are optional and not graded.

1. If time permits, wait until the replication status changes to **100% synchronized**. This might take additional 90 minutes.

1. Examine the values of **RPO**, as well as **Crash-consistent** and **App-consistent** recovery points.

1. Perform a test failover to the **azLab-vm-vnet-asr** virtual network.

> **Result**: After you completed this exercise, you have configured replication of an Azure VM and reviewed Azure VM replication settings.