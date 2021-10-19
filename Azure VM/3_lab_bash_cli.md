# Lab: Create a VM with the CLI (10 min)

### Objectives
In this walk-through, we will configure the Cloud Shell, use Azure CLI to create a resource group and virtual machine, and review Azure Advisor recommendations.

# Exercise 0: Configure the Cloud Shell
In this task, we will configure Cloud Shell, then use Azure CLI to create a resource group and a virtual machine.

Sign in to the Azure portal.

From the Azure portal, open the Azure Cloud Shell by clicking on the icon in the top right of the Azure Portal.

In the Welcome to Azure Cloud Shell dialog, when prompted to select either Bash or PowerShell, select Bash.

A new window will open stating You have no storage mounted. Select advanced settings.

In the advanced settings screen, fill in the following fields, then click Create Storage:

        Resource Group (new): student0x
        Storage account (Create a new account a use a globally unique name): student0xstorage
        File share (create new): **student0xshellstorage** 
# Exercise 1: Use CLI to create a virtual machine
In this task, we will use Azure CLI to create a resource group and a virtual machine.

Ensure Bash is selected in the upper-left drop-down menu of the Cloud Shell pane (and if not, select it).

Verify the resource group you are using by entering the following command.

        az group list --output table
In Cloud Shell enter the command below and make sure that each line, except for the last one, is followed by the backslash (\) character. If you type the whole command on the same line, do not use any backslash characters.

        az vm create \
        --name student0x \
        --resource-group student0x \
        --image UbuntuLTS \
        --location West Europe \
        --admin-username student0x \
        --admin-password --password--
Note: If you are using the command line on a Windows computer, replace the backslash (\) character with the caret (^) character.

Note: The command will take 2 to 3 minutes to complete. The command will create a virtual machine and various resources associated with it such as storage, networking and security resources. Do not continue to the next step until the virtual machine deployment is complete.

When the command finishes running, in the browser window, close the Cloud Shell pane.

In the Azure portal, search for Virtual machines and verify that myVMCLI is running.

Screenshot of the virtual machines page with myVMPS in a running state.

# Exercise 2: Execute commands in the Cloud Shell
In this task, we will practice executing CLI commands from the Cloud Shell.

Ensure Bash is selected in the upper-left drop-down menu of the Cloud Shell pane.

Retrieve information about the virtual machine you provisioned, including name, resource group, location, and status. Notice the PowerState is running.

        az vm show --resource-group student0x --name student0x --show-details --output table 
Stop the virtual machine. Notice the message that billing continues until the virtual machine is deallocated.

        az vm stop --resource-group student0x --name student0x
Verify your virtual machine status. The PowerState should now be stopped.

        az vm show --resource-group student0x --name student0x --show-details --output table 
