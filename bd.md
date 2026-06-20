# ETAPA 1. CREAR LA BASE CENTRAL (M)
## Crear la BD central.

```
CREATE DATABASE Coffee_Central
GO

USE Coffee_Central
GO
```
## Crear tablas globales
```
USE Coffee_Central;
GO

/*======================================================
TABLA SEDE
======================================================*/

CREATE TABLE Sede
(
    id_sede CHAR(2) NOT NULL,
    nombre VARCHAR(100) NOT NULL,
    direccion VARCHAR(150) NOT NULL,
    ciudad VARCHAR(100) NOT NULL,

    CONSTRAINT PK_Sede
    PRIMARY KEY(id_sede)
);
GO

/*======================================================
TABLA CLIENTE
======================================================*/

CREATE TABLE Cliente
(
    id_cliente INT NOT NULL,
    nombre VARCHAR(50) NOT NULL,
    apellido VARCHAR(50) NOT NULL,
    cedula VARCHAR(15) NOT NULL,
    telefono VARCHAR(20),
    correo VARCHAR(100),

    CONSTRAINT PK_Cliente
    PRIMARY KEY(id_cliente),

    CONSTRAINT UQ_ClienteCedula
    UNIQUE(cedula)
);
GO

/*======================================================
TABLA ESTADO_ANIMO
======================================================*/

CREATE TABLE Estado_Animo
(
    id_estado INT NOT NULL,
    nombre VARCHAR(50) NOT NULL,
    descripcion VARCHAR(200),

    CONSTRAINT PK_EstadoAnimo
    PRIMARY KEY(id_estado)
);
GO

/*======================================================
TABLA PRODUCTO
======================================================*/

CREATE TABLE Producto
(
    id_producto INT NOT NULL,
    nombre VARCHAR(100) NOT NULL,
    descripcion VARCHAR(200),
    tipo VARCHAR(50),
    precio DECIMAL(10,2) NOT NULL,
    id_estado INT NOT NULL,

    CONSTRAINT PK_Producto
    PRIMARY KEY(id_producto),

    CONSTRAINT FK_ProductoEstado
    FOREIGN KEY(id_estado)
    REFERENCES Estado_Animo(id_estado)
);
GO

/*======================================================
TABLA CONSUMO
======================================================*/

CREATE TABLE Consumo
(
    id_consumo INT NOT NULL,
    fecha DATE NOT NULL,
    total DECIMAL(10,2) NOT NULL,
    id_cliente INT NOT NULL,
    id_sede CHAR(2) NOT NULL,

    CONSTRAINT PK_Consumo
    PRIMARY KEY(id_consumo),

    CONSTRAINT FK_ConsumoCliente
    FOREIGN KEY(id_cliente)
    REFERENCES Cliente(id_cliente),

    CONSTRAINT FK_ConsumoSede
    FOREIGN KEY(id_sede)
    REFERENCES Sede(id_sede)
);
GO

/*======================================================
TABLA DETALLE_CONSUMO
======================================================*/

CREATE TABLE Detalle_Consumo
(
    id_detalle INT NOT NULL,
    id_consumo INT NOT NULL,
    cantidad INT NOT NULL,
    subtotal DECIMAL(10,2) NOT NULL,
    id_producto INT NOT NULL,

    CONSTRAINT PK_DetalleConsumo
    PRIMARY KEY(id_detalle),

    CONSTRAINT FK_DetalleConsumo
    FOREIGN KEY(id_consumo)
    REFERENCES Consumo(id_consumo),

    CONSTRAINT FK_DetalleProducto
    FOREIGN KEY(id_producto)
    REFERENCES Producto(id_producto)
);
GO
```
# ETAPA 2. CARGAR DATOS


## Testeo Bidereccional Mec

```sql
use MEC
Select * from mytable
```
