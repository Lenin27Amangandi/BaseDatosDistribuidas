## Fragmentacion Mec


```sql

/*--------------------------------------------*/
-- FRAMENTACIONES HORIZONTALES EN MEC
-- FRAGMENTACION HORIZONTAL PRIMARIA
-- Aniaadir el campo de fragmentacion
-- A la PK de la table
/*--------------------------------------------*/


create table estudianteMEC
(cc char(10) not null,
numunic char(10) not null,
nombre varchar(50) not null,
apellido varchar(50) not null,
idfac char(3) not null
)
-- PK
alter table estudianteMEC add constraint 
pk_cc_idfac primary key (cc,idfac)
-- FK
alter table estudianteMEC add constraint 
fk_idfacestMEC foreign key (idfac) 
references facultad(idfac)

-- RECUPERAR DATOS DE Mec

-- tabla estudianteMEC
-- insert into mec.dbo.estudianteMEC
insert into Mec.dbo.estudianteMEC
SELECT cc,numunic,nombre,apellido,idfac
FROM [XANDER27HALF].[Facultades].[dbo].[estudiante]
where idfac='MEC'

select * from estudianteMEC

```
