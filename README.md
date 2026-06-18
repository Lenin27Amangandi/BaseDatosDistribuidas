# BaseDatosDistribuidas
Implementación de scripts para bdd


```sql
/--------------------- MoodCooffe_BDSUR ------------------/

-- Esquema de replicaci�n. 
/* tabla facultad, replicaci�n unidireccional */

/--------------------------------------------/

-- Eliminar la base de datos si existe
USE master;
GO

IF DB_ID('MoodCooffe_DBSUR') IS NOT NULL
BEGIN
    ALTER DATABASE ModCooffe_DBSUR SET SINGLE_USER WITH ROLLBACK IMMEDIATE;
    DROP DATABASE ModCooffe_DBSUR;
END
GO

create database MoodCoffee_DBSur COLLATE Modern_Spanish_CI_AS
go
use MoodCoffee_DBSur


select * from Sede
select * from Estado_Animo
select * from Producto

-- 2. Modificar el precio del producto
UPDATE Producto 
SET precio = 2.50 
WHERE id_producto = 4

SELECT * FROM Producto WHERE id_producto




--- Tratar de solcuionar

USE [MoodCoffee_DBSur];
GO

-- Esto le dice a la base de datos que vuelva a aceptar cambios DML en tablas de réplica
EXEC sp_changesubstatus 
    @publication = 'P_ReplClienteOperacional', 
    @article = 'Cliente_Operativo', 
    @status = 'active';
GO


--- EditarNodoSur

UPDATE Producto 
SET precio = 4.50 
WHERE id_producto = 4

UPDATE Sede 
SET nombre = 'Sede Norte Central' 
WHERE id_sede = 1;

UPDATE Estado_Animo 
SET descripcion = 'Productivo y Feliz' 
WHERE id_estado = 2;

```

## Mecanica

```sql
-- Eliminar la base de datos si existe
USE master;
GO

IF DB_ID('MEC') IS NOT NULL
BEGIN
    ALTER DATABASE MEC SET SINGLE_USER WITH ROLLBACK IMMEDIATE;
    DROP DATABASE MEC;
END
GO

-- Crear base de datos
CREATE DATABASE MEC COLLATE Modern_Spanish_CI_AS;
GO

USE MEC;
GO

sp_helpdb mec
```
