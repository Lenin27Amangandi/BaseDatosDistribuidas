## Creacion

```sql
USE MoodCoffee_DBNorte;
GO

-- Configuración transaccional obligatoria en SQL Server para vistas distribuidas
SET XACT_ABORT ON;
GO

-- =========================================================================
-- SCRIPT UNIFICADO DE CREACIÓN / INSERCIÓN (Consumo 302 - Sede 1)
-- =========================================================================
BEGIN TRANSACTION;

    -- PASO 1: Insertar en el fragmento financiero vertical (Físicamente en el Norte)
    -- Registramos el valor monetario de la transacción en la tabla centralizada
    INSERT INTO [XANDER27HALF].[MoodCoffee_DBNorte].[dbo].[Consumo_Financiero] (id_consumo, total)
    VALUES (302, 8.75);

    -- PASO 2: Insertar la cabecera del consumo operativo a través de la vista mixta
    -- (SQL Server redirige el insert de forma local a 'Consumo_Operativo1' por el id_sede = 1)
    INSERT INTO v_Consumo_Operativo (id_consumo, fecha, id_cliente, id_sede)
    VALUES (302, '2026-07-14', 4, 1);

    -- PASO 3: Insertar el detalle asociado a través de la vista global de detalles
    -- (SQL Server redirige el insert de forma local a 'Detalle1' por el id_sede = 1)
    INSERT INTO v_Detalle_Consumo (id_linea, id_consumo, id_sede, cantidad, subtotal, id_producto)
    VALUES (999, 302, 1, 3, 8.75, 1);

COMMIT TRANSACTION;
GO

-- =========================================================================
-- VERIFICACIÓN DE LA INSERCIÓN
-- =========================================================================
-- Consultamos las vistas para verificar que el nuevo registro mixto se ha reconstruido con éxito
SELECT 'Cabecera del Consumo' AS Vista, * FROM v_Consumo_Operativo WHERE id_consumo = 302;
SELECT 'Detalle del Consumo' AS Vista, * FROM v_Detalle_Consumo WHERE id_consumo = 302;
GO
```

## Update 

```sql
USE MoodCoffee_DBNorte;
GO

-- Configuración transaccional obligatoria en SQL Server para consultas distribuidas
SET XACT_ABORT ON;
GO

-- =========================================================================
-- SCRIPT UNIFICADO DE ACTUALIZACIÓN / UPDATE (Consumo 302 - Sede 1)
-- =========================================================================
BEGIN TRANSACTION;

    -- PASO 1: Actualizar el fragmento financiero vertical (4 partes - Local)
    -- Modificamos el total de la venta de $8.75 a $12.50
    UPDATE [XANDER27HALF].[MoodCoffee_DBNorte].[dbo].[Consumo_Financiero]
    SET total = 12.50
    WHERE id_consumo = 302;

    -- PASO 2: Actualizar la cabecera del consumo operativo a través de la vista mixta (4 partes - Local)
    -- SQL Server redirige la orden localmente a 'Consumo_Operativo1' por el id_sede = 1
    UPDATE [XANDER27HALF].[MoodCoffee_DBNorte].[dbo].[v_Consumo_Operativo]
    SET id_cliente = 1 -- Cambiamos de cliente asignado (del Cliente 4 al Cliente 1)
    WHERE id_consumo = 302 
      AND id_sede = 1;

    -- PASO 3: Actualizar el detalle asociado a través de la vista global de detalles (4 partes - Local)
    -- SQL Server redirige la orden localmente a 'Detalle1' por el id_sede = 1
    UPDATE [XANDER27HALF].[MoodCoffee_DBNorte].[dbo].[v_Detalle_Consumo]
    SET cantidad = 2,
        subtotal = 12.50 -- Ajustamos el subtotal de la línea para cuadrar con el nuevo total financiero
    WHERE id_linea = 999 
      AND id_consumo = 302 
      AND id_sede = 1;

COMMIT TRANSACTION;
GO

-- =========================================================================
-- VERIFICACIÓN DE LA ACTUALIZACIÓN (4 partes - Local)
-- =========================================================================
-- Consultamos las vistas y tablas utilizando la nomenclatura formal para verificar los cambios
SELECT 'Cabecera Modificada' AS Objeto, * 
FROM [XANDER27HALF].[MoodCoffee_DBNorte].[dbo].[v_Consumo_Operativo] 
WHERE id_consumo = 302;

SELECT 'Detalle Modificado' AS Objeto, * 
FROM [XANDER27HALF].[MoodCoffee_DBNorte].[dbo].[v_Detalle_Consumo] 
WHERE id_consumo = 302;

SELECT 'Financiero Modificado' AS Objeto, * 
FROM [XANDER27HALF].[MoodCoffee_DBNorte].[dbo].[Consumo_Financiero] 
WHERE id_consumo = 302;
GO
```

## Delete

```sql
USE MoodCoffee_DBNorte;
GO

-- Configuración transaccional obligatoria en SQL Server para vistas distribuidas
SET XACT_ABORT ON;
GO

-- =========================================================================
-- SCRIPT UNIFICADO DE ELIMINACIÓN (Consumo 302 - Sede 1)
-- =========================================================================
BEGIN TRANSACTION;

    -- PASO 1: Eliminar las líneas de detalle asociadas a través de la vista global
    -- (SQL Server redirige la orden de forma local a 'Detalle1' por el filtro id_sede = 1)
    DELETE FROM v_Detalle_Consumo
    WHERE id_consumo = 302 
      AND id_sede = 1;

    -- PASO 2: Eliminar el fragmento financiero vertical (Físicamente en el Norte)
    DELETE FROM [XANDER27HALF].[MoodCoffee_DBNorte].[dbo].[Consumo_Financiero]
    WHERE id_consumo = 302;

    -- PASO 3: Eliminar la cabecera del consumo operativo a través de la vista mixta
    -- (SQL Server redirige la orden de forma local a 'Consumo_Operativo1' por el filtro id_sede = 1)
    DELETE FROM v_Consumo_Operativo
    WHERE id_consumo = 302 
      AND id_sede = 1;

COMMIT TRANSACTION;
GO

-- =========================================================================
-- VERIFICACIÓN DE LA ELIMINACIÓN
-- =========================================================================
-- Al consultar las vistas y tablas, no debe aparecer ningún registro del consumo 302
SELECT 'Detalles Borrados' AS Estado, * FROM v_Detalle_Consumo WHERE id_consumo = 302;
SELECT 'Financiero Borrado' AS Estado, * FROM [XANDER27HALF].[MoodCoffee_DBNorte].[dbo].[Consumo_Financiero] WHERE id_consumo = 302;
SELECT 'Operativo Borrado' AS Estado, * FROM v_Consumo_Operativo WHERE id_consumo = 302;
GO
```
