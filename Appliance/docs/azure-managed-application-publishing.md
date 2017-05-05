# How to create a Managed Application 
This article explains how an ISV or partner can create/publish a Managed Application which can be consumed by customers. The key pieces involved include an appliance package which contains the template files, creating which user, group or application will have access to the resource group in customer subscription and finally creating the appliance definition. [TODO: change this overview section]


## Creating an Appliance Definition

The publisher has to publish an appliance definition which will be consumed when creating an appliance. There are two ways an appliance can be created. First is using an appliance definition and the second is by using a plan.

Below are the steps required to create an appliance definition which the publisher of an appliance will follow.

### Create appliance package

The first step is to create the managed application package which contains the main template files. The publisher or ISV will have to create four files. The first file is called applianceMainTemplate.json. This template file defines the actual resources that will be provisioned as part of the managed application. For example, if the managed application need to create a virtual machine and a storage account, these resources will be defined in the applianceMainTemplate.json. The second file that the publisher needs to create is the mainTemplate.json. This is the template file which contains only the managed application resource (Microsoft.Solutions/appliances). It also contains all the parameters that are needed for the resources in the applianceMainTemplate.json. Please refer to the sample here. 

The package also contains a createUiDefinition.json. This section will describe how to create UI definition file, what needs to go in applianceMainTemplate.json and mainTemplate.json etc. [TODO]

Once all the needed files are ready, you will upload the package to an accessible location from where it can be consumed.

Sample applianceMainTemplate.json:

    {
    	"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    	"contentVersion": "1.0.0.0",
    	"parameters": {
    	},
    	"variables": {
    		"storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",
    	},
    	"resources": [{
    		"type": "Microsoft.Storage/storageAccounts",
    		"name": "[variables('storageAccountName')]",
    		"apiVersion": "2016-01-01",
    		"location": "westus",
    		"sku": {
    			"name": "Standard_LRS"
    		},
    		"kind": "Storage",
    		"properties": {
    			
    		}
    	},
    	],
    	"outputs": {
    		
    	}
    }

Sample mainTemplate.json:

	    {
      		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      		"contentVersion": "1.0.0.0",
      		"parameters": {
    			"storageAccountName": {
      				"defaultValue": "nirajgsa",
      				"type": "String"
    			},
       		},
      		"variables": {
    			"applianceInfraResourceGroupId": "[concat(resourceGroup().id,uniquestring(resourceGroup().id))]"
      		},
      		"resources": [{
      			"type": "Microsoft.Solutions/appliances",
      			"name": "[parameters('storageAccountName')]",
      			"apiVersion": "2016-09-01-preview",
      			"location": "[resourceGroup().location]",
      			"kind": "ServiceCatalog",
      				"properties": {
    					"ManagedResourceGroupId": "[variables('applianceinfraresourcegroupId')]",
    					"applianceDefinitionId":  "/subscriptions/d05f4e58-b80d-4ebd-a6d7-c9cac216cd39/resourceGroups/ApplianceDefinitions/providers/Microsoft.Solutions/applianceDefinitions/nirajgss07",
    					"Parameters": {
     
      					}
    				}
      		}]
     	 }

Sample createUIDefinition.json:


### Create Azure AD User group or Application
Next create a user group or application that you want to use to manage the resources on behalf of the customer. This user group or application will have permissions on the managed resource group as described by the role. The role could be any built-in RBAC role like "Owner", "Contributor" etc. To find more about built in roles, please see here. An individual user can also be given permissions to manage the resources but it is recommended to use a user group. For more information on Active directory users/groups please see here. [TODO: ADD a fwlink to AD article]. To create a new active directory user group, use -

`az ad group create -n `

You can also use an existing group. You will need the object Id of the newly created or an existing user group. Below is how you can get the object ID if you know the display name that was used for creating the group.

`az ad group show --display-name <name>`

Example -

`az ad group show --display-name ravAppliancetestADgroup`

This will return the following -
 
    {
    "displayName": "ravAppliancetestADgroup",
    "mail": null,
    "objectId": "9aabd3ad-3716-4242-9d8e-a85df479d5d9",
    "objectType": "Group",
    "securityEnabled": true
    }
    
You will need the objectId value from above. 

### Get the Role Definition ID

Next, you will need the role definition ID of the RBAC built-in role which you want to grant access to the above user, user group or application. Typically you would want to use the "Owner" or "Contributor" or "Reader" role. Below command shows how to get the role definition ID for the "Owner" role. 

    az role definition list --name owner

This will return the following - 

    {
    "id": "/subscriptions/78814224-3c2d-4932-9fe3-913da0f278ee/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "properties": {
      "assignableScopes": [
    "/"
      ],
      "description": "Lets you manage everything, including access to resources.",
      "permissions": [
    {
      "actions": [
    "*"
      ],
      "notActions": []
    }
      ],
      "roleName": "Owner",
      "type": "BuiltInRole"
    },
    "type": "Microsoft.Authorization/roleDefinitions"
      }

You will need the value of the "name" property from above.


### Create the appliance definition

Once you have created the appliance package and the authorizations, you can create the appliance definition using the below command. 

      az managedapp definition create -n ravtestAppDef4 -l "Brazil" 
		--resource-group ravApplianceDefRG3 --lock-level None 
		--display-name ravtestappdef --description ravtestdescription  
		--authorizations "9aabd3ad-3716-4242-9d8e-a85df479d5d9:8e3af657-a8ff-443c-a75c-2fe8c4bcb635" 
		--package-file-uri "https://wud.blob.core.windows.net/appliance/SingleStorageAccount.zip" --debug 
    	
The description of the parameters used above is as follows:

--resource-group - This is name of the resource group where the appliance definition will be created.

--lock-level - There will be lock placed on the resource group. The supported values as as follows [TODO]

--authorizations - This describes the principalID and the role definition ID which will be used for granting permission to the managed resource group. It is specified in the format of <principalId>:<roleDefinitionId>. Multiple values can also be specified for this. If multiple values are needed, it should be specified in this form - "<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>". Multiple values are separated by a space.

--package-file-uri -  This points to the location of the appliance package which contains the template files. 

