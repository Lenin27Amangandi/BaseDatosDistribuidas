# MoodCooffe_DBNorte

/*--------------------- MoodCoffee_DBNorte ------------------*/
create database MoodCoffee_DBNorte COLLATE Modern_Spanish_CI_AS
go
use MoodCoffee_DBNorte
/*--------------------------------------------*/
-- Creacion de Tablas Nodo Norte
/*--------------------------------------------*/


/*--------------------------------------------*/
-- Creacion Sede
/*--------------------------------------------*/
create table Sede
(
id_sede int not null,
nombre varchar(100) not null,
ciudad varchar(100) not null,
direccion varchar(255)
)
-- PK Sede
alter table Sede add constraint 
pk_id_sede primary key (id_sede)


/*--------------------------------------------*/
-- Creacion Estado_Animo
/*--------------------------------------------*/
create table Estado_Animo
(
id_estado int not null,
nombre varchar(100) not null,
descripcion text
)
-- PK Estado Animo
alter table Estado_Animo add constraint 
pk_id_estado primary key (id_estado)


/*--------------------------------------------*/
-- Creacion Producto
/*--------------------------------------------*/
create table Producto
(
id_producto int not null,
nombre varchar(100) not null,
descripcion text,
tipo varchar(50),
precio decimal(10,2) not null,
id_estado int not null
)
-- PK Producto
alter table Producto add constraint 
pk_id_producto primary key (id_producto)
-- FK Producto
alter table Producto add constraint 
fk_producto_estado foreign key (id_estado) 
references Estado_Animo(id_estado)



/*--------------------------------------------*/
-- Creacion Cliente_Operativo
/*--------------------------------------------*/
create table Cliente_Operativo
(
id_cliente int not null,
nombre varchar(100) not null,
apellido varchar(100) not null
)
-- PK Cliente Operativo
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


/*--------------------------------------------*/
-- FRAGMENTACIONES HORIZONTALES EN MOODCOFFEE (LO QUE FALTA)
-- FRAGMENTACION HORIZONTAL PRIMARIA
-- Añadir el campo de fragmentación (id_sede) a la PK de la tabla
/*--------------------------------------------*/

create table Consumo_Operativo1
(
id_consumo int not null,
total decimal(10,2) not null,
fecha date not null,
id_cliente int not null,
id_sede int not null -- Campo de fragmentación añadido
)

/*--------------------------------------------*/
-- FRAGMENTACION HORIZONTAL DERIVADA
-- Añadimos el campo de fragmentación (id_sede) a la tabla
-- y se añade ese campo a la PK de la tabla
/*--------------------------------------------*/

create table Detalle1
(
id_consumo int not null,
id_producto int not null,
id_sede int not null, -- Campo añadido para la derivada
cantidad int not null,
subtotal decimal(10,2) not null
)

/*--------------------------------------------*/
-- Creación de PKs y FKs de Fragmentación
/*--------------------------------------------*/

-- Consumo Operativo 1 (Horizontal Primaria)
alter table Consumo_Operativo1 add constraint
pk_id_consumo_idsede primary key (id_consumo, id_sede)

alter table Consumo_Operativo1 add constraint
fk_idsede_cons1 foreign key (id_sede)
references Sede(id_sede)

alter table Consumo_Operativo1 add constraint
fk_idcliente_cons1 foreign key (id_cliente)
references Cliente_Operativo(id_cliente)

-- Detalle 1 (Horizontal Derivada)
alter table Detalle1 add constraint
pk_idconsumo_idprod_idsede primary key (id_consumo, id_producto, id_sede)

alter table Detalle1 add constraint
fk_idconsumo_idsede_det1 foreign key (id_consumo, id_sede)
references Consumo_Operativo1(id_consumo, id_sede) on delete cascade

alter table Detalle1 add constraint
fk_idproducto_det1 foreign key (id_producto)
references Producto(id_producto)

/*--------------------------------------------*/	   
--- LISTO TODOS LOS REGISTROS DE FRAGMENTACIÓN
/*--------------------------------------------*/

select * from Consumo_Operativo1
select * from Detalle1
