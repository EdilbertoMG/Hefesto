# Crear Opciones de Configuraciones

1.	En Sql crea una vista para tu Opcion con los campos que se van a mostrar.
```sql
--liquibase formatted sql
--changeset emarrugo:1 dbms:mssql runOnChange:true stripComments:false endDelimiter:GO
if exists (select * from dbo.sysobjects where id = object_id(N'[dbo].[ConfigDctoClienteVsArticulo]') and OBJECTPROPERTY(id, N'IsView') = 1)
drop view [dbo].[ConfigDctoClienteVsArticulo]
GO
/*
select * from [ConfigDctoClienteVsArticulo]
*/
Create view  [dbo].[ConfigDctoClienteVsArticulo]
With Encryption
AS
	Select 
		  Codigo = '1'
		, Nombre = ' '
		
GO
```
2.	Si tu opcion tiene Grilla tambien debes crearla como normalmente se hace.

3.	

4.	Editar el archivo **Zeus.Inventario.Infrastructure\Factories\ZeusContextoDB.cs**, ir al final del archivo y asegurarse de crear las líneas de código correspondientes a la tabla en cuestión.
Declarar:
```c#
public DbSet<Colores> Colores { get; set; }
```
