# Script Sur

```sql
EXEC sp_testlinkedserver @servername = 'XANDER27HALF';


USE MoodCoffee_DBSur;

-- 1. Cambiar al contexto de la base de datos del Nodo 2
USE MoodCoffee_DBSur;
GO

-- Fragmentacion Vertical - Nodo 2
CREATE TABLE Consumo_Operativo_Base 
(
    id_consumo INT NOT NULL,
    fecha DATE NOT NULL,
    id_cliente INT NOT NULL,
    id_sede INT NOT NULL
);
GO

-- Creacion de PK
ALTER TABLE Consumo_Operativo_Base ADD CONSTRAINT 
pk_id_consumo_OB_Sur PRIMARY KEY (id_consumo); -- Añadido "_Sur" para evitar conflictos si se consolidan esquemas
GO

-- 2. Insertar datos usando la nomenclatura de 4 partes del Linked Server
INSERT INTO MoodCoffee_DBSur.dbo.Consumo_Operativo_Base (id_consumo, fecha, id_cliente, id_sede)
SELECT id_consumo, fecha, id_cliente, id_sede 
FROM [XANDER27HALF].[Moodcoffee_GDB].[dbo].[Consumo] -- <--- AQUÍ CAMBIA: Prefijo del Linked Server
WHERE id_sede = 2; -- <--- RECOMENDADO: Filtrar de una vez para optimizar el almacenamiento local del Nodo Sur

-- Verificación
SELECT * FROM Consumo_Operativo_Base;

```

