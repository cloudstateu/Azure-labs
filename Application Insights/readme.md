# Lab: Monitoring application with Azure Application Insights

## Objectives

In this lab you will learn how to monitor your application with Azure Application Insights. For the purpose of this lab  you will create a database with Adventure Works sample data and deploy a .NET application which connects to the database. The application will also get data from external service. All of those dependencies will be visible in Application Insights monitoring.


## Instructions

### Task 1: Create a database

1. Go to Azure Portal and press "+ Create a resource"
   
2. Select **Databases** and **SQL Database** and provide following details in "Basis" tab:    
    -   Resource group: **YOUR_RESOURCE_GROUP**
    -   Database name: **aw-db-chm-studentX** [replace X with your student number]
    -   Server: **Create new**
        -   Server name: **chm-studentX-sqlserv** [replace X with your student number]    
        -   Server admin login / password: Provide and remember this data
        -   Location: **West Europe**
    -   Want to use SQL elastic pool?: **No**
    -   Compute + storage: **Serverless**

3. Press **Next: Networking** and provide following details:
   - Connectivity method: **Public endpoint**
   - Allow Azure services and resources to access this server: **No**
   - Add current client IP address: **Yes**
  
4. Press **Next: Additional settings** and provide following details:
    - Use existing data: **Sample**
    - Enable Azure Defender for SQL: **Not now**

5. Press **Review and create**, then **Create** and wait for the resources to be deployed, then **Go to the resource**

6. On your database overview blade press **Show database connection strings** in the upper right part of the screen.

7. Record the value of the **ADO.NET** text box. You'll use this value later in this lab.
   > **Note:** You have to replace the password value in this connection string.


### Task 2: Create an App Service

1. In Azure Portal press "+ Create a resource" in the search box type **web app**

2. Select **Web app** from the list, press **Create** and provide following details:
    -   Existing resource group: **YOUR_RESOURCE_GROUP**    
    -   Name: **app-chm-studentX** [replace X with your student number]
    -   Publish: **Code**
    -	Runtime stack: **.NET Core 3.1 (LTS)**
    -	Operating System: **Windows**
    -	Region: **West Europe**
    -	Windows plan: **Create new:** **app-service-plan-chm-studentX** [replace X with your student number]    
    -	SKU and size: **Standard (S1)**

3. Press **Next: Networking** and provide following details:
    - Enable Application Insights: **Yes**
    - Application Insights: **Create new: monitoring-chm-studentX** [replace X with your student number]

1. Press **Review and create**, then **Create** and wait for the resources to be deployed, then **Go to the resource**.


### Task 2: Deploy the application to App Service 

1. Access the web app that you created earlier in this lab.

2. In the **Deployment** section on the left, find the **Deployment Center (Classic)** section, and then select **External** tile and press **Continue** at the bottom.

3. Select **App Service build service** and press **Continue**.

4. In the "Configure" step provide following details
   - Repository: https://github.com/cloudstateu/AdventureWorksLT-Sample-CsApp
   - Branch: **main**
   - Repository Type: **Git**
   - Private Repository: **No**

5. Press **Continue** and wait for succesful deployment.
   > **Note:** In the deployment blade, wait for the "Success" in the "Status" column.
   

### Task 3: Configure the web app for monitoring and database connection

1. Access the web app that you created earlier in this lab.

1. In the "Overview" section locate URL (upper right part of the screen) and on open the link in the new tab. 

1. Back in the Azure Portal, locate **Settings** section, click **Application Insights** and **View Application Insights Data**.

1. Go to **Live metrics** inside the **Investigate** section on the left. Wait for the stream to be connected and leave this dashboard opened.

2. Go to your application website again (in the other browser tab) and press **DB Products count** in the menu. The application should fail at this moment.

1. Go back to **Live metrics** and get yourself familiar with the logged telemetry and exceptions.

3. Time to fix the database connection string. Naviage to your **App Service** blade, locate the **Settings** section and press  **Configuration**. 
   
4. On the "Configuration" blade locate **Connection string** section and press **New Connection string**. Enter following details:    
    -   Name: **Default**
    -   Value: ***Database Connection String copied earlier in this lab***
    -   Type: **SQLServer**

5. Press **OK** and **Save** at the top of the page. Wait for the application to be restarted and refresh website. Now the request should be succesful. 

6. Go to your application **Live metrics** once again and edit **Sample telemetry** filter in the upper right part of the screen. 

1. In the query builder, remove both ``Success = false`` filters in order to view all the data and press **Save**.

1. Go to your website again and spend some time making different requests. Browse the metrics in Application Insights at any time. 

1. At the end, go to your **Application Insights** resource and locate **Application map** in the **Investigate** section. Make yourself familiar with this tool.