## Fragmentacion Mec

## Fragmentacion EstudaintesMateria
```sql

use Mec

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

## Fragmentacion  MateriaMecanica
```sql

-- creacion
create table materiaMEC
(
Idmat char(3) not null,
nommat varchar(50) not null,
horassemana tinyint not null,
Idfac char(3) not null
)

-- PK
alter table materiaMEC add constraint
pk_idmat primary key (Idmat,idfac)
-- FK
alter table materiaMEC add constraint
fk_idfacmatMEC foreign key (Idfac) 
references Facultad(Idfac)

--tabla materiaMEC
insert into Mec.dbo.materiaMEC
select *
FROM [XANDER27HALF].[Facultades].[dbo].[materia]
where idfac='MEC'
select * from materiaMEC

```
