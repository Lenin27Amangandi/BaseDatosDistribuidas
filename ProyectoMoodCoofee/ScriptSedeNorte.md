# Replicacion

```sql
/*--------------------- MoodCoffee_DBNorte ------------------*/

-- Esquema de replicacin. 
/* tablas Sede, Estado_Animo y Producto, se crean las tablas 
en el nodo de gestion para una replicacin unidireccional */
/* tablas Cliente_Operativo se crea para una replicacin bidireccional */

create database MoodCoffee_DBNorte COLLATE Modern_Spanish_CI_AS
go
use MoodCoffee_DBNorte

create table Sede
(
id_sede int not null,
nombre varchar(100) not null,
ciudad varchar(100) not null,
direccion varchar(255)
)

create table Estado_Animo
(
id_estado int not null,
nombre varchar(100) not null,
descripcion text
)

create table Producto
(
id_producto int not null,
nombre varchar(100) not null,
descripcion text,
tipo varchar(50),
precio decimal(10,2) not null,
id_estado int not null
)


/*--------------------------------------------*/
-- Creacin de PKs
/*--------------------------------------------*/

-- Sede
alter table Sede add constraint 
pk_id_sede primary key (id_sede)

-- Estado Animo
alter table Estado_Animo add constraint 
pk_id_estado primary key (id_estado)

-- Producto
alter table Producto add constraint 
pk_id_producto primary key (id_producto)

alter table Producto add constraint 
fk_producto_estado foreign key (id_estado) 
references Estado_Animo(id_estado)

/*--------------------------------------------*/
------ RECUPERAR DATOS DE MOODCOFFEE --------
/*--------------------------------------------*/

-- tabla Sede
insert into MoodCoffee_DBNorte.dbo.Sede
select * from Moodcoffee_GDB.dbo.Sede

select * from Sede

-- tabla Estado_Animo
insert into MoodCoffee_DBNorte.dbo.Estado_Animo
select * from Moodcoffee_GDB.dbo.Estado_Animo

select * from Estado_Animo

-- tabla Producto
insert into MoodCoffee_DBNorte.dbo.Producto
select * from Moodcoffee_GDB.dbo.Producto

select * from Producto


select * from Sede
select * from Estado_Animo
select * from Producto
```

## Fragmetacion

```sql
--- Fragmetacion vertical
use MoodCoffee_DBNorte

-- ==========================================
--              Cliente_Datos
-- ==========================================
-- Creamos La tabla Cliente_Operativo
create table Cliente_Operativo
(
id_cliente int not null,
nombre varchar(100) not null,
apellido varchar(100) not null
)

-- pk Cliente Operativo
alter table Cliente_Operativo add constraint 
pk_id_cliente_operativo primary key (id_cliente)

-- Insertar datos Cliente Operativo
insert into MoodCoffee_DBNorte.dbo.Cliente_Operativo
select id_cliente,nombre,apellido from Moodcoffee_GDB.dbo.Cliente

select * from Cliente_Operativo

-- ==========================================
--              Cliente_Datos
-- ==========================================
create table Cliente_Datos
(
    id_cliente INT NOT NULL,
    cedula VARCHAR(10) NOT NULL,
    telefono VARCHAR(15),
    correo VARCHAR(150)
)
--- Pk
alter table Cliente_Datos add constraint 
pk_id_cliente primary key (id_cliente)

-- Insertar datos 
insert into MoodCoffee_DBNorte.dbo.Cliente_Datos
select id_cliente,cedula,telefono,correo from Moodcoffee_GDB.dbo.Cliente

select * from Cliente_Datos


-- ==========================================
--              Consumo_Financiero
-- ==========================================

----CREATE TABLE Consumo 
----(
----    id_consumo INT NOT NULL,
----    total DECIMAL(10, 2) NOT NULL,
----    fecha DATE NOT NULL,
----    id_cliente INT NOT NULL,
----    id_sede INT NOT NULL
----);


create table Consumo_Financiero
(
    id_consumo INT NOT NULL,
    total DECIMAL(10, 2) NOT NULL
)


--- Pk
alter table Consumo_Financiero add constraint 
pk_id_consumo primary key (id_consumo)

-- Insertar datos 
insert into MoodCoffee_DBNorte.dbo.Consumo_Financiero
select id_consumo,total from Moodcoffee_GDB.dbo.Consumo

select * from Consumo_Financiero

-- ==========================================
--              Consumo_Operativo1
-- ==========================================

-- 1. Creamos directamente Consumo_Operativo1 sin tablas intermedias
CREATE TABLE Consumo_Operativo1 (
    id_consumo INT NOT NULL,
    fecha DATE NOT NULL,
    id_cliente INT NOT NULL,
    id_sede INT NOT NULL
);
GO

-- 2. Añadimos la Clave Primaria compuesta (incluye el campo de fragmentación)
ALTER TABLE Consumo_Operativo1 
ADD CONSTRAINT pk_id_consumo_id_sede1 PRIMARY KEY (id_consumo, id_sede);
GO

-- 3. Añadimos las Llaves Foráneas para cuidar la integridad en el Norte
-- NOTA:id_cliente apunta a la tabla 'Cliente_Operativo' que acabamos de crear antes
ALTER TABLE Consumo_Operativo1 
ADD CONSTRAINT fk_consumo_cliente1 FOREIGN KEY (id_cliente) 
REFERENCES Cliente_Operativo(id_cliente);
GO

-- 4. Insertamos directo aplicando la Proyección y el Filtro (id_sede = 1)
INSERT INTO MoodCoffee_DBNorte.dbo.Consumo_Operativo1 (id_consumo, fecha, id_cliente, id_sede)
SELECT id_consumo, fecha, id_cliente, id_sede 
FROM Moodcoffee_GDB.dbo.Consumo
WHERE id_sede = 1;
GO

-- 5. Verificación final del fragmento en el Norte
SELECT * FROM Consumo_Operativo1;
GO

-- ==========================================
--              Detalle1
-- ==========================================

drop table Detalle1

CREATE TABLE Detalle1 (
    id_linea INT NOT NULL,       -- ¡Cambio aquí!
    id_consumo INT NOT NULL,
    id_sede INT NOT NULL,  
    cantidad INT NOT NULL,
    subtotal DECIMAL(10,2) NOT NULL,
    id_producto INT NOT NULL
);
GO

-- 2. Clave primaria (Adaptada con id_linea)
ALTER TABLE Detalle1 
ADD CONSTRAINT pk_id_linea_id_consumo_id_sede PRIMARY KEY (id_linea, id_consumo, id_sede);
GO

-- 3. Llave foránea (Se mantiene igual porque apunta a la cabecera)
ALTER TABLE Detalle1 
ADD CONSTRAINT fk_idconsumo_idsede FOREIGN KEY (id_consumo, id_sede)
REFERENCES Consumo_Operativo1(id_consumo, id_sede);
GO


-- =========================================================================
-- 4. Inserción de datos aplicando la lógica de Semijoin (⋉)
-- =========================================================================
INSERT INTO MoodCoffee_DBNorte.dbo.Detalle1 (id_linea, id_consumo, id_sede, cantidad, subtotal, id_producto)
SELECT 
    gdb_det.id_linea,      -- Buscamos el nuevo nombre que pusiste con sp_rename
    gdb_det.id_consumo, 
    1, 
    gdb_det.cantidad, 
    gdb_det.subtotal, 
    gdb_det.id_producto
FROM Moodcoffee_GDB.dbo.Detalle_Consumo gdb_det
--WHERE 1 IN (
--    SELECT central_con.id_sede 
--    FROM Moodcoffee_GDB.dbo.Consumo central_con
--    WHERE gdb_det.id_consumo = central_con.id_consumo
--);
--GO
WHERE (
    -- Subconsulta correlacionada que emula el Semijoin (⋉) con Consumo_Operativo1
    SELECT central_con.id_sede 
    FROM Moodcoffee_GDB.dbo.Consumo central_con
    WHERE gdb_det.id_consumo = central_con.id_consumo
) = 1;

SELECT * FROM Detalle1;

```
