Implement 'network-overview' dashboard

1. Filter dashboard include "date" (date range), "resource name" (multiple selected)

2. Widgets: common setting request body, response,... for all widgets

Common request body:
{
  "startDate": "2026-04-25",
  "endDate": "2026-05-24",
  "resourceTypes": [],
  "resourceNames": []
}

Common response:
{
  "data": [],
  "totalCount": 0
}

3. Detail:

_Widget name: "Routers"
Type: StatWidgetComponent
Insight: display total number of Router devices
Request body: common request body
Response:
{
  "routerCount": 0
}
Dataset: single value: router count
Click data: none

_Widget name: "Switches"
Type: StatWidgetComponent
Insight: display total number of Switch devices
Request body: common request body
Response:
{
  "switchCount": 15
}
Dataset: single value: switch count
Click data: none

_Widget name: "Access Points"
Type: StatWidgetComponent
Insight: display total number of Access Point devices
Request body: common request body
Response:
{
  "accessPointCount": 0
}
Dataset: single value: access point count
Click data: none

_Widget name: "Collectors"
Type: StatWidgetComponent
Insight: display total number of Collector devices
Request body: common request body
Response:
{
  "collectorCount": 2
}
Dataset: single value: collector count
Click data: none

_Widget name: "Firewalls"
Type: StatWidgetComponent
Insight: display total number of Firewall devices
Request body: common request body
Response:
{
  "firewallCount": 13
}
Dataset: single value: firewall count
Click data: none

_Widget name: "Resource Alert Status"
Type: TableWidgetComponent
Insight: display current alert severity for each network resource
Request body: common request body
Response:
{
  "data": [
    {
      "resourceName": "SERVER01",
      "severity": "Critical"
    }
  ]
}
Dataset:
- columns: Resource, Severity
Click data: resource name
Drilldown: none

_Widget name: "Resources by Type"
Type: PieWidgetComponent
Insight: display the distribution of network resources split by resource type
Request body: common request body
Response:
{
  "data": [
    {
      "resourceType": "Firewalls",
      "count": 13,
      "percentage": 43.33
    },
    {
      "resourceType": "Switches",
      "count": 15,
      "percentage": 50.0
    },
    {
      "resourceType": "Collectors",
      "count": 2,
      "percentage": 6.67
    }
  ]
}
Dataset: value: count or percentage, legend: resource type
Click data: resource type

_Widget name: "Downtime (Minutes)"
Type: TableWidgetComponent
Insight: display total downtime in minutes per resource, sorted descending; color cells by severity threshold
Request body: common request body with sort by downtime descending
Response:
{
  "data": [
    {
      "resourceName": "BroxABCPrime.broxnt.example.net",
      "downtimeMinutes": 93920
    }
  ]
}
Dataset:
- columns: Name, Downtime (mins)
Click data: resource name
Drilldown: none

_Widget name: "Worst PING Round Trip Times Worst 5"
Type: LineChartWidgetComponent
Insight: display PING round trip time trends for the 5 worst-performing resources over the selected date range
Request body: common request body
Response:
{
  "data": [
    {
      "resourceName": "BishopsC...",
      "date": "2026-04-10T00:00:00Z",
      "responseTimeMs": 120.5
    }
  ]
}
Dataset:
- axis x: date
- axis y: response time (ms)
- series: resource name
Click data: resource name

_Widget name: "CPU"
Type: TableWidgetComponent
Insight: display CPU usage percentage (averaged over 5 min periods) per network resource
Request body: common request body
Response:
{
  "data": [
    {
      "resourceName": "SERVER01",
      "cpuPercent": 85.5
    }
  ]
}
Dataset:
- columns: Name, CPU (over 5 min period)
Click data: resource name

_Widget name: "FANS"
Type: TableWidgetComponent
Insight: display fan health status per network resource
Request body: common request body
Response:
{
  "data": [
    {
      "resourceName": "SERVER01",
      "fanStatus": "Normal"
    }
  ]
}
Dataset:
- columns: Name, Status
Click data: resource name

_Widget name: "Memory"
Type: TableWidgetComponent
Insight: display memory free percentage per network resource
Request body: common request body
Response:
{
  "data": [
    {
      "resourceName": "SERVER01",
      "memoryPercentFree": 45.2
    }
  ]
}
Dataset:
- columns: Name, Memory Percent Free
Click data: resource name

_Widget name: "Interfaces - Discards Last 24 Hours"
Type: LineChartWidgetComponent
Insight: display interface discard packet trends over the last 24 hours for all selected resources
Request body: common request body with date range set to last 24 hours
Response:
{
  "data": [
    {
      "resourceName": "BoB-Core-...",
      "interfaceName": "eth0",
      "date": "2026-05-24T10:00:00Z",
      "discards": 0.8
    }
  ]
}
Dataset:
- axis x: date/time
- axis y: discards (ms unit label)
- series: resource + interface name
Click data: resource name

_Widget name: "Interfaces - InErrors Last 24 Hours"
Type: LineChartWidgetComponent
Insight: display inbound interface error trends over the last 24 hours for all selected resources
Request body: common request body with fixed 24 h ago start date and now end date
Response:
{
  "data": [
    {
      "resourceName": "BoB-Core-...",
      "interfaceName": "eth0",
      "date": "2026-05-24T10:00:00Z",
      "inErrors": 0.05
    }
  ]
}
Dataset:
- axis x: date/time
- axis y: in-errors (ms unit label)
- series: resource + interface name
Click data: resource name

_Widget name: "Interface - In Octets Worst 5"
Type: LineChartWidgetComponent
Insight: display inbound octet throughput trends for the 5 worst (highest traffic) interfaces over the selected date range
Request body: common request body
Response:
{
  "data": [
    {
      "resourceName": "BoB-Core-...",
      "interfaceName": "eth0",
      "date": "2026-04-10T00:00:00Z",
      "inOctets": 52428800
    }
  ]
}
Dataset:
- axis x: date
- axis y: inbound octets (display as M / bn scale)
- series: resource + interface name (top 5 by total inbound octets)
Click data: resource name

_Widget name: "Interface - Out Octets Worst 5"
Type: LineChartWidgetComponent
Insight: display outbound octet throughput trends for the 5 worst (highest traffic) interfaces over the selected date range
Request body: common request body
Response:
{
  "data": [
    {
      "resourceName": "BoB-Core-...",
      "interfaceName": "eth0",
      "date": "2026-04-10T00:00:00Z",
      "outOctets": 104857600
    }
  ]
}
Dataset:
- axis x: date
- axis y: outbound octets (display as M / bn scale)
- series: resource + interface name (top 5 by total outbound octets)
Click data: resource name
