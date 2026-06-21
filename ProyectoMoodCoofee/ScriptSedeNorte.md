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
