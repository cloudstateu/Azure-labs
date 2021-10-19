# Lab: Create Azure VM with PowerShell


### Objectives

In this walk-through, we will configure the Cloud Shell, use Azure PowerShell module to create a resource group and virtual machine.

# Exercise 0: Configure the Cloud Shell 

In this task, we will configure Cloud Shell. 

1. Sign in the Azure portal at https://portal.azure.com 

1. From the Azure portal, open the **Azure Cloud Shell** by clicking on the icon in the top right of the Azure Portal.

1. When prompted to select either **Bash** or **PowerShell**, select **PowerShell**.

1. On the **You have no storage mounted** screen select **Show advanced settings** then fill in the information below

        Resource Group (new): student0x
        Storage account (Create a new account a use a globally unique name): student0xstorage
        File share (create new): **shellstorage** 

1. Select **Create Storage**

# Exercise 1: Create a resource group and virtual machine

In this task, we will use PowerShell to create a resource group and a virtual machine.  

1. Ensure **PowerShell** is selected in the upper-left drop-down menu of the Cloud Shell pane.

1. Verify your new resource group by running the following command in the Powershell window. Press **Enter** to run the command.

    ```PowerShell
    Get-AzResourceGroup | Format-Table
    ```

1. Create a virtual machine by pasting the following command into the terminal window. 

    ```PowerShell
    New-AzVm `
    -ResourceGroupName "student0x" `
    -Name "student0x" `
    -Location "West Europe" `
    -VirtualNetworkName "student0x-vnet" `
    -SubnetName "student0x-subnet" `
    -SecurityGroupName "student0x-nsg" `
    -PublicIpAddressName "student0x-ip"
    ```
    
1. When prompted provide the username (**student0x**) and the password that will be configured as the local Administrator account on that virtual machines.azureadmin

1. Once VM is created, close the PowerShell session Cloud Shell pane.

1. In the Azure portal, search for **Virtual machines** and verify the VM named **student0x** is running. This may take a few minutes.

1. Access the new virtual machine and review the Overview and Networking settings to verify your information was correctly deployed. 

# Exercise 2: Execute commands in the Cloud Shell

In this task, we will practice executing PowerShell commands from the Cloud Shell. 

1. Retrieve information about your virtual machine including name, resource group, location, and status. Notice the PowerState is **running**.

    ```PowerShell
    Get-AzVM -name student0x -status | Format-Table -autosize
    ```

1. Stop the virtual machine using the following command. 

    ```PowerShell
    Stop-AzVM -ResourceGroupName student0x -Name student0x
    ```
1. When prompted confirm (Yes) to the action. Wait for **Succeeded** status.

1. Verify your virtual machine state. The PowerState should now be **deallocated**. You can also verify the virtual machine status in the portal. Close Cloudshell.

    ```PowerShell
    Get-AzVM -name student0x -status | Format-Table -autosize
    ```