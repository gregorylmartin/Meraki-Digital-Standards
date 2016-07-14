#SQL Standards (SQL Server)



##Stored Procedures
Stored procedures and functions should, very near the top, always have...

```sql
set nocount on
```
Then, last thing at the bottom,...
```sql
set nocount off
```
