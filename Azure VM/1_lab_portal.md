# Lab: Create Azure VM by using the Azure Portal 


### Objectives

This lab introduces you how to create Virtual Machine instance from Azure Market Place image using Azure Portal, install IIS on Azure VM and add rules to security group of VM to share IIS Web Server to internet. 

### Exercise 1: Create a Windows VM Instance

In this section you will learn how to create a Windows Server 2016 Datacenter from Azure Market Place image. 

 

1. Sign in the Azure portal at https://portal.azure.com 

1. On the left Hub menu click Virtual Machines. 

1. On the Virtual Machines blade click the **+ Create** button.

1. On the search box labeled as Search Compute type Windows Server 2016 Datacenter. 

1. Then after searching for results, click on the item Windows Server 2016 Datacenter. 

1. On the Windows Server 2016 Datacenter page select a deployment model – Resource Manager and click on button Create. 

1. On the configuration **Basic** settings blade provide the following configuration: 

            Subscription: your lab subscription
            ResourceGroup: student0x 
            Name: student0x
            Region: West Europe 

            User name: student0x 
            Password: type and remember your password-secret 
            Confirm password: confirm your password-secret above 

            Public inbound ports: None

1. On the **Disks** blade disk type, choose Standard HDD and make sure you use managed disks option.


1. On the **Networking** settings blade: 

            Virtual network: (new) student0x-vnet 
            Subnet: default (10.0.16.0/24) 
            Public IP address: (new) student0x-ip
            Network Security Group: (new) student0x-nsg 


1. Next configure monitoring and **managment** options: 

            Boot diagnostics: Enabled 
            Guest OS diagnostics: Disabled 
            Enable auto-shutdown: Off 
            Backup: Disabled  

1. Finally go to **Review + create**, check configuration and when you're ready, **Create** your Virtual Machine.

1. Next step the Azure Virtual Machine will be deployed. 

### Exercise 2: Connect to Windows VM Instance 


In this section you will learn how to connect to previously created instance of Azure Virtual Machine using the RDP protocol. 


1. After successful deployment of Azure VM, on the left Hub menu click Virtual Machines. 

1. Click on your name of Azure instance VM - student0x. 

1. On the **Connect** blade, select RDP and Download RDP File

1. On the Remote Desktop Connection click on Connect button. 

1. Enter credentials: 

            User name: student0x 
            Password: your previously entered password-secret 

      Next click on connect. 

1. Confirm the certificate by clicking yes button. 

1. You are logged in to the Azure Virtual Machine. 

### Exercise 3: Install IIS 

In this section you will learn how install and add role as IIS Web Server on Azure Virtual Machine. 

1. Log to your Azure Virtual Machine Instance. 

1. On the Windows menu click on Server Manager. 

1. On the Server Manager window click on add roles and features. 

1. On the Add Roles and Features Wizard provide the next step configurations: 

 

            Before you begin: click on Next. 

            Installation Type: select Roles-Base or feature-based installation. 

            Server Selection: click on Next. 

            Server Roles: add WebServer (IIS) and Add Features, click on Next. 

            Features: click on Next. 

            Role Services: click on Next. 

            Confirmation: click on Install. 

 

1. After installation of IIS, open any web browser on Azure VM host where IIS Web Server is running and type the following address: 

      http://localhost 

      You should see the welcome page of locally IIS Web Server. 

### Exercise 4: Open port on Azure VM 

In this section you learn how open port from security group in Azure Portal to share IIS Web Server on the internet. 

1. On the Virtual Machine click on **Networking**. 

1. On the Networking blade click on **Add inbound port rule** button. 

1. On the Add inbound port rule blade set: 

            Destination port ranges: 80   

      Click on **Add** button. 

1. After creating security rule go on Overview blade and copy Public IP address to any web browser but not on your Azure VM host which IIS Web Server Role. 

1. You should see welcome IIS website from Azure VM IIS Web Sever. 