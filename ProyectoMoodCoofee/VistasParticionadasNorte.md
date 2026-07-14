# Vista particionada en el norte

## Consumo Operativo 1
```sql
USE MoodCoffee_DBNorte;
GO

-- Asegurar la restricción CHECK de exclusividad en el Norte (Sede 1)
ALTER TABLE Consumo_Operativo1 
ADD CONSTRAINT chk_idSede_Norte CHECK (id_sede = 1);
GO

sp_help Consumo_Operativo1

USE MoodCoffee_DBNorte;
GO

IF OBJECT_ID('v_Consumo_Operativo', 'V') IS NOT NULL DROP VIEW v_Consumo_Operativo;
GO

CREATE VIEW v_Consumo_Operativo
AS
    -- Segmento Local: Sede Norte
    SELECT * FROM [XANDER27HALF].MoodCoffee_DBNorte.dbo.Consumo_Operativo1
    
    UNION ALL
    
    -- Segmento Remoto: Sede Sur en 4 partes
    SELECT * FROM [DESKTOP-KDT9TQQ].[MoodCoffee_DBSur].[dbo].[Consumo_Operativo2];
GO


```



## Detalle_Consumo


```sql
USE MoodCoffee_DBNorte;
GO

-- =========================================================================
-- 1. Asegurar la restricción CHECK de exclusividad en el Norte (Sede 1)
-- =========================================================================
ALTER TABLE Detalle1 
ADD CONSTRAINT chk_idSede_Detalle_Norte CHECK (id_sede = 1);
GO

-- Verificar que la restricción se haya creado correctamente en el Norte
sp_help Detalle1;
GO

-- =========================================================================
-- 2. Creación de la Vista Particionada Global de Detalles en el Norte
-- =========================================================================
IF OBJECT_ID('v_Detalle_Consumo', 'V') IS NOT NULL DROP VIEW v_Detalle_Consumo;
GO

CREATE VIEW v_Detalle_Consumo
AS
    -- Segmento Local: Detalles de la Sede Norte
    SELECT * FROM [XANDER27HALF].MoodCoffee_DBNorte.dbo.Detalle1
    
    UNION ALL
    
    -- Segmento Remoto: Detalles de la Sede Sur
    SELECT * FROM [DESKTOP-KDT9TQQ].[MoodCoffee_DBSur].[dbo].[Detalle2];
GO
select * from v_Detalle_Consumo
```
