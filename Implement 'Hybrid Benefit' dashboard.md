Implement 'Hybrid Benefit' dashboard

1. Filter dashboard include:
- OS Type
- Resource Group
- Subscription Name
- Tag

2. Widgets: common setting request body, response, dataset, click data.
Common request body:
{
  "osType": [],
  "resourceGroups": [],
  "subscriptionNames": [],
  "tags": [],
  "startDate": null,
  "endDate": null,
  "groupBy": []
}

3. Detail:

_Widget name: Allocated AHB Cores
Type: StatWidgetComponent
Insight: display total allocated Azure Hybrid Benefit cores currently used across all selected resources
Request body: common request body
Response:
{
  "allocatedAhbCores": 49000
}
Dataset:
- value: allocated AHB cores
- display format: compact number, example "49K"
Click data:
- none

_Widget name: Hybrid Benefit Description
Type: custom description hard code template
Insight: display explanation text about Azure Hybrid Benefit and how it helps migrate and save on Windows Server or SQL Server licenses
API CALL: none, static content
Template: contant description "
Azure Hybrid Benefit is a licensing offer that helps you migrate and save to Azure. To applythis benefit you must be paying for either.
. Windows Server or SQL Server core licenses with Software Assurance or a subscription to these products.
. An active Linux subscription, including Red Hat Enterprise Linuxor SUSE LinuxEnterprise Server running in Azure.
Byusing Azure Hybrid Benefit, you can achieve cost savings, m odernize and m aintain a 1exible hybrid environm ent while optimizing business applications.
"
Click event:
- documentation url in text
+"migrate and save" link "https://azure.microsoft.com/en-us/benefits/hybrid-benefit/"
+"Software Assurance" link "https://docs.microsoft.com/en-us/windows-server/get-started/windows-server-licensing-and-pricing"

_Widget name: Hybrid Benefit Summary
Type: Table not expandable
Insight: display summarized Azure Hybrid Benefit usage by OS type, including whether the resource is using AHB, total instances, VM cores and AHB cores
Request body: common request body
Response:
{
  "items": [
    {
      "osType": "Windows Server BYOL",
      "isAHB": true,
      "instances": 10237,
      "vmCores": null,
      "ahbCores": 48576
    },
    {
      "osType": "Canonical",
      "isAHB": false,
      "instances": 45494,
      "vmCores": null,
      "ahbCores": null
    }
  ]
}
Dataset:
- columns: OS Type, IsAHB, Instances, VMCores, AHB Cores
- group by: OS Type
Click data:
- osType
Expand row click data: none

_Widget name: AHB Cores by Date and OS Type
Type: LineChartComponent
Insight: display AHB cores trend over time by OS type
Request body: common request body group by date and osType
Response:
{
  "data": [
    {
      "osType": "Windows Server BYOL",
      "date": "2026-01-10",
      "ahbCores": 1700
    }
  ]
}
Dataset:
- axis x: date
- axis y: AHB cores
- series: OS Type
Click data:
- date
- osType

_Widget name: Hybrid Benefit Detail
Type: TableWidgetComponent
Insight: display detailed resource-level Azure Hybrid Benefit usage, including resource group, resource name, OS type, AHB flag, VMSS flag, instances, VM cores and AHB cores
Request body: common request body group by resource group
Response:
{
  "data": [
    {
      "resourceGroup": "rg-cemar-monolith-prod-arz-001",
      "resourceName": "vmss-cemar-public-web-prod-arz-001",
      "osType": "Windows Server BYOL",
      "isAHB": true,
      "vmssFlag": "VMSS",
      "instances": 55,
      "vmCores": null,
      "ahbCores": 440
    }
  ]
}
Dataset:
- columns: Resource Group, Resource Name, OS Type, IsAHB, VMSSFlag, Instances, VMCores, AHB Cores
Click data:
- resourceGroup