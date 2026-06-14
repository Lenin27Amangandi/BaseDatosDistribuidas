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

IF DB_ID('MoodCooffe_BDSUR') IS NOT NULL
BEGIN
    ALTER DATABASE MoodCooffe_BDSUR SET SINGLE_USER WITH ROLLBACK IMMEDIATE;
    DROP DATABASE MoodCooffe_BDSUR;
END
GO





SELECT * FROM Producto

-- 2. Modificar el precio del producto
UPDATE Producto 
SET precio = 2.50 
WHERE id_producto = 4

SELECT * FROM Producto WHERE id_producto 

```
