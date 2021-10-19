# Lab: Create an Azure Policy


### Objectives

This lab introduces you to Azure Policy. We will create an Azure Policy to restrict deployment of Azure resources to a specific location.

### Exercise 0: Prepare Resource Group

1. Sign in to the Azure portal.

1. In services blade, search for and select Resource Groups.

1. Click on **+ Create** button, on the left corner.

1. Enter your resource group name e.g. **student0x**, select West Europe region and click **Review + Create**, finally review summary and click **Create**.

### Exercise 1: Create a Policy assignment

In this task, we will configure the allowed location policy and assign it to our subscription.

1. In services blade, search for and select Policy.

1. Under the Authoring section click Assignments. An assignment is a policy that has been assigned to take place within a specific scope. For example, a definition could be assigned to the subscription scope.

1. Click Assign Policy at the top of the Policy - Assignments page.

1. On the Assign Policy page, as a Scope select Resource Group created previously.

            Scope: student0x
            Policy definition: click elipses then search **Allowed Locations** then Select
            Assignment Name: Allowed Locations

1. On the Parameters tab, select West Europe. Click Review + create, and then Create.

      **Note:** A scope determines what resources or grouping of resources the policy assignment applies to. In our case we assigned this policy to a specific resource group, however we could assign the policy at subscription level. Be aware that resources can be excluded based on the scope configuration. Exclusions are optional.

1. The Allowed locations policy assignment is now listed on the Policy - Assignments pane and it is now in place, enforcing the policy at the scope level we specified (resource group level).

### Exercise 2: Test Allowed location policy
In this task, we will test the Allowed location policy.

1. In the Azure Portal, from the All services blade, search for and select Storage accounts, and then click **+ Create**.

1. Configure the storage account. Leave the defaults for everything else.

            Subscription: use lab subscription
            Resource group: student0x
            Storage account name: student0xstorageaccount (globally unique)
            Location: (US) East US
            Click Review + create and then click Create.

You will receive validation error stating that resource was disallowed by policy, including the Allowed locations policy name.

### Exercise 3: Create tag requirement on resource

In this section, we will create another policy to enforce existence of a tag.

1. Go back to Policy - Assignments page.

1. In Assign policy, create policy with your Resource Group scope. Next search for policy named **Require a tag on resources**
            Scope: student0x
            Policy definition: click elipses then search **Require a tag on resources** then Select
            Assignment Name: Require a tag on resources

1. On the Parameters tab, enter requiredd Tag name - **env**. Click Review + create, and then Create.

### Exercise 4: Test tag requirement on resource

Final objective is to validate tag requirement for a tag.

1. In the Azure Portal, from the All services blade, search for and select Storage accounts, and then click **+ Create**.

1. Configure the storage account. Leave the defaults for everything else.

            Subscription: use lab subscription
            Resource group: student0x
            Storage account name: student0xstorageaccount (globally unique)
            Location: West Europe	
            Click Review + create and then click Create.

You will receive validation error stating that resource was disallowed by policy that states a requirement for tag on resources.
