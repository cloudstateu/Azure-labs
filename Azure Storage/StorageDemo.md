# Lab: Azure Storage

## Lab Scenario
You will create a process which will import images from any URL to your CDN. Goal of this lab is to utilize Azure Storage resources: Blob containers, Queues, Tables.


## Objectives
After you complete this lab, you will be able to:
-   Use Storage Queues to trigger Azure Function   
-   Output blobls from Azure Funtion to Storage Account
-   Create and configure Content Delivery Network
-   Write data to Azure Tables
-   Use Storage Explorer


## Instructions

### Exercise 1: Create Azure resources

#### Task 1: Open the Azure portal

1.  Sign in to the Azure portal (<https://portal.azure.com>) with your lab account.

#### Task 2: Create a Storage account

1.  Create a new **storage account** with the following details:    
    -	Resource group: **[your-resource-group]**
    -	Name: **contenthost[yourname]**
    -	Location: **West Europe**
    -	Performance: **Standard**
    -	Account kind: **StorageV2 (general purpose v2)**
    -	Replication: **Locally-redundant storage (LRS)**

1. Ignore other options and press "Review + create" then "Create"

    > **Note**: Wait for Azure to finish creating the storage account before you move forward with the lab. You'll receive a notification when the account is created.


#### Task 3: Create a container for images

1. Go to the newly created Azure Storage Account.

1. Locate "Containers" item on the left side menu.

2. In the "Containers" blade press "+ Container" at the top and provide following settings
    - container name: **images**
    - Public access level: **Container (anonymous read access for container and blobs)**


#### Task 4: Create a queue

1. In the Azure Storage Account created in the previous step, locate "Queues" item on the left side menu.

2. In the "Queues" blade press "+ Queues" at the top and provide following settings
    - queue name: **links**

#### Review
In this exercise, you created an Azure Storage account with contianer for images and a queue which be used to trigger process importing images to your storage.


### Exercise 2: Setup Azure Function App to transfer any image to your storage

1.  Create a new **Function App** with the following details:    
    Basics:
    -	Resource group: **[your-resource-group]**
    -	Function App name: **chm-functionapp-[yourname]**
    -   Publish: **Code**
    -   Runtime stack: **.NET**
    -   Version: **3.1**
    -	Region: **West Europe**

    Hosting  (default):
    -	Storage account: **NEW** (leave default)
    -	Operating system: **Windows**
    -	Plan: **Consumption (serverless)**

    Monitoring  (default):
    -	Enable Application Insights: **Yes**
    -	Application Insights: **NEW** (leave default)

1. Press "Review + create" then "Create" and wait for the deployment to be completed. 

1. Go to the newly created Azure Funtion App.

1. Locate "Functions" on the left side menu and press "+ Add" at the top.

1. Add function with following settings:
   - Development environment: **Develop in portal**
   - Select a template: **Azure Queue Storage trigger**

    Template details
    - New Function: **QueueTrigger1**
    - Queue name: **links**
    - Storage account: press **New** and select storage account created in earlier in this lab (**contenthost[yourname]**)

1. Press "Add" and wait for the function to be created. Next, go to the "Integration" from the left side menu.

1. In the "Outputs" section press "+ Add output" and provide following settings:
    -   Binding Type: **Azure Blob Storage**
    -   Parameter name: **outputBlob**
    -   Path: **images/{rand-guid}.png**
    -   Storage account: **contenthost[yourname]_STORAGE**

1. Confirm settings and go to the "Code + Test" from the left side menu.

1. Replace "run.csx" file contents with the following code:
    ```cs
    using System;
    using System.Net;

    public static void Run(string myQueueItem, out byte[] outputBlob, ILogger log)
    {
        using (var wc = new WebClient())
        {
            outputBlob = wc.DownloadData(myQueueItem); 
        }
    }
    ```

### Exercise 3: Test the process

1. Go to the **links** Azure Storage Queue created earlier in this lab in the **contenthost[yourname]** Storage Account.

1. Press "+ Add message" at the top nad paste any ***.png** image from the Internet. 
    > You may use this sample image: `https://cdn2.thecatapi.com/images/b3m.png`

1. Press "Refresh" at the top. The message should disappear from the list because it has been processed by the Azure Function.

2. Close gueue blade in your storage account and locate **images** container. Your image should be listed in the container with a random filename. Download this file and verify whether it's the same image.

#### Review
In this exercise, you used Azure Function App to take a message from Storage Queue and upload a file to Blob Conainer.


### Exercise 4: Configure Content Delivery Network and endpoints

#### Task 1: Create a Content Delivery Network profile

1. In the Azure Storage Account created earlier in this lab, locate "Azure CDN" item on the left side menu.

2.  Create a new Content Delivery Network profile with the following details:    
    -   Name: **contentdeliverynetwork**
    -	Pricing tier: **Standard Akamai**
    -   CDN endpoint name: **chm-cdnendpoint-[yourname]**
    -   Origin hostname: **contenthost[yourname].blob.core.windows.net (Blob)** (leave default)
  
    > **Note**: Wait for Azure to finish creating the CDN profile before you move forward with the lab. You'll receive a notification when the app is created.

3. From the newly created endpoint blade, copty the **Endpoint hostname
** value and save it for later. 

4. Go to the **images** container created earlier and copy any blob file name.

5. Open web browser and paste values from the previous steps in this format: `[your endpoint hostname]/images/[blob file name]`. 

6. You should be prompted to download the image.

#### Review
In this exercise, you registered enpoints for your Delivery Network and then used the endpoint to download files.

### Exercise 5: Output data to Storage Table

#### Task 1: Create a Storage Table

1. In the Azure Storage Account created earlier in this lab, locate "Tables" item on the left side menu.

2. In the "Tables" blade press "+ Table" at the top and create a table named **linkslog**

#### Task 2: Modify Function App to output data to table

1. Go to the "QueueTrigger1" funtion in the **chm-functionapp-[yourname]** created earlier in this lab. 

1. Open "Integration" add press "+ Add output". Provide followind details in the "Create Output" blade:
    -   Binding Type: **Azure Table Storage**
    -   Parameter name: **outputTable**
    -   Table name: **linkslog**
    -   Storage account: **contenthost[yourname]_STORAGE**

1. Next paste the following code in the "Code + Test" blade and press "Save"
   ```cs
    using System;
    using System.Net;

    public static void Run(string myQueueItem, out byte[] outputBlob, ICollector<LinkLog> outputTable, ILogger log)
    {
        using (var wc = new WebClient())
        {
            outputBlob = wc.DownloadData(myQueueItem); 
        }

        outputTable.Add(new LinkLog
        {
            PartitionKey = "chm-demo",
            RowKey = Guid.NewGuid().ToString(),
            Url = myQueueItem,
            ImgSize = outputBlob.Length 
        });
    }

    public class LinkLog
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Url { get; set; }
        public int ImgSize { get; set; }
    }
   ```

#### Task 3: Verify data in Storage Table

1. In the Azure Storage Account created earlier in this lab, go to **links** queue and add a new message with a link.

1. Wait for the message to be processed and go to the "Storage Explorer" located in the left side menu.

1. In the Storage Explorer, naviage to **Tables** and **linkslog**.

1. Verify whether row created in the previous task is present.

#### Review
In this exercise, your Azure Function outputted data to Storage Table and you used Storage Explorer to browse data in storage account.