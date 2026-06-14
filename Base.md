/--------------------- MoodCooffe_BDSUR ------------------/

-- Esquema de replicaci�n. 
/* tabla facultad, replicaci�n unidireccional */

/--------------------------------------------/

-- Eliminar la base de datos si existe
USE master;
GO

IF DB_ID('MoodCooffe_BDSUR') IS NOT NULL
BEGIN
    ALTER DATABASE MoodCooffe_BDSUR SET SINGLE_USER WITH ROLLBACK IMMEDIATE;
    DROP DATABASE MoodCooffe_BDSUR;
END
GO



use master
create database MoodCooffe_BDSUR COLLATE Modern_Spanish_CI_AS
go
use MoodCooffe_BDSUR
