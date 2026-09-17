```



```

### Kusto Queries

```

AppRequests
| where TimeGenerated > ago(1h)
| where Name == 'GET api/calculator'
| project TimeGenerated, Success, DurationMs,ClientCity, OperationId
| order by TimeGenerated desc


AppRequests
| where TimeGenerated > ago(1h)
| where Name == 'GET api/calculator'
| summarize count() by tostring(Success)
| render piechart 


AppRequests
| where TimeGenerated > ago(1h)
| where Name == 'GET api/calculator'
| summarize count() by bin(TimeGenerated,5m), tostring(Success)
| render barchart 

```