# Eliminacion de base de datos centralizada cunado aun estaba con replicacion 
```sql
USE master;
GO

EXEC sp_helpreplicationdboption @dbname = 'MoodCoffee_GDB';


USE master;
GO

EXEC sp_removedbreplication @dbname = 'MoodCoffee_GDB';
GO


ALTER DATABASE MoodCoffee_GDB
SET SINGLE_USER
WITH ROLLBACK IMMEDIATE;
GO

DROP DATABASE MoodCoffee_GDB;
GO
```
