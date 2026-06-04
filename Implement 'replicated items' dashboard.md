Implement 'replicated items' dashboard

1. Filter dashboard include "datasource subscription" (multiple select), "vault" (multiple select), "status" (multiple select)

2. Base model in db: inject xml file

Base model: ReplicatedItem

Fields:
- id: string
- tenantId: string
- name: string
- replicationHealth: string
- datasourceSubscriptionId: string
- datasourceSubscriptionName: string
- activeLocation: string
- status: string
- vaultId: string
- vaultName: string
- failoverHealth: string

3. Widgets: common setting request body, response,...

Common request body:
{
  "datasourceSubscriptionIds": [],
  "vaultIds": [],
  "statuses": []
}

Common response:
{
  "data": [],
}

4. Detail:

_Widget name: Replicated items table
Type: Material table (not need use dirll down table, just table with pagination) Replication Health display with icon, status display with icon
- Columns:
  - Name
  - Replication Health
  - Datasource Subscription
  - Active Location
  - Status
  - Vault
  - Failover Health
Insight: display replicated items with replication health, datasource subscription, active location, protection status, vault, and failover health
Request body:
{
  "datasourceSubscriptionIds": [],
  "vaultIds": [],
  "statuses": []
}
Response:
{
  "data": [
    {
      "id": "string",
      "name": "vmtest07",
      "replicationHealth": "Critical",
      "datasourceSubscriptionId": "string",
      "datasourceSubscriptionName": "SCC-DEV-SANDBOX-DLEE",
      "activeLocation": "UK South",
      "status": "Protected",
      "vaultId": "string",
      "vaultName": "rsv-ukw-test-dr-001",
      "failoverHealth": "Warning"
    }
  ]
}
Click Data: None
Drill down: None