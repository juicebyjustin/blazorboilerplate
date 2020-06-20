# Hosting Blazor Boilerplate on Microsoft Azure  
Thanks to [soomon](https://github.com/soomon) For this the contribution of this wiki.

# Pre-requisites / Assumptions


* Please be aware that hosting on Microsoft Azure will cost money.
* This tutorial assumes that you do already have a working azure subscription.
* In this example, Blazor Boilerplate is being hosted using App Services and a managed SQL database.
* The certificate for Identity Server has been stored in Azure Key Vault.
* Please ensure that for all resources created in this tutorial, you choose the same subscription, region and resource group!
* All URIs, resources, user names and passwords shown have been deleted prior to publishing.

 


# Create Resource Group

If you do not already have a resource group, create a new one.

* Open up Microsoft Azure
* Search for Resource Group in the search field
* Select Resource groups
* Click Add
* Choose your subscription, a name and a region
* Click Review + Create
* Click Create
* Wait for the deployment to finish 


 
# Create App Service Plan

Next, we need an App Service Plan to host our application

* Search for “App Service Plans”
* Click “Create app service plan”
* Choose your subscription, resource group, a name for the plan and a region to host it.
* Select Windows as the operating system
* Click on „Change size“ to select the free tier for app service plans
* Click „Dev / Test“ and select “Share infrastructure”
* Click „Apply“
* Review your choices.
* Click „Review + create“
* Click „Create“
* Wait for the deployment to finish

 
 
# Create App Service

* Search for “App Services”
* Choose „App Services“
* Click „Create app service“
* Choose your subscription, resource group, a name for the app, publish code, .Net Core Runtime, Windows and region.

Please note: Although Blazor Boilerplate currently targets .Net 3.1, .Net Core 3.0 has been selected as choosing 3.1 will switch the OS from Windows to Linux. For debugging purposes, Windows is being preferred. The app will still work.

* Click “Review + Create”
* Click “Create”
* After the deployment has finished, select “Go to resource”
* Select “Identity” on the left hand side.
* Note that “Status” for System assigned identities is switched to “Off" by default. Enable it and click on “Save”
* Accept to restart the app.
* Click on “Get publish profile” to download the publish profile.

 
 

## Import the Publish Profile to Visual Studio

* In Visual Studio, open the Blazor Boilerplate Solution.
* Right-Click on BlazorBoilerplate.Server and select “Publish…”
* Click on “Start”
* Choose “Import Profile…”
* Select the downloaded profile.
* Click “Open”
* Three profiles will be imported. We will use the “Web Deploy” profile in this tutorial.

   
# Create SQL Server

* Back in Azure, search for “SQL servers”
* Choose “SQL servers” 
* Click “Add”
* Choose your Subscription, Resource group, Server name, Location, Server admin login and Password. Also, confirm the password.
* Click Create
* Once the deployment is finished, select “Go to resource”
* Click on “Show firewall settings” on the right hand side.
* Switch this setting to on.
* Click Save.
* Click okay.
   
   

# Create SQL Database

* In Azure, search for “SQL databases”
* Select “SQL databases”
* Click “Create SQL database”
* Choose your Subscription, Resource group, Database name and Server. No Elastic pool is needed. * * * Click “Configure Database”
* Choose a database size that suites you. For this tutorial, we use Basic with 10 DTUs and a max size of 2GB
* Click Apply
* Click “Review + create”
* Click “Create”
* Wait for the deployment to complete and select “Go to resource”.
* Select “Show database connection strings” on the right hand side
* Copy the connection string.
 
 
**Back in Visual Studio**
* In the publish dialog, select the publish profile and click “Edit”
* Paste the Connection String into Settings -> Databases -> “DefaultConnection”. Insert your password.
* Make sure that at the beginning of the connection string, you replace “Server=tcp:” with “Data Source=”

The final connection string should be something like this:
Data Source=blazor-boilerplate-demo.database.windows.net,1433;Initial Catalog=blazor_boilerplate;Persist Security Info=False;User ID=bbhostinggjhjghGJHJHG;Password=dshkuifsdfkhj//()(/&(/565zt;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
 
* Copy and paste your resulting connection string into Settings -> Entity Framework Migrations for all connection strings
 
 
 
 
# Create Key Vault

* Switch to Azure. Search for “Key vaults”
* Choose „Key vaults“
* Click „Create key vault“
* Select your subscription and Resource group. Choose a name, Region and Pricing tier. 
* Click “Review + create”
* Click “Create”

* After the deployment finished, select “Go to resource”.
* In the Key vault, select “Certificates”
* Click „Generate/Import“.
* Choose to Import a certificate.
* Select your Certificate for Identity Server to use. You can use the default “AuthSample.pfx”. The certificate needs to have a private key marked as exportable.
* Select your certificate and click “Open”.
* Choose a name for the certificate and enter the password. For AuthSample.pfx, it is “Admin123”.
* Click „Create“
* Verify that the certificate has been imported correctly.

**Issuance Policy**
* Click on the certificate in the list. 
* Click “Issuance Policy”
* Click “Advanced Policy Configuration”
* Verify that the private key is marked as exportable.

**Access Policy**
* Click on your key vault on the top left.
* On the menu at the left hand side, choose “Access policies”
* Click „Add Access Policy“
* Select the „Get“ permission on “Secret permissions”. As Identity Server needs access to the private key, we will import the certificate as a secret – not as a certificate.
* Click on “Select principal”
* Search for your app service by entering it’s name.
* Click on the App Service.
* It will now appear on the bottom list.
* Click “Select”.
* Verify that your app service now appears in “Select principal”.
* Click “Add”.
* Click „Save“.
 

# Use Key Vault in appsettings.json

In the key vault, copy the DNS Name displayed on the right hand side.
Back in Visual Studio, open appsettings.json andin the "HostingOnAzure" Section:

* Change "RunsOnAzure" to true
* Paste the Vault URL at “Vault URI”. 
* Fill in your Certificate name.
* Change “UseLocalCertStore” to false.



# Publish
Now publish your application. Once publishing is complete, it should automatically be launched inside a browser. 
 
 
 
# Troubleshooting

**Using Kudu**
For troubleshooting you can use an Azure Service called Kudu, to access your application’s log files.

**Using Visual Studio**

You can also access log files through Visual Studio integrated tools.