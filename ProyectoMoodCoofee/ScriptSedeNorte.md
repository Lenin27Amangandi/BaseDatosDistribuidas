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

create table Cliente_Operativo
(
id_cliente int not null,
nombre varchar(100) not null,
apellido varchar(100) not null
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

-- Cliente Operativo
alter table Cliente_Operativo add constraint 
pk_id_cliente_operativo primary key (id_cliente)

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

 -- tabla Cliente_Operativo
insert into MoodCoffee_DBNorte.dbo.Cliente_Operativo
select * from Moodcoffee_GDB.dbo.Cliente_Operativo
select * from Cliente_Operativo

select * from Sede
select * from Estado_Animo
select * from Producto
select * from Cliente_Operativo
```

## Fragmetacion

```sql
--- Fragmetacion vertical
use MoodCoffee_DBNorte

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
--              Consumo_Operativo_Base
-- ==========================================

----CREATE TABLE Consumo 
----(
----    id_consumo INT NOT NULL,
----    fecha DATE NOT NULL,
----    id_cliente INT NOT NULL,
----    id_sede INT NOT NULL
----);
drop table Consumo_Operativo_Base
CREATE TABLE Consumo_Operativo_Base 
(
    id_consumo INT NOT NULL,
    fecha DATE NOT NULL,
    id_cliente INT NOT NULL,
    id_sede INT NOT NULL
);

alter table Consumo_Operativo_Base add constraint 
pk_id_consumo_OB primary key (id_consumo)

-- Insertar datos 
insert into MoodCoffee_DBNorte.dbo.Consumo_Operativo_Base
select id_consumo,fecha,id_cliente,id_sede from Moodcoffee_GDB.dbo.Consumo

select * from Consumo_Operativo_Base


-- ==========================================
--              Consumo_Operativo1
-- ==========================================

-- Se crea con la misma estructura que la base
drop table Consumo_Operativo1
CREATE TABLE Consumo_Operativo1 (
    id_consumo INT NOT NULL,
    fecha DATE NOT NULL,
    id_cliente INT NOT NULL,
    id_sede INT NOT NULL
);

alter table Consumo_Operativo1 add constraint 
pk_id_consumo_id_sede primary key (id_consumo,id_sede)


-- Inserción aplicando la condición de fragmentación horizontal: σ id_sede = 1
INSERT INTO Consumo_Operativo1 (id_consumo, fecha, id_cliente, id_sede)
SELECT id_consumo, fecha, id_cliente, id_sede
FROM Consumo_Operativo_Base
WHERE id_sede = 1;

-- Verificación del fragmento
SELECT * FROM Consumo_Operativo1;


```
