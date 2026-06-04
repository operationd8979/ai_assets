Implement 'emissions-savings' dashboard

1. Filter dashboard include "year" (multiple select (only 3 years ago to now)), "recommendation type" (multiple select), "subscription" (multiple select), "region" (multiple select)

2. Widgets: common setting request body, response,... for all widgets

Common request body:
{
  "year": 2025,
  "recommendationTypes": [],
  "subscriptionIds": [],
  "regions": []
}

Common response:
{
  "data": [],
  "totalCount": 0
}

4. Detail:

_Widget name: "Potential Carbon Reductions"
Type: StatWidgetComponent
Insight: display total potential carbon reductions in kgCO2e
Request body: common request body
Response:
{
  "potentialCarbonReductionsKgCO2e": 30.41
}
Dataset: single value: potential carbon reductions
Click data: none

_Widget name: "Potential Carbon Emissions"
Type: StatWidgetComponent
Insight: display total potential carbon emissions and compare with actual YTD emissions
Request body: common request body
Response:
{
  "potentialCarbonEmissionsKgCO2e": 172310,
  "actualYtdCarbonEmissionsKgCO2e": 172340,
  "percentageChange": 0.02
}
Dataset: single value: potential carbon emissions
Click data: none

_Widget name: "Potential Cost Savings"
Type: StatWidgetComponent
Insight: display total potential cost savings from recommendations
Request body: common request body
Response:
{
  "potentialCostSavings": 3001,
  "currency": "GBP"
}
Dataset: single value: potential cost savings
Click data: none

_Widget name: "Total Recommendations"
Type: StatWidgetComponent
Insight: display total number of recommendations
Request body: common request body
Response:
{
  "totalRecommendations": 6
}
Dataset: single value: total recommendations
Click data: none

_Widget name: "Potential Carbon Reductions by Subscription Name"
Type: BarChartComponent
Insight: display potential carbon reductions grouped by subscription name
Request body: common request body
Response:
{
  "data": [
    {
      "subscriptionName": "SCC-DEV-CYB...",
      "potentialCarbonReductionsKgCO2e": 0.02
    }
  ]
}
Dataset: axis y: subscription name, axis x: potential carbon reductions kgCO2e
Click data: subscription name

_Widget name: "Carbon Emissions Comparison"
Type: BarChartComponent
Insight: compare actual carbon emissions and potential carbon emissions by year
Request body: common request body
Response:
{
  "data": [
    {
      "year": 2025,
      "carbonActualKgCO2e": 172340,
      "potentialCarbonEmissionsKgCO2e": 172310
    }
  ]
}
Dataset: axis y: kgCO2e, axis x: year, series: carbon actual and potential carbon emissions
Click data: year

_Widget name: "Recommendation by Type"
Type: PieWidgetComponent
Insight: display recommendation percentage split by recommendation type
Request body: common request body
Response:
{
  "data": [
    {
      "recommendationType": "Downgrade",
      "count": 4,
      "percentage": 66.67
    },
    {
      "recommendationType": "Shut down",
      "count": 2,
      "percentage": 33.33
    }
  ]
}
Dataset: value: count or percentage, legend: recommendation type
Click data: recommendation type

_Widget name: "Carbon Reductions Equivalent"
Type: info / equivalent metrics use customer html with angular sanitizer
Insight: display equivalent environmental impact for total carbon reductions
Request body: common request body
Response:
{
  "treesAbsorptionPerYear": 1,
  "milesDrivenByCar": 79.07,
  "gallonsOfGasolineConsumed": 3.44
}
Dataset: list of equivalent metrics
Click data: none

_Widget name: "Potential Carbon Reductions by Resource"
Type: use angular material card list display icon, title resource name, subtitle re
Insight: display recommendation details by resource, including recommendation message, potential carbon reductions, and potential cost savings
Request body: common request body
Response:
{
  "data": [
    {
      "resourceName": "advimage01",
      "recommendationMessage": "Switch to the more flexible, cost-effective Standard_B2as_v2 SKU that adapts to your workload",
      "potentialCarbonReductionsKgCO2e": 6.17,
      "potentialCostSavings": 701,
      "currency": "GBP"
    }
  ],
  "total": {
    "potentialCarbonReductionsKgCO2e": 30.41,
    "potentialCostSavings": 3001,
    "currency": "GBP"
  }
}
Dataset: columns: resource name, recommendation message, potential carbon reductions kgCO2e, potential cost savings
Click data: resource name

_Widget name: "Carbon Reductions by Location"
Type: MapWidgetComponent
Insight: display carbon reduction distribution by geographic location
Request body: common request body
Response:
{
  "data": [
    {
      "location": "Europe",
      "latitude": 50.1109,
      "longitude": 8.6821,
      "potentialCarbonReductionsKgCO2e": 30.41
    }
  ]
}
Dataset: location points with latitude, longitude, and potential carbon reductions kgCO2e
Click data: location