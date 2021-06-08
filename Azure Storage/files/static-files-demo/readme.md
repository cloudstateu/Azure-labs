# Lab: Azure Storage Static website

## Prerequisities
* [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer/)

---

## Instructions

### Exercise 1: Create Storage Account Azure resources

#### Task 1: Open the Azure portal

1.  Sign in to the Azure portal (<https://portal.azure.com>) with your lab account.

#### Task 2: Create a Storage account
   > **Note:** You can skip this excercise if you already have storage account

1.  Create a new **storage account** with the following details:    
    -	Resource group: **[your-resource-group]**
    -	Name: **contenthost[yourname]**
    -	Location: **West Europe**
    -	Performance: **Standard**
    -	Account kind: **StorageV2 (general purpose v2)**
    -	Replication: **Locally-redundant storage (LRS)**

1. Ignore other options and press "Review + create" then "Create"

    > **Note**: Wait for Azure to finish creating the storage account before you move forward with the lab. You'll receive a notification when the account is created.

#### Task 3: Create Static website

1. Go to the newly created Azure Storage Account.

1. Locate "Static website" item on the left side menu under "Data management".

1. Switch toggle to "Enabled", provide following settings and press "Save"
    - Index document name: **index.html**
    - Error document path: **error.html**

#### Review
You created an Azure Storage container to host your static website.

### Exercise 2: Upload website files to your storage

#### Task 1: Download sample landing page

1. Go to [Cloudstate's GitHub](https://github.com/cloudstateu/Azure-labs/tree/main/Azure%20Storage/files) and download `static-files-demo.zip`

1. Extract ZIP contents to your local hard drive. This files will be used later in this lab.

1. Edit following files and insert values you prefer: 
   * **index.html** > line 20: `<h1 id="text01">&lt;your name&gt;</h1>`
   * **/assets/main.js** > line 1443: `timestamp` (Use [epoch time converter](https://www.epochconverter.com/)) 

#### Task 2: User Azure Storage Explorer

1. Run [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer/) on your machine.

1. On the "Account Management" tab press "Add an account" and use your lab account to authenticate in Azure.

1. After successful authentication go to "Explorer" and navigate to the cloud resources
    `Subscription ("key icon") > Storage Accounts > contenthost[yourname] > Blob containers > &web`

1. Press "Upload" > "Upload files" and upload **index.html** from **static-files-demo** which you downloaded in the previous steps.

1. Press "Upload" > "Upload folder" and upload **assets** folder from **static-files-demo**.

#### Task 3: Verify your static website

1. Go back to Azure Portal to the "Static website" in Azure Storage Account created earlier in this lab.

1. Locate "Primary endpoint" and copy it's value. 

1. Paste the endpoint in the new browser tab. 
   
1. Verify whether website contains data you provided earlier.

#### Review
You used Azure Storage Explorer application to upload your website to the Azure Storage Static website.