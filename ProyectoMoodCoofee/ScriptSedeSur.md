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


