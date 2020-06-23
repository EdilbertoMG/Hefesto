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
			return return NewDetail();
		}
```

4.	En la carpeta de las vista la __ConfigDctoClienteVsArticulo.cshtml, poniendo esto ocultas los botones que no necesites
```c#
@(Html.Zeus().ToolbarZeus("ConfigComisionesDescuentoRecaudoPorVendedorToolbar",
									"#mainPanel",
									"Comisión Rentab vs Recaudo",
									null,
									null,
									null,
									null,
									null,
									null,
									null,
									null,
									false,
									null,
									false,
									null,
									languageResource.Language))
```
```js
$("#ConfigDctoClienteVsArticuloToolbar_btnSave").hide();
		$(".master-subtitle > span > small").html("");
```
