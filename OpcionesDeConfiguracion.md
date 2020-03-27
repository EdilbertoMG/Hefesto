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
		  Codigo = ' '
		, Nombre = ' '
		
GO
```
2.	Si tu opcion tiene Grilla tambien debes crearla como normalmente se hace.

3.	Creamos el **Back** y el **Front** normalmente

4.	En el Controlador del Front hacemos que nuestro List retorne el Edit Model
```c#
public IActionResult List()
		{
			//return View("ConfigDctoClienteVsArticuloList");
			return View("ConfigDctoClienteVsArticuloEdit", GetNewModel());
		}
```
