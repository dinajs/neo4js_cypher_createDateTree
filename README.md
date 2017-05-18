# Neo4js Cypher CreateDateTree

```
WITH range(2016, 2018) AS Years, range(10,12) AS Months,range(1,9)AS LowNum
FOREACH(year IN Years | 
  MERGE (y:Years {year: year})
  FOREACH(month IN LowNum |
    CREATE (m:Months {date_txt:(toString(year)+'-0'+month),month:month})
    MERGE (y)-[:HAS_MONTH]->(m)
    FOREACH(day IN LowNum|
                    CREATE (d:Days {date_txt:m.date_txt+'-0'+day,day:day})
      				MERGE (m)-[:HAS_DAY]->(d)
     )
    FOREACH(day IN (CASE 
                      WHEN month IN [1,3,5,7,8,10,12] THEN range(10,31) 
                      WHEN month = 2 THEN 
                        CASE
                          WHEN year % 4 <> 0 THEN range(10,28)
                          WHEN year % 100 <> 0 THEN range(10,29)
                          WHEN year % 400 <> 0 THEN range(10,29)
                          ELSE range(10,28)
                        END
                      ELSE range(10,30)
                    END)|
                    CREATE(d:Days {date_txt:m.date_txt+'-'+day,day:day})
      				MERGE (m)-[:HAS_DAY]->(d)
     )
   )
   
   FOREACH(month IN Months |
    CREATE (m:Months {date_txt:(toString(year)+'-'+month),month:month})
    MERGE (y)-[:HAS_MONTH]->(m)
   FOREACH(day IN LowNum|
                    CREATE(d:Days {date_txt:m.date_txt+'-0'+day,day:day})
      				MERGE (m)-[:HAS_DAY]->(d)
     )
    FOREACH(day IN (CASE 
                      WHEN month IN [1,3,5,7,8,10,12] THEN range(10,31) 
                      WHEN month = 2 THEN 
                        CASE
                          WHEN year % 4 <> 0 THEN range(10,28)
                          WHEN year % 100 <> 0 THEN range(10,29)
                          WHEN year % 400 <> 0 THEN range(10,29)
                          ELSE range(10,28)
                        END
                      ELSE range(10,30)
                    END)|
                    CREATE (d:Days {date_txt:m.date_txt+'-'+day,day:day})
      				MERGE (m)-[:HAS_DAY]->(d)
     )
   )
   )
```
