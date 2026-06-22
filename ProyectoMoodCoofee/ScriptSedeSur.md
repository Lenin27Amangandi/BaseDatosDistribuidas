# Script Sur

```sql
EXEC sp_testlinkedserver @servername = 'XANDER27HALF';

drop table Consumo_Operativo_Base 

USE MoodCoffee_DBSur;

--    Fragmentacion Vertical 

CREATE TABLE Consumo_Operativo_Base 
(
    id_consumo INT NOT NULL,
    fecha DATE NOT NULL,
    id_cliente INT NOT NULL,
    id_sede INT NOT NULL
);

-- Creacion de PK
alter table Consumo_Operativo_Base add constraint 
pk_id_consumo_OB primary key (id_consumo)

-- Insertar datos 
insert into MoodCoffee_DBSur.dbo.Consumo_Operativo_Base
select id_consumo,fecha,id_cliente,id_sede from [XANDER27HALF].[Moodcoffee_GDB].[dbo].[Consumo]

SELECT * FROM Consumo_Operativo_Base;
```


```sql
-- ==========================================
--    Fragmentacion Horizontal VH - Nodo 2
-- ==========================================

CREATE TABLE Consumo_Operativo2 (
    id_consumo INT NOT NULL,
    fecha      DATE NOT NULL,
    id_cliente INT NOT NULL,
    id_sede    INT NOT NULL
);
GO

-- Creación de la PK Compuesta para el Fragmento 2
ALTER TABLE Consumo_Operativo2 ADD CONSTRAINT 
pk_id_consumo_id_sede2 PRIMARY KEY (id_consumo, id_sede);
GO

-- FK Consumo_Operativo2 con la Sede Maestro
ALTER TABLE Consumo_Operativo2 ADD CONSTRAINT 
fk_consumo2_sede FOREIGN KEY (id_sede) REFERENCES Sede(id_sede);

-- FK Consumo_Operativo2 con el Cliente Operativo
ALTER TABLE Consumo_Operativo2 ADD CONSTRAINT 
fk_consumo2_cliente FOREIGN KEY (id_cliente) REFERENCES Cliente_Operativo(id_cliente);
GO

-- Inserción aplicando la condición de fragmentación horizontal del SUR: σ id_sede = 2
INSERT INTO Consumo_Operativo2 (id_consumo, fecha, id_cliente, id_sede)
SELECT id_consumo, fecha, id_cliente, id_sede
FROM Consumo_Operativo_Base
WHERE id_sede = 2; -- <--- CORREGIDO: Cambiado a Sede 2
GO

-- Verificación del fragmento del Sur
SELECT * FROM Consumo_Operativo2;
```



```sql
-- ==========================================
--    Fragmentacion Derivada - Nodo Sur
-- ==========================================

CREATE TABLE Detalle2 (
    id_detalle   INT NOT NULL,
    id_consumo   INT NOT NULL,
    id_sede      INT NOT NULL,  -- Campo de localidad
    cantidad     INT NOT NULL,
    subtotal     DECIMAL(10,2) NOT NULL,
    id_producto  INT NOT NULL
);
GO

-- Pk del fragmento derivado local
ALTER TABLE Detalle2 ADD CONSTRAINT 
pk_id_detalle_id_consumo_id_sede2 PRIMARY KEY (id_detalle, id_consumo, id_sede);

-- Fk mapeada correctamente hacia Consumo_Operativo2 (Local del Nodo Sur)
ALTER TABLE Detalle2 ADD CONSTRAINT 
fk_idconsumo_idsede2 FOREIGN KEY (id_consumo, id_sede)
REFERENCES Consumo_Operativo2(id_consumo, id_sede);
GO

-- Inserción de datos aplicando Semijoin para la Sede Sur (Sede 2)
INSERT INTO MoodCoffee_DBSur.dbo.Detalle2 (id_detalle, id_consumo, id_sede, cantidad, subtotal, id_producto)
SELECT 
    gdb_det.id_detalle, 
    gdb_det.id_consumo, 
    2, -- <--- CORREGIDO: Forzamos el ID de la Sede Sur (Sede 2)
    gdb_det.cantidad, 
    gdb_det.subtotal, 
    gdb_det.id_producto
FROM [X].[Moodcoffee_GDB].[dbo].[Detalle_Consumo] gdb_det -- <--- CORREGIDO: Tabla remota correcta de Detalles
WHERE (
    SELECT central_con.id_sede 
    FROM [XANDER27HALF].[Moodcoffee_GDB].[dbo].[Consumo] central_con
    WHERE gdb_det.id_consumo = central_con.id_consumo
) = 2; -- <--- CORREGIDO: Condición estricta para la Sede Sur (Sede 2)
GO

-- Verificación del fragmento local
SELECT * FROM Detalle2;

-- Enlace con el catálogo local de Productos
ALTER TABLE Detalle2 ADD CONSTRAINT fk_detalle2_producto FOREIGN KEY (id_producto) REFERENCES Producto(id_producto);
GO


```

```sql
-- Ejecuta esto en tu base del Sur para pintar la línea inmediatamente
ALTER TABLE Producto 
    ADD CONSTRAINT fk_producto_estado_sur FOREIGN KEY (id_estado) 
    REFERENCES Estado_Animo(id_estado);
GO
```
