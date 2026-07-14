# Vistas Particionadas Sur

## Vsita Consumo_Operativo2 


```sql
USE MoodCoffee_DBSur;
GO

-- =========================================================================
-- 1. Asegurar la restricción CHECK de exclusividad en el Sur (Sede 2)
-- =========================================================================
ALTER TABLE Consumo_Operativo2 
ADD CONSTRAINT chk_idSede_Sur CHECK (id_sede = 2);
GO

-- Verificar que la restricción se haya creado correctamente
sp_help Consumo_Operativo2;
GO

-- =========================================================================
-- 2. Creación de la Vista Particionada Global en el Sur
-- =========================================================================
IF OBJECT_ID('v_Consumo_Operativo', 'V') IS NOT NULL DROP VIEW v_Consumo_Operativo;
GO

CREATE VIEW v_Consumo_Operativo
AS
    -- Segmento Remoto: Sede Norte (Traído desde el servidor del Norte)
    SELECT * FROM [XANDER27HALF].MoodCoffee_DBNorte.dbo.Consumo_Operativo1
    
    UNION ALL
    
    -- Segmento Local: Sede Sur
    SELECT * FROM [DESKTOP-KDT9TQQ].[MoodCoffee_DBSur].[dbo].[Consumo_Operativo2];
GO
```
