## Eliminacion de vistas

```sql
USE MoodCoffee_DBNorte;
GO

-- 1. Eliminar la vista con el nombre antiguo si existe
IF OBJECT_ID('v_Detalle_Consumo', 'V') IS NOT NULL 
    DROP VIEW v_Detalle_Consumo;
GO

-- 2. Crear la vista con el nuevo nombre (Ejemplo: v_Detalle_Factura_Global)
CREATE VIEW v_Detalle_Factura_Global
AS
    -- Segmento Local: Sede Norte
    SELECT * FROM [XANDER27HALF].MoodCoffee_DBNorte.dbo.Detalle1
    
    UNION ALL
    
    -- Segmento Remoto: Sede Sur
    SELECT * FROM [DESKTOP-KDT9TQQ].[MoodCoffee_DBSur].[dbo].[Detalle2];
GO
```

## Creación
```sql
USE MoodCoffee_DBNorte;
GO

-- Configuración obligatoria para inserciones en vistas distribuidas
SET XACT_ABORT ON;
GO

-- Insertamos el nuevo detalle de prueba '999' asociado al consumo existente 1
INSERT INTO v_Detalle_Consumo (id_linea, id_consumo, id_sede, cantidad, subtotal, id_producto)
VALUES (999, 1, 1, 3, 4.50, 1);
GO

-- Verificación de la inserción
SELECT * FROM v_Detalle_Consumo 
WHERE id_linea = 999;
GO
```

## Actualización Remota Ejemplo (UPDATE Explícito)

```sql
USE MoodCoffee_DBNorte;
GO

-- Configuración obligatoria para actualizaciones sobre vistas particionadas
SET XACT_ABORT ON;
GO

-- Modificación de la fecha en el consumo operativo #3 (Sede Norte)
UPDATE v_Consumo_Operativo
SET fecha = '2078-04-12'
WHERE id_consumo = 3 
  AND id_sede = 1;
GO

-- Verificación del cambio reflejado
SELECT * FROM v_Consumo_Operativo 
WHERE id_consumo = 3;
GO
```


## Actualización Remota Ejemplo (UPDATE Explícito)

```sql
USE MoodCoffee_DBNorte;
GO

-- Configuración transaccional distribuida obligatoria
SET XACT_ABORT ON;
GO

-- Inicio manual y explícito de la transacción distribuida
BEGIN DISTRIBUTED TRANSACTION;

    -- Actualización sobre el registro físico del Sur a través de la vista
    UPDATE v_Detalle_Consumo
    SET subtotal = 2.20
    WHERE id_linea = 10 
      AND id_consumo = 8 
      AND id_sede = 2;

-- Confirmación bajo el protocolo de dos fases (2PC)
COMMIT TRANSACTION;
GO
```


## Eliminación Ejemplo (DELETE)
```sql
USE MoodCoffee_DBNorte;
GO

-- Configuración de integridad obligatoria
SET XACT_ABORT ON;
GO

-- Eliminación del registro de prueba '999' del Norte
DELETE FROM v_Detalle_Consumo
WHERE id_linea = 999 
  AND id_sede = 1;
GO

-- Verificación de la eliminación completa
SELECT * FROM v_Detalle_Consumo 
WHERE id_linea = 999;
GO
```
