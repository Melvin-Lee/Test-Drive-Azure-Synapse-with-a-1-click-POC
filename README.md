## Azure Synapse 1-click POC environment with pre-populated dataset, pipeline, notebook
This 1-click deployment allows the user to deploy a Proof-of-Concept environment of Azure Synapse Analytics with dataset (New York Taxi Trips & Fares data), pipeline to (ingest, merge, aggregate), 	notebook (Spark ML prediction)

## Prerequisites

Owner role (or Contributor roles) for the Azure Subscription the template being deployed in. This is for creation of a separate Proof-of-Concept Resource Group and to delegate roles necessary for this proof of concept. Refer to this [official documentation](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-steps) for RBAC role-assignments.

## Deployment Steps
1. Fork out [this github repository](https://github.com/Azure/Test-Drive-Azure-Synapse-with-a-1-click-POC) into your github account. 
    
   **If you don't fork repo:** 
   + **The pre-populated dataset, pipeline and notebook will not be deployed**
   + **You will get a Github publishing error**
   
   
  <!--  ![Fork](https://raw.githubusercontent.com/Azure/Test-Drive-Azure-Synapse-with-a-1-click-POC/main/images/4.gif) -->
 
2. Click 'Deploy To Azure' button given below to deploy all the resources.

    [![Deploy To Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FTest-Drive-Azure-Synapse-with-a-1-click-POC%2Fmain%2Fazuredeploy.json)

   - Provide the values for:

     - Resource group (create new)
     - Region
     - Company Tla
     - Option (true or false) for Allow All Connections
     - Option (true or false) for Spark Deployment
     - Spark Node Size (Small, Medium, large) if Spark Deployment is set to true
     - Sql Administrator Login
     - Sql Administrator Login Password
     - Sku
     - Option (true or false) for Metadata Sync
     - Frequency
     - Time Zone
     - Resume Time
     - Pause Time
     - Option (Enabled or Disabled) for Transparent Data Encryption
     - Github Username (username for the account where [this github repository](https://github.com/Azure/Test-Drive-Azure-Synapse-with-a-1-click-POC) was forked out into)

   - Click 'Review + Create'.
   - On successful validation, click 'Create'.

## Azure Services being deployed
This template deploys necessary resources to run an Azure Synapse Proof-of-Concept. 
Following resources are deployed with this template along with some RBAC role assignments:

- An Azure Synapse Workspace 
- An Azure Synapse SQL Pool
- An optional Apache Spark Pool
- Azure Data Lake Storage Gen2 account
- A new File System inside the Storage Account to be used by Azure Synapse
- A Logic App to Pause the SQL Pool at defined schedule
- A Logic App to Resume the SQL Pool at defined schedule
- A key vault to store the secrets

<!-- The data pipeline inside the Synapse Workspace gets New York Taxi trip and fare data, joins them and perform aggregations on them to give the final aggregated results. Other resources include datasets, linked services and dataflows. All resources are completely parameterized and all the secrets are stored in the key vault. These secrets are fetched inside the linked services using key vault linked service. The Logic App will check for Active Queries. If there are active queries, it will wait 5 minutes and check again until there are none before pausing -->

## Post Deployment
- Current Azure user needs to have ["Storage Blob Data Contributor" role access](https://docs.microsoft.com/en-us/azure/synapse-analytics/get-started-add-admin#azure-rbac-role-assignments-on-the-workspaces-primary-storage-account) to recently created Azure Data Lake Storage Gen2 account to avoid 403 type permission errors.
- After the deployment is complete, click 'Go to resource group'.
- You'll see all the resources deployed in the resource group.
- Click on the newly deployed Synapse workspace.
- Click on link 'Open' inside the box labelled as 'Open Synapse Studio'.
- Click on 'Log into Github' after workspace is opened. Provide your credentials for the github account holding the forked out repository.
- After logging in into your github account, click on 'Integrate' icon in the left panel. A blade will appear from right side of the screen.
- Make sure that 'main' branch is selected as 'Working branch' and click 'Save'.

![PostDeployment-1](https://raw.githubusercontent.com/Azure/Test-Drive-Azure-Synapse-with-a-1-click-POC/main/images/1.gif)

- Now open the pipeline named 'TripFaresDataPipeline'.
- Click on 'Parameters' tab at bottom of the window.
- Update the following parameter values. ___(You can copy the resource names from the resource group recently deployed.)___
    - SynapseWorkspaceName  (Make sure workspace name is fully qualified domain name, i.e. workspaceName.database.windows.net)
    - SQLDedicatedPoolName
    - SQLLoginUsername
    - KeyVaultName
    - DatalakeAccountName

![PostDeployment-2](https://raw.githubusercontent.com/Azure/Test-Drive-Azure-Synapse-with-a-1-click-POC/main/images/2.gif)

- After the parameters are updated, click on 'Commit all'.
- After successful commit, click 'Publish'. A blade will appear from right side of the window.
- Click 'Ok'.

![PostDeployment-3](https://raw.githubusercontent.com/Azure/Test-Drive-Azure-Synapse-with-a-1-click-POC/main/images/3.gif)

- Now to trigger the pipeline, click 'Add trigger' at the top panel and click 'Trigger now'.
- Confirm the pipeline parameters' values and click 'Ok'.
- You can check the pipeline status under 'Pipeline runs' in the 'Monitor' tab on the left panel.

![PostDeployment-4](https://raw.githubusercontent.com/Azure/Test-Drive-Azure-Synapse-with-a-1-click-POC/main/images/5.gif)

- To run the notebook (if spark pool is deployed), click on 'Develop' tab on the left panel.
- Now under 'Notebooks' dropdown on left side of screen, click the notebook named 'Data Exploration and ML Modeling - NYC taxi predict using Spark MLlib'.
- Click 'Run all' to run the notebook. (It might take a few minutes to start the session)

![PostDeployment-5](https://raw.githubusercontent.com/Azure/Test-Drive-Azure-Synapse-with-a-1-click-POC/main/images/6.gif)

- Once published all the resources will now be available in the live mode.
- To switch to the live mode from git mode, click the drop down at top left corner and select 'Switch to live mode'.

![PostDeployment-6](https://raw.githubusercontent.com/Azure/Test-Drive-Azure-Synapse-with-a-1-click-POC/main/images/liveMode.PNG)

## Steps for PowerBI integration

**Pre-requisites**

PowerBI workspace created. Please note that you can’t use default workspace (‘My workspace’). create a new PBI workspace or use any other workspace other than ‘My workspace’.

Create PowerBI workspace --> https://docs.microsoft.com/en-us/power-bi/collaborate-share/service-create-the-new-workspaces

**Link Azure Synapse workspace to PowerBI workspace**

- Go to Manage --> Linked Services.
- Click on PowerBIWorkspaceTripsFares linked service
    ![image](https://user-images.githubusercontent.com/47899900/130508485-fb5a0d94-561d-4fa0-bd6b-6a079306d211.png)
- From the drop down list, select your PowerBI workspace and Save and publish.

    ![image](https://user-images.githubusercontent.com/47899900/130509536-f55a255e-f635-4272-8abf-03463ac9a8df.png)



- Download [NYCTaxiCabTripAndFare.pbit] (https://github.com/venushaa/Test-Drive-Azure-Synapse-with-a-1-click-POC/blob/main/synapsepoc/PowerBITemplate/NYCTaxiCabTripAndFare.pbit) from PowerBITemplate folder
- Provide Server and login credentials.
- Server name and DB name can be found in connection strings.
![image](https://user-images.githubusercontent.com/47899900/130510699-be974053-579f-4e9d-b8e8-011f1dbd3bb7.png)
- Publish the dashboard to the PowerBI workspace you have created.
- Go to Develop --> PowerBI --> Refresh.
- You see the PowerBI report in Synapse you had published in PowerBI workspace.

    ![image](https://user-images.githubusercontent.com/47899900/130295705-07d6e054-92bf-4079-bb6f-ba28b0e4b8f9.png)

