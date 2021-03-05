
# Create a Front Door for a highly available global web application

Get started with Azure Front Door by using the Azure portal to set up high availability for a web application.

## Create two instances of a web app

This quickstart requires two instances of a web application that run in different Azure regions. Both the web application instances run in *Active/Active* mode, so either one can take traffic. This configuration differs from an *Active/Stand-By* configuration, where one acts as a failover.

If you don't already have a web app, use the following steps to set up example web apps.

1. Sign in to the Azure portal at https://portal.azure.com.

1. On the top left-hand side of the screen, select **Create a resource** >  **WebApp**.
 

1. In the **Basics** tab of **Create Web App** page, enter or select the following information.

    | Setting                 | Value                                              |
    | ---                     | ---                                                |
    | **Subscription**               | Select your subscription. |    
    | **Resource group**       | Select **Create new** and enter *your_resource_group_name* in the text box.|
    | **Name**                   | Enter a unique **Name** for your web app. This example uses *WebAppExample-1*. |
    | **Publish** | Select **Code**. |
    | **Runtime stack**         | Select **.NET Core 3.1 (LTS)**. |
    | **Operating System**          | Select **Windows**. |
    | **Region**           | Select **West Europe**. |
    | **Windows Plan** | Select **Create new** and enter *myAppServicePlan* in the text box. |
    | **Sku and size** | Select **Standard S1 100 total ACU, 1.75 GB memory**. |

1. Select **Review + create**, review the **Summary**, and then select **Create**. It might take several minutes for the deployment to complete.

1. Deploy application on newly created Web App 

   1.  Open the Windows Terminal application.
   1.  Sign in to the Azure CLI by using your Azure credentials and list Web Apps in your resource group:

    ```
    az login
    az webapp list --resource-group YOUR_RESOURCE_GROUP
    ```

   3.  Change the current directory to the **Azure-labs/Front Door/files/** directory that contains the lab files.

   3.  Deploy the **publishWestEurope.zip** file to the web app that you created earlier in this lab:

    ```
    az webapp deployment source config-zip --resource-group YOUR_RESOURCE_GROUP --src publishWestEurope.zip --name <name-of-your-api-app>
    ```

    > **Note**: Replace the *\<name-of-your-api-app\>* placeholder with the name of the web app that you created earlier in this lab. You recently queried this app’s name in the previous steps.


After your deployment is complete, create a second web app. Use the same procedure with the same values, except for the following values:

| Setting          | Value     |
| ---              | ---  |
| **Resource group**   | Select **Create new** and enter *your_second_resource_group_name* |
| **Name**             | Enter a unique name for your Web App, in this example, *WebAppExample-2*  |
| **Region**           | A different region, in this example, *East US* |
| **App Service plan** > **Windows Plan**         | Select **New** and enter *myAppServicePlanSouthCentralUS*, and then select **OK** |


1. Deploy application on newly created Web App 

   1.  Open the Windows Terminal application.
   1.  Sign in to the Azure CLI by using your Azure credentials and list Web Apps in your resource group:

    ```
    az login
    az webapp list --resource-group YOUR_RESOURCE_GROUP
    ```

   3.  Change the current directory to the **Azure-labs/Front Door/files/** directory that contains the lab files.

   3.  Deploy the **publishEastUs.zip** file to the web app that you created earlier in this lab:

    ```
    az webapp deployment source config-zip --resource-group YOUR_RESOURCE_GROUP --src publishEastUs.zip --name <name-of-your-api-app>
    ```

    > **Note**: Replace the *\<name-of-your-api-app\>* placeholder with the name of the web app that you created earlier in this lab. You recently queried this app’s name in the previous steps.

## Create a Front Door for your application

Configure Azure Front Door to direct user traffic based on lowest latency between the two web apps servers. To begin, add a frontend host for Azure Front Door.

1. From the home page or the Azure menu, select **Create a resource**. Select **Networking** > **See All** > **Front Door**.

1. In the **Basics** tab of **Create a Front Door** page, enter or select the following information, and then select **Next: Configuration**.

    | Setting | Value |
    | --- | --- |
    | **Subscription** | Select your subscription. |    
    | **Resource group** | Select **Create new** and enter *your_resource_group_name* in the text box.|
    | **Resource group location** | Select **East US**. |

1. In **Frontends/domains**, select **+** to open **Add a frontend host**.

1. For **Host name**, enter a globally unique hostname eg. *workshop-name-student*. Select **Add**.



Next, create a backend pool that contains your two web apps.

1. Still in **Create a Front Door**, in **Backend pools**, select **+** to open **Add a backend pool**.

1. For **Name**, enter *myBackendPool*, then select **Add a backend**.
    

1. In the **Add a backend** blade, select the following information and select **Add**.

    | Setting | Value |
    | --- | --- |
    | **Backend host type** | Select **App service**. |   
    | **Subscription** | Select your subscription. |    
    | **Backend host name** | Select the first web app you created. In this example, the web app was *WebAppExample-1*. |

    **Leave all other fields default.*

1. Select **Add a backend** again. select the following information and select **Add**.

    | Setting | Value |
    | --- | --- |
    | **Backend host type** | Select **App service**. |   
    | **Subscription** | Select your subscription. |    
    | **Backend host name** | Select the second web app you created. In this example, the web app was *WebAppExample-2*. |

    **Leave all other fields default.*

1. Select **Add** on the **Add a backend pool** blade to complete the configuration of the backend pool.

   
Finally, add a routing rule. A routing rule maps your frontend host to the backend pool. The rule forwards a request for `workshop-name-student.azurefd.net` to **myBackendPool**.

1. Still in **Create a Front Door**, in **Routing rules**, select **+** to configure a routing rule.

1. In **Add a rule**, for **Name**, enter *LocationRule*. Accept all the default values, then select **Add** to add the routing rule.

   >[!WARNING]
   > You **must** ensure that each of the frontend hosts in your Front Door has a routing rule with a default path (`\*`) associated with it. That is, across all of your routing rules there must be at least one routing rule for each of your frontend hosts defined at the default path (`\*`). Failing to do so may result in your end-user traffic not getting routed correctly.

1. Select **Review + Create**, and then **Create**.

   



## View Azure Front Door in action

Once you create a Front Door, it takes a few minutes for the configuration to be deployed globally. Once complete, access the frontend host you created. In a browser, go to `workshop-name-student.azurefd.net`. Your request will automatically get routed to the nearest server to you from the specified servers in the backend pool.

If you created these apps in this quickstart, you'll see an information page.

To test instant global failover in action, try the following steps:

1. Open a browser, as described above, and go to the frontend address: `workshop-name-student.azurefd.net`.

1. In the Azure portal, search for and select *App services*. Scroll down to find one of your web apps, **WebAppExample-1** in this example.

1. Select your web app, and then select **Stop**, and **Yes** to verify.

1. Refresh your browser. You should see the same information page.

   >[!TIP]
   >There is a little bit of delay for these actions. You might need to refresh again.

1. Find the other web app, and stop it as well.

1. Refresh your browser. This time, you should see an error message.
<!-- 
## Clean up resources

After you're done, you can remove all the items you created. Deleting a resource group also deletes its contents. If you don't intend to use this Front Door, you should remove resources to avoid unnecessary charges.

1. In the Azure portal, search for and select **Resource groups**, or select **Resource groups** from the Azure portal menu.

1. Filter or scroll down to find a resource group, such as **FrontDoorQS_rg0**.

1. Select the resource group, then select **Delete resource group**.

   >[!WARNING]
   >This action is irreversable.

1. Type the resource group name to verify, and then select **Delete**.

Repeat the procedure for the other two groups.

## Next steps

Advance to the next article to learn how to add a custom domain to your Front Door.
> [!div class="nextstepaction"]
> [Add a custom domain](front-door-custom-domain.md) -->