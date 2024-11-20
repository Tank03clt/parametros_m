# Demonstração do uso de parâmetros M

#### Query exemplo

```sql
SELECT 
    c.[CountryFull] AS Country,
    s.[OrderDate],
    SUM(s.[Quantity] * s.[UnitPrice]) AS SalesAmount
FROM 
    [CONTOSO-DEV].[Data].Sales s
JOIN 
    [CONTOSO-DEV].[Data].Customer c
ON 
    c.CustomerKey = s.CustomerKey
GROUP BY 
    c.[CountryFull],
    s.[OrderDate]
ORDER BY 
    s.[OrderDate], c.[CountryFull];
```

Output:
![alt text](assets\image001.png)

