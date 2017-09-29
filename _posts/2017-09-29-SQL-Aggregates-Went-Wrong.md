I've spent a lot of time at work in the last 2 weeks on a bizarre issue that is caused by embedded views and aggregates.

It was painful at the time because I had to troubleshoot remotely with average 15 minutes wait-time when running the RDL report.

After many hours of troubleshooting, it has come down to the queries below, which has been trimmed down for the sake of simplicity:

```sql
SELECT  
X.[ProgramID],   
X.[TableSource],  
X.[ExpenseLinkageID],  
X.[ExpenseLinkageDate],  
X.[IsReversal],  
SUM([Amount] + COALESCE([GST], 0)) OVER (PARTITION BY ExpenseLinkageID, ExpenseLinkageDate) as [ExpenseTotal],  
MAX(X.[IsPreviousPeriodReversal]) OVER (PARTITION BY ExpenseLinkageID, ExpenseLinkageDate) as [IsPreviousPeriodReversal]
FROM X
WHERE ProgramID = '2a6ff412-ddea-4e22-ac43-68f38ab13375' AND TableSource = 'AR'


SELECT * FROM (
SELECT  
X.[ProgramID],   
X.[TableSource],  
X.[ExpenseLinkageID],  
X.[ExpenseLinkageDate],  
X.[IsReversal],  
SUM([Amount] + COALESCE([GST], 0)) OVER (PARTITION BY ExpenseLinkageID, ExpenseLinkageDate) as [ExpenseTotal],  
MAX(X.[IsPreviousPeriodReversal]) OVER (PARTITION BY ExpenseLinkageID, ExpenseLinkageDate) as [IsPreviousPeriodReversal]
FROM X
) Y
WHERE ProgramID = '2a6ff412-ddea-4e22-ac43-68f38ab13375' AND TableSource = 'AR'
```

I was expecting these 2 queries to return the same results however they don't.
