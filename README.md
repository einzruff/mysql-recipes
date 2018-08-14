# mysql-recipes
MySQL recipes and musings
---
Recipe 1: Inventory contains multiple entries of a part. I want a list of all distinct parts with their newest dates and the quantity on hand.
These must all be active parts (activeFlag = 1).

Table 'Inventory'

| id   | dateCreated   | partId   | qtyOnHand  |
| ---- | ------------- | -------- | ---------- |
| 2    | 2015-5-11     | 123456   | 20         |
| 3    | 2016-8-2      | 123456   | 25         |
| 4    | 2016-3-6      | 456789   | 14         |
| 5    | 2018-7-16     | 876543   | 42         |

Table 'Part'

| id     | num  | activeFlag |
| ------ | ---- | ---------- |
| 123456 | Z343 | 1          |
| 456789 | Z344 | 1          |
| 876543 | Z345 | 1          |

```sql
SELECT MAX(T1.dateCreated), T1.partId, T1.qtyOnHand, T2.num, T2.activeFlag
FROM (SELECT dateCreated,partId,qtyOnHand 
		FROM Inventory 
		WHERE qtyOnHand>0 
        ORDER BY dateCreated DESC) AS T1
INNER JOIN Part T2 ON T1.partId = T2.id
WHERE T2.activeFlag=1
GROUP BY T1.partId
```

RESULT:

| MAX(T1.dateCreated) | partId | qtyOnHand |
| ------------------- | ------ | --------- |
| 2016-8-2            | 123456 | 25        |
| 2016-3-6            | 456789 | 14        |
| 2016-7-16           | 876543 | 42        |
