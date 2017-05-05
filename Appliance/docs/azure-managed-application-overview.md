# Azure Managed Application 

## Overview

Today Azure provides a robust Marketplace where ISVs and start ups can offer their solutions to customers around the world. Azure Marketplace is a gallery which consists of hundreds of complex, multi-resource templates from a number of first and third party vendors. Customers can within minutes deploy and start using PaaS and SaaS applications. Although, it provides a great way to quickly deploy and start using an offering, the ongoing maintenance and updates to the application is still a responsibility of the customer. Moreover, from a vendor's perspective, there is no way to charge customers for use of an application beyond the VM-image billing. Furthermore, vendors have no way of preventing customers from tampering with application resources, nor of preventing access to intellectual property that makes up an application. This is where Azure managed Applications come in. 

Azure Managed applications provides an ecosystem which allows vendors to make a PaaS or SaaS service available as a self-contained application that customers can deploy themselves in their subscription but can be managed by the partner/vendor itself. It enables vendors to bill customers using Azure’s billing system, use templates to manage the lifecycle of deployed applications. On the other side, it allows customers to automatically acquire updates and pay for support and maintenance. They do not have to maintain or update the application themselves or diagnose and fix issues when the application fails.

Such an ecosystem in Azure would not only benefit PaaS and SaaS vendors but also corporate central platform teams and System Integrators that wish to package and resell their solutions.

## How does Managed Application work?
As stated above, there are two sides to this complete experience. On one side is the vendor/ISV who would like to create a managed application and make it available for broader use. On the other side is the customer or consumer who wishes to create and use this application. In this article, we will cover both sides of the experience. Before that, lets understand how a managed application works. 

Managed Application is very similar to a marketplace solution template. The key difference is that the resources are provisioned in a resource group which is managed by the ISV/vendor. The resource group is present in the customer subscription but a user/user group/application in the ISV tenant has access to this resource group. The access could be an Active Directory Owner, Contributor, Reader or any other built-in role that will enable ISV to manage and service the application. All these concepts and how they will be used are further explained in the section below. 

See figure below [TODO]

## Key Concepts

### ManagedBy Resource Group
This is the resource group where all the azure resources being provisioned in the template will be created. So, for example, if the appliance is creating a storage account, this resource group will contain the storage account resource. It will not contain the appliance resource.

### Appliance package
The publisher creates a package which contains the template files and the createUIDefinition file. Specifically it contains the following files-

**applianceMainTemplate.json** - This is the template file which defines all the resources that will be provisioned by the appliance. This is a regular template file which is used for creating resources.

**MainTemplate.json** - This is template file which defines the appliance resource (Microsoft.Solutions/appliances). One key property defined in this resource is the ManagedResourceGroupId. This property indicates which resource group will be used for hosting the actual resources defined in the applianceMainTemplate.json.

**createUIDefinition.json** - This file describes how the UI needed for the parameters defined in the template will be rendered. [TODO: ELABORATE ON THIS]

### Authorization
The publisher needs to specify the permissions that he will need to manage the resources on behalf of the customer. This permission is give to the Managed By resource group. The permission will be given to a user, user group or application.

**PrincipalID** - This is the Azure AD identifier of the user, group or application which will be used to grant access to the Managed Resource Group. This belongs to the publisher's tenant.

**RoleDefinitionID** - This is the Azure AD identifier of the role assigned to the Principal ID above. It could be any of the built-in RBAC roles in the publisher's tenant.


### Create the appliance

The final step is to create the appliance. 

	az appliance create -n ravtestappliance3 -l "westcentralus" 
		--appliance-definition-id "/subscriptions/78814224-3c2d-4932-9fe3-913da0f278ee/resourceGroups/ravApplianceRG3/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppdef3" 
		--managed-rg-id "/subscriptions/78814224-3c2d-4932-9fe3-913da0f278ee/resourceGroups ravApplianceManagedCustRG3" 
		--rg-name ravApplianceCustRG3

--appliance-definition-Id -  This is the resource Id of the appliance definition that was created in the above step. To get this id, you can run the following command -

    az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2

This will return the appliance definition and you will need the value of "Id" property.

--managed-rg-id - This is name of the resource group where all the resources defined in the applianceMainTemplate.json will be created. This is the managedBy resource group. This resource group will be managed by the publisher. If it does not exist, it will be created for you.

--rg-name - This is resource group where the appliance resource will be created/deployed. The Microsoft.Solutions/appliance resource will live in this resource group. 
