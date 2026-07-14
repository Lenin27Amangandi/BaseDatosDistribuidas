```sql
USE MoodCoffee_DBSur;
GO

-- Directiva obligatoria para la ejecución de transacciones distribuidas
SET XACT_ABORT ON;
GO

-- =========================================================================
-- CASO 1: Inserción destinada a la SEDE 2 (Financiero en Norte, Operativo en Sur)
-- =========================================================================
BEGIN TRANSACTION;

    -- 1. Inserción remota en el fragmento financiero centralizado (4 partes remoto)
    INSERT INTO [XANDER27HALF].[MoodCoffee_DBNorte].[dbo].[Consumo_Financiero] (id_consumo, total)
    VALUES (301, 19.50);

    -- 2. Inserción local en el fragmento operativo del Sur (4 partes local)
    INSERT INTO [DESKTOP-KDT9TQQ].[MoodCoffee_DBSur].[dbo].[Consumo_Operativo2] (id_consumo, fecha, id_cliente, id_sede)
    VALUES (301, '2026-07-14', 3, 2);

COMMIT TRANSACTION;
GO

-- =========================================================================
-- CASO 2: Inserción destinada a la SEDE 1 (Físicamente todo en el Norte)
-- =========================================================================
BEGIN TRANSACTION;

    -- 1. Inserción remota en el fragmento financiero centralizado (4 partes remoto)
    INSERT INTO [XANDER27HALF].[MoodCoffee_DBNorte].[dbo].[Consumo_Financiero] (id_consumo, total)
    VALUES (302, 8.75);

    -- 2. Inserción remota en el fragmento operativo del Norte (4 partes remoto)
    INSERT INTO [XANDER27HALF].[MoodCoffee_DBNorte].[dbo].[Consumo_Operativo1] (id_consumo, fecha, id_cliente, id_sede)
    VALUES (302, '2026-07-14', 4, 1);

COMMIT TRANSACTION;
GO
```
