# Sede Central De MoodCooffe

```sql
-- Eliminar la base de datos si existe
USE master;
GO

IF DB_ID('Moodcoffee_GDB') IS NOT NULL
BEGIN
    ALTER DATABASE Moodcoffee_GDB SET SINGLE_USER WITH ROLLBACK IMMEDIATE;
    DROP DATABASE Moodcoffee_GDB;
END
GO

-- Crear base de datos
CREATE DATABASE Moodcoffee_GDB COLLATE Modern_Spanish_CI_AS;
GO

USE Moodcoffee_GDB;
GO

--- Creación de tablas (Limpias, sin restricciones internas)
CREATE TABLE Estado_Animo 
(
    id_estado INT NOT NULL,
    nombre VARCHAR(100) NOT NULL,
    descripcion TEXT
);

CREATE TABLE Cliente 
(
    id_cliente INT NOT NULL,
    nombre VARCHAR(100) NOT NULL,
    apellido VARCHAR(100) NOT NULL,
    cedula VARCHAR(10) NOT NULL,
    telefono VARCHAR(15),
    correo VARCHAR(150)
);

CREATE TABLE Sede 
(
    id_sede INT NOT NULL,
    nombre VARCHAR(100) NOT NULL,
    ciudad VARCHAR(100) NOT NULL,
    direccion VARCHAR(255)
);

CREATE TABLE Producto 
(
    id_producto INT NOT NULL,
    nombre VARCHAR(100) NOT NULL,
    descripcion TEXT,
    tipo VARCHAR(50),
    precio DECIMAL(10, 2) NOT NULL,
    id_estado INT NOT NULL
);

CREATE TABLE Consumo 
(
    id_consumo INT NOT NULL,
    total DECIMAL(10, 2) NOT NULL,
    fecha DATE NOT NULL,
    id_cliente INT NOT NULL,
    id_sede INT NOT NULL
);

CREATE TABLE Detalle_Consumo 
(
    id_consumo INT NOT NULL,
    id_producto INT NOT NULL,
    cantidad INT NOT NULL,
    subtotal DECIMAL(10, 2) NOT NULL
);


------ Creación de claves primarias (PK)
ALTER TABLE Estado_Animo ADD CONSTRAINT pk_id_estado PRIMARY KEY (id_estado);
ALTER TABLE Cliente      ADD CONSTRAINT pk_id_cliente PRIMARY KEY (id_cliente);
ALTER TABLE Sede         ADD CONSTRAINT pk_id_sede PRIMARY KEY (id_sede);
ALTER TABLE Producto     ADD CONSTRAINT pk_id_producto PRIMARY KEY (id_producto);
ALTER TABLE Consumo      ADD CONSTRAINT pk_id_consumo PRIMARY KEY (id_consumo);

-- PK de la Entidad Débil: Compuesta por herencia de asociación (Consumo + Producto)
ALTER TABLE Detalle_Consumo ADD CONSTRAINT pk_detail_consumo PRIMARY KEY (id_consumo, id_producto);


------ Creación de claves foráneas (FK)
ALTER TABLE Producto ADD CONSTRAINT fk_producto_estado FOREIGN KEY (id_estado) REFERENCES Estado_Animo(id_estado);

ALTER TABLE Consumo ADD CONSTRAINT fk_consumo_cliente FOREIGN KEY (id_cliente) REFERENCES Cliente(id_cliente);
ALTER TABLE Consumo ADD CONSTRAINT fk_consumo_sede FOREIGN KEY (id_sede) REFERENCES Sede(id_sede);

-- Relaciones de la Entidad Débil
ALTER TABLE Detalle_Consumo ADD CONSTRAINT fk_detail_consumo FOREIGN KEY (id_consumo) REFERENCES Consumo(id_consumo) ON DELETE CASCADE;
ALTER TABLE Detalle_Consumo ADD CONSTRAINT fk_detail_producto FOREIGN KEY (id_producto) REFERENCES Producto(id_producto);


--- Ingreso de datos maestros (Para la futura replicación)
-- IDs de estados normales y fáciles (1 al 4)
INSERT INTO Estado_Animo VALUES (1, 'Estresado', 'Alta carga de trabajo o estudio, requiere relajación');
INSERT INTO Estado_Animo VALUES (2, 'Feliz', 'Estado de ánimo alegre, ideal para compartir o celebrar');
INSERT INTO Estado_Animo VALUES (3, 'Cansado', 'Baja energía, requiere un impulso de cafeína');
INSERT INTO Estado_Animo VALUES (4, 'Productivo', 'Enfocado en trabajar o estudiar, alta concentración');

-- IDs de sedes normales y fáciles (1 y 2)
INSERT INTO Sede VALUES (1, 'Sede Norte', 'Quito', 'Av. Amazonas y Gaspar de Villarroel');
INSERT INTO Sede VALUES (2, 'Sede Sur', 'Quito', 'Av. Maldonado y El Recreo');

-- IDs de productos normales y fáciles (1 al 5)
INSERT INTO Producto VALUES (1, 'Té de Manzanilla', 'Infusión relajante natural', 'Bebida Caliente', 1.50, 1);
INSERT INTO Producto VALUES (2, 'Muffin de Chocolate', 'Postre con extra de cacao', 'Pastelería', 2.50, 1);
INSERT INTO Producto VALUES (3, 'Frappé de Oreo', 'Bebida fría dulce con crema', 'Bebida Fría', 3.50, 2);
INSERT INTO Producto VALUES (4, 'Americano Doble', 'Café negro intenso para despertar', 'Bebida Caliente', 2.00, 3);
INSERT INTO Producto VALUES (5, 'Capuccino Vainilla', 'Café con leche espumoso y vainilla', 'Bebida Caliente', 2.80, 4);

-- IDs de clientes (Exactamente 12 inserciones: 6 Norte y 6 Sur)
-- CLIENTES ASOCIADOS A SEDE NORTE (IDs 1 al 6)
INSERT INTO Cliente VALUES (1, 'Juan', 'Perez', '1700000001', '0990000001', 'juan.perez.norte@epn.edu.ec');
INSERT INTO Cliente VALUES (2, 'Carlos', 'Lopez', '1700000002', '0990000002', 'carlos.lopez@epn.edu.ec');
INSERT INTO Cliente VALUES (3, 'Ana', 'Gomez', '1700000003', '0990000003', 'ana.gomez@epn.edu.ec');
INSERT INTO Cliente VALUES (4, 'David', 'Andrade', '1700000004', '0990000004', 'david.andrade@epn.edu.ec');
INSERT INTO Cliente VALUES (5, 'Elena', 'Cevallos', '1700000005', '0990000005', 'elena.cevallos@epn.edu.ec');
INSERT INTO Cliente VALUES (6, 'Francisco', 'Torres', '1700000006', '0990000006', 'francisco.torres@epn.edu.ec');

-- CLIENTES ASOCIADOS A SEDE SUR (IDs 7 al 12)
INSERT INTO Cliente VALUES (7, 'Lenin', 'Amangandi', '1726543210', '0998765432', 'lenin.amangandi@epn.edu.ec');
INSERT INTO Cliente VALUES (8, 'Juan', 'Perez', '1700000008', '0990000008', 'juan.perez.sur@epn.edu.ec');
INSERT INTO Cliente VALUES (9, 'Luis', 'Martinez', '1700000009', '0990000009', 'luis.martinez@epn.edu.ec');
INSERT INTO Cliente VALUES (10, 'Maria', 'Rodriguez', '1700000010', '0990000010', 'maria.rodriguez@epn.edu.ec');
INSERT INTO Cliente VALUES (11, 'Gabriela', 'Suarez', '1700000011', '0990000011', 'gabriela.suarez@epn.edu.ec');
INSERT INTO Cliente VALUES (12, 'Hugo', 'Benavides', '1700000012', '0990000012', 'hugo.benavides@epn.edu.ec');


--- Ingreso de consumos balanceados (12 transacciones en total)

-- ==========================================
-- TRANSACCIONES EN SEDE 1 (NORTE) - Clientes 1 al 6
-- ==========================================
-- Consumo 1: Juan Perez (Cliente 1) compra un Frappé (Producto 3)
INSERT INTO Consumo VALUES (1, 3.50, '2026-05-31', 1, 1);
INSERT INTO Detalle_Consumo VALUES (1, 3, 1, 3.50);

-- Consumo 2: Carlos Lopez (Cliente 2) compra un Americano (Producto 4)
INSERT INTO Consumo VALUES (2, 2.00, '2026-05-31', 2, 1);
INSERT INTO Detalle_Consumo VALUES (2, 4, 1, 2.00);

-- Consumo 3: Ana Gomez (Cliente 3) compra un Té (Producto 1)
INSERT INTO Consumo VALUES (3, 1.50, '2026-06-01', 3, 1);
INSERT INTO Detalle_Consumo VALUES (3, 1, 1, 1.50);

-- Consumo 4: David Andrade (Cliente 4) compra un Capuccino (Producto 5)
INSERT INTO Consumo VALUES (4, 2.80, '2026-06-01', 4, 1);
INSERT INTO Detalle_Consumo VALUES (4, 5, 1, 2.80);

-- Consumo 5: Elena Cevallos (Cliente 5) compra un Muffin (Producto 2)
INSERT INTO Consumo VALUES (5, 2.50, '2026-06-02', 5, 1);
INSERT INTO Detalle_Consumo VALUES (5, 2, 1, 2.50);

-- Consumo 6: Francisco Torres (Cliente 6) compra un Americano (Producto 4)
INSERT INTO Consumo VALUES (6, 2.00, '2026-06-02', 6, 1);
INSERT INTO Detalle_Consumo VALUES (6, 4, 1, 2.00);


-- ==========================================
-- TRANSACCIONES EN SEDE 2 (SUR) - Clientes 7 al 12
-- ==========================================
-- Consumo 7: Lenin Amangandi (Cliente 7) compra Té (Producto 1) y Capuccino (Producto 5)
INSERT INTO Consumo VALUES (7, 4.30, '2026-05-31', 7, 2);
INSERT INTO Detalle_Consumo VALUES (7, 1, 1, 1.50);
INSERT INTO Detalle_Consumo VALUES (7, 5, 1, 2.80);

-- Consumo 8: Juan Perez Sur (Cliente 8) compra un Frappé (Producto 3)
INSERT INTO Consumo VALUES (8, 3.50, '2026-05-31', 8, 2);
INSERT INTO Detalle_Consumo VALUES (8, 3, 1, 3.50);

-- Consumo 9: Luis Martinez (Cliente 9) compra un Capuccino (Producto 5)
INSERT INTO Consumo VALUES (9, 2.80, '2026-06-01', 9, 2);
INSERT INTO Detalle_Consumo VALUES (9, 5, 1, 2.80);

-- Consumo 10: Maria Rodriguez (Cliente 10) compra 2 Frappés (Producto 3)
INSERT INTO Consumo VALUES (10, 7.00, '2026-06-02', 10, 2);
INSERT INTO Detalle_Consumo VALUES (10, 3, 2, 7.00);

-- Consumo 11: Gabriela Suarez (Cliente 11) compra un Muffin (Producto 2)
INSERT INTO Consumo VALUES (11, 2.50, '2026-06-02', 11, 2);
INSERT INTO Detalle_Consumo VALUES (11, 2, 1, 2.50);

-- Consumo 12: Hugo Benavides (Cliente 12) compra un Americano (Producto 4)
INSERT INTO Consumo VALUES (12, 2.00, '2026-06-02', 12, 2);
INSERT INTO Detalle_Consumo VALUES (12, 4, 1, 2.00);


--- Verificación final
SELECT * FROM Estado_Animo;
SELECT * FROM Sede;
SELECT * FROM Producto;
SELECT * FROM Cliente;
SELECT * FROM Consumo;
SELECT * FROM Detalle_Consumo;
```
