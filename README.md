# dynamictypes-vCenterDrsRules.package

This vRealize Orchestrator package creates a Dynamic Type (DT) for the management of vSphere Cluster DRS VM Affinity, Anti-Affinity rules and VMGroup to HostGroup Affinity and Anti-Affinity rules.  Once the DT namespace created, you can use the provided workflows to create a custom resource within vRealize Automation – Cloud Assembly to manage the DRS rules in a Cloud Template.
 

Requirements:
Running Instance of vRealize Automation 8.x
vRealize Orchestrator with the vSphere Plugin configured with vCenter Endpoints that you wish to manage the Cluster DRS Rules

________________________________________
Content:
Location: Workflows/custom/vCenter/drs/dynamicType

createDrsRule – DT
Used to create the Custom Resource in vRA
Creates DRS Rule with the provided inputs
Returns DynamicTypes:vcClustersDrsRules.drsRuleSet

updateDrsRule – DT
Used to update the Custom Resource in vRA
Update an existing DRS Rule with the provided inputs
Returns DynamicTypes:vcClustersDrsRules.drsRuleSet

deleteDrsRule – DT
Used to detroy the Custom Resource in vRA
Destroys the provided DRS Rule set

Location: Actions/custom.vmware.vc.vm
findVirtualMachineByName

Location: Actions/custom.vmware.vc.cluster
getVcClusterObjectBySdkId
addVmsToDrsRule
findClusterDrsRuleByName
createNewDrsRule
deleteDrsRuleByName
addVmsToDrsVMGroup
createNewDrsVMGroup
deleteDrsVMGroup
getAllDRSHostGroupPerCluster
getDRSHostGroupPerClusterByName
getDrsVmGroupVmNames

Location: Actions/custom.vmware.vc.dt.drsRules
createDynamicTypeDrsRule
createDynamicTypeDrsVcCluster
createDynamicTypeDrsVcServers
findAllDrsRuleSetsDrsRulesDT
findAllVcClustersDrsRulesDT
findAllVcServersDrsRulesDT
findDrsRulesetByIdDrsRulesDT
findRelationsDrsRulesDT
findVcClusterByIdDrsRulesDT
findVcServerByIdDrsRulesDT
hasChildrenDrsRulesDT

Resource Elements:
Dynamic Type Configuration:
9877eaa4-6a14-4009-a7e0-4d34deff7550.txt-3-Nov-2021-17-57-02-GMT
vCenterServer.png
ServersCluster.png
Folder-16x16.png
drsRules.png
________________________________________

Installation instructions:
1.	Import dynamictypes-vCenterDrsRules.package in vRealize Orchestrator:
Ref. https://docs.vmware.com/en/vRealize-Orchestrator/8.6/com.vmware.vrealize.orchestrator-using-client-guide.doc/GUID-CE405299-1800-40C0-9054-5C032A5B022C.html
2.	Import the Dynamic Type configuration from this vRO Package by running the following workflow: “/Library/Dynamic Types/Configuration/Import Configuration From Package”
3.	Validate that the dynamic Type is working by going browsing the existing vCenter Cluster DRS rules under ‘Inventory/Dynamic Types/vcClusterDrsRules/vCenterServers’
Example:
	 
 
4.	Create the vRealize Automation - Cloud Assembly Custom Resource with the following parameters:
Ref. https://docs.vmware.com/en/vRealize-Automation/8.6/Using-and-Managing-Cloud-Assembly/GUID-9722DEAD-CDCC-4E66-8357-A17B0E0CF783.html

Name	vCenterClusterDrsRuleSet

Description	Custom Resource to create / update and delete vCenter Cluster DRS Affinity/Anti-Affinity RuleSets
Resource Type	Custom.vCenterClusterDrsRuleSet
Activate	Enabled / True
Based on	vRO Inventory
Create	CreateDrsRule - DT
Update	updateDrsRule - DT
Destroy	deleteDrsRule - DT

________________________________________

Using the custom resource:
Open a Cloud template and drag the resource “Custom.vCenterClusterDrsRuleSet” on the canvas.  You need to provide the following inputs for the Custom Resource:
Name:  ruleType 
Required: True
Type: String
Description: Dropdown list to select Drs Rule Type
Allowed Values: Anti-Affinity, Affinity,VMGroup-Host-Affinty,VMGroup-Host-Anti-Affinity
Name: ruleName
Required: True
Type: String
Description: Name the rule will have in vCenter
Name: vms
Required: True
Type: Array of Strings
Description: List of virtual machine names to be include in the rule

Name: hostGroupName
Required: Only when using the rule types VMGroup-Host-Affinty or VMGroup-Host-Anti-Affinity
Type: String
Description:   Name of an Existing Host Group that is defined in the Cluster VM/HostGroup

Name: vmGroupName
Required: Only when using the rule types VMGroup-Host-Affinty or VMGroup-Host-Anti-Affinity
Type: String
Description: Name the VMGroup that will be created under the Cluster VM/Host Group section for the VM-Host Affinity or Anti-Affinity 
Example: 
 

YAML Example:
drsRuleSet_webServer1:

    type: Custom.vCenterClusterDrsRuleSet

    dependsOn:

      - webServer1

    properties:

      ruleType: Anti-Affinity

      ruleName: '${"AA-DrsRule-" + env.deploymentId}'

      vms: '${resource.webServer1[*].resourceName}'

webServer1:

    type: Cloud.Machine

    properties:

      image: template-ubuntu

      flavor: linux-small

      customizationSpec: linux01

      count: '${input.serverCount}'

      networks:

        - network: '${resource.webApps.id}'
