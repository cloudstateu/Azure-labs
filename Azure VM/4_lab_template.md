# Lab: Azure AD Identity Protection

All tasks in this lab are performed from the Azure portal.

Lab files:

-  **vm-template.json**

-  **vm-template.parameters.json**


### Objectives

The main tasks for this exercise is to deploy an Azure VM by using an Azure Resource Manager template.


#### Exercise: Deploy an Azure VM by using an Azure Resource Manager template

1. Start a web browser and fo to Azure portal at [**http://portal.azure.com**](http://portal.azure.com). Sign in by using a student account you were provided.

1. In the Azure portal, navigate to the **New** blade.

1. From the **New** blade, search Azure Marketplace for **Template deployment**.

1. Use the list of search results to navigate to the **Custom deployment** blade.

1. On the **Custom deployment** blade, select the **Build your own template in the editor**.

1. From the **Edit template** blade, load the template file **vm-template.json**.

     > **Note**: Review the content of the template and note that it defines deployment of an Azure VM hosting Windows Server 2016 Datacenter.

1. Save the template and return to the **Custom deployment** blade.

1. From the **Custom deployment** blade, navigate to the **Edit parameters** blade.

1. From the **Edit parameters** blade, load the parameters file **vm-template.parameters.json**.

1. Save the parameters and return to the **Custom deployment** blade.

1. From the **Custom deployment** blade, initiate a template deployment with the following settings:

    - Subscription: the name of the subscription you are using in this lab

    - Resource group: the name of a new resource group **YOUR RESOURCE GROUP**

    - Location: **West Europe**

    - Vm Size: **Standard_DS1_v2**

    - Vm Name: **azVm-[YOUR STUDENT NUMBER]**

    - Admin Username: **Student**

    - Admin Password: **Pa55w.rd1234**

    - Virtual Network Name: **azLabVnet1**

1. Click on **Review + create**


> **Result**: After you completed this exercise, you have initiated a template deployment of an Azure VM, now you can review VM in the Overview.
