## Posibles Cambios
Si se corrige la referencia en el norte en el cual para el norte conusmo opertativo no apunte a las 2 tablas cleintes sino a la de clienteopertativo y la de cliente operativo apunta a cliente datos
```sql
USE MoodCoffee_DBNorte;
GO

-- 1. Eliminar la FK redundante de Consumo_Operativo1 hacia Cliente_Datos
ALTER TABLE Consumo_Operativo1
DROP CONSTRAINT fk_consumo1_cliente_datos;
GO

-- 2. Crear la nueva FK entre los dos fragmentos verticales de Cliente
ALTER TABLE Cliente_Datos
ADD CONSTRAINT fk_clientedatos_clienteoperativo FOREIGN KEY (id_cliente) 
REFERENCES Cliente_Operativo(id_cliente);
GO
```
