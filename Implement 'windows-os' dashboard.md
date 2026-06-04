Implement 'windows-os' dashboard

1. Filter dashboard include "date" (filter range only 1 month ago), "resource name" (multiple selected)

2. Widgets: common setting request body, response,... for all widgets

Common request body:
{
  "startDate": "2026-04-22",
  "endDate": "2026-05-21",
  "resourceNames": [],
  "subscriptionIds": [],
  "operatingSystems": [],
  "certificateNames": [],
  "diskNames": [],
  "interfaceNames": []
}

Common response:
{
  "data": [],
  "totalCount": 0
}

3. Detail:

_Widget name: "Total Resources"
Type: StatWidgetComponent
Insight: display total number of Windows resources
Request body: common request body
Response:
{
  "totalResources": 330
}
Dataset: single value: total resources
Click data: none

_Widget name: "In Critical Status"
Type: StatWidgetComponent
Insight: display total resources in critical status
Request body: common request body
Response:
{
  "criticalStatusCount": 0
}
Dataset: single value: critical status count
Click data: status

_Widget name: "In Error Status"
Type: StatWidgetComponent
Insight: display total resources in error status
Request body: common request body
Response:
{
  "errorStatusCount": 0
}
Dataset: single value: error status count
Click data: status

_Widget name: "In Warning Status"
Type: StatWidgetComponent
Insight: display total resources in warning status
Request body: common request body
Response:
{
  "warningStatusCount": 0
}
Dataset: single value: warning status count
Click data: status

_Widget name: "In Normal Status"
Type: StatWidgetComponent
Insight: display total resources in normal status
Request body: common request body
Response:
{
  "normalStatusCount": 330
}
Dataset: single value: normal status count
Click data: status

_Widget name: "Resource Alert Status"
Type: table
Insight: display alert status and severity by Windows resource
Request body: common request body
Response:
{
  "data": [
    {
      "resourceName": "SERVER01",
      "severity": "Normal"
    }
  ]
}
Dataset: columns: resource name, severity
Click data: resource name

_Widget name: "Windows Operating Systems"
Type: PieWidgetComponent
Insight: display Windows resources split by operating system version
Request body: common request body
Response:
{
  "data": [
    {
      "operatingSystem": "Microsoft Windows Server",
      "count": 189,
      "percentage": 57.33
    }
  ]
}
Dataset: value: count or percentage, legend: operating system
Click data: operating system

_Widget name: "Windows Disks by Total Operations"
Type: BarChartComponent
Insight: display top Windows disks by total disk operations
Request body: common request body
Response:
{
  "data": [
    {
      "resourceName": "SQLFRAP...",
      "totalOperations": 4.4
    }
  ]
}
Dataset: axis y: resource name, axis x: total operations
Click data: resource name

_Widget name: "SSL Certificate Expiry Time"
Type: table
Insight: display SSL certificate expiry and remaining days by resource
Request body: common request body group by resource name
Response:
{
  "data": [
    {
      "resourceName": "SWPAVAR2",
      "certificateName": "SSL Certificates AMQP",
      "expiredDays": null,
      "remainingDays": 1590
    }
  ]
}
Dataset: columns: resource name, expired days, remaining days
Click data: level 0: resource name, level 1: certificate name
drill down data:
+request body: add resourceNames filter with selected resource name and group by certificate name
+response:
{
  "data": [
	{
	  "certificateName": "SSL Certificates AMQP",
	  "expiredDays": null,
	  "remainingDays": 1550
	},
	{
	  "certificateName": "SSL Certificates HTTPS",
	  "expiredDays": null,
	  "remainingDays": 40
	}
  ]
}

_Widget name: "Windows Resources by Total Disk IO"
Type: table
Insight: display disk read and disk write IOPS by Windows resource and physical disk
Request body: common request body group by resource name
Response:
{
  "data": [
    {
      "resourceName": "FILPPOLMGTW001",
      "diskReadPerSecIOPS": 279.44,
      "diskWritePerSecIOPS": 15.10
    }
  ]
}
Dataset: columns: resource name, disk read per sec IOPS, disk write per sec IOPS
Click data: level 0: resource name, level 1: disk name
drill down data:
+request body: add resourceNames filter with selected resource name and group by disk name
+response:
{
  "data": [
	{
	  "diskName": "Physical Disks-0 C:",
	  "diskReadPerSecIOPS": 159.29,
	  "diskWritePerSecIOPS": 4.85
	},
	{
	  "diskName": "Physical Disks-1 D:",
	  "diskReadPerSecIOPS": 120.15,
	  "diskWritePerSecIOPS": 10.25
	}
  ]
}

_Widget name: "Windows Interfaces Throughput Statistics Real-time"
Type: table
Insight: display real-time inbound and outbound network throughput by Windows resource
Request body: common request body group by resource name
Response:
{
  "data": [
    {
      "resourceName": "SERVER01",
      "inboundMbps": 12.5,
      "outboundMbps": 8.2
    }
  ]
}
Dataset: columns: resource name, inbound Mbps, outbound Mbps
Click data: level 0: resource name, level 1: interface name
drill down data:
+request body: add resourceNames filter with selected resource name and group by interface name
+response:
{
  "data": [
    {
      "interfaceName": "Ethernet0",
      "inboundMbps": 10.5,
      "outboundMbps": 7.4
    }
  ]
}

_Widget name: "Windows Resources by Volume Utilization Real-time"
Type: table
Insight: display real-time volume utilization percentage by Windows resource
Request body: common request body group by resource name
Response:
{
  "data": [
    {
      "resourceName": "JMPFRAMGTW001",
      "volumeUtilizationPercent": 85.99
    }
  ]
}
Dataset: columns: resource name, volume utilization percent
Click data: level 0: resource name, level 1: disk name
drill down data:
+request body: add resourceNames by selected resource filter and group by disk name
+response:
{
  "data": [
    {
      "diskName": "Physical Disks-0 C:",
      "volumeUtilizationPercent": 85.99
    },
    {
      "diskName": "Physical Disks-1 D:",
      "volumeUtilizationPercent": 75.42
    }
  ]
}

_Widget name: "Windows Network Interfaces by Bandwidth"
Type: table
Insight: display inbound and outbound bandwidth by Windows network interface
Request body: common request body group by resource name
Response:
{
  "data": [
    {
      "resourceName": "SERVER01",
      "inboundMbps": 10.5,
      "outboundMbps": 7.4
    }
  ]
}
Dataset: columns: resource name, inbound Mbps, outbound Mbps
Click data: level 0: resource name, level 1: interface name
drill down data:
+request body: add resourceNames filter with selected resource name and group by interface name
+response:
{
  "data": [
    {
      "interfaceName": "Ethernet0",
      "inboundMbps": 10.5,
      "outboundMbps": 7.4
    }
  ]
}

_Widget name: "Windows Memory % Utilization"
Type: LineChartComponent
Insight: display memory utilization trend by Windows resource over time
Request body: common request body
Response:
{
  "data": [
    {
      "resourceName": "ADCFRA...",
      "timestamp": "2026-05-01T00:00:00Z",
      "memoryUtilizationPercent": 68.2
    }
  ]
}
Dataset: axis y: memory utilization percent, axis x: time, series: resource name
Click data: timestamp, resource name

_Widget name: "Windows Resources by Memory Utilization Real-time"
Type: table
Insight: display real-time memory utilization percentage by Windows resource
Request body: common request body group by resource name
Response:
{
  "data": [
    {
      "resourceName": "127.0.0.1_collector_355",
      "memoryUtilizationPercent": 68.20
    }
  ]
}
Dataset: columns: resource name, memory utilization percent
Click data: resource name
drill down data: None

_Widget name: "Windows CPU % Utilization"
Type: LineChartComponent
Insight: display CPU utilization trend by Windows resource over time
Request body: common request body
Response:
{
  "data": [
    {
      "resourceName": "ADCFRA...",
      "timestamp": "2026-05-01T00:00:00Z",
      "cpuUtilizationPercent": 10.7
    }
  ]
}
Dataset: axis y: CPU utilization percent, axis x: time, series: resource name
Click data: timestamp, resource name

_Widget name: "Windows Resources by CPU Utilization Real-time"
Type: table
Insight: display real-time CPU utilization percentage by Windows resource
Request body: common request body group by resource name
Response:
{
  "data": [
    {
      "resourceName": "127.0.0.1_collector_355",
      "cpuUtilizationPercent": 10.70
    }
  ]
}
Dataset: columns: resource name, CPU utilization percent
Click data: resource name
drill down data: None