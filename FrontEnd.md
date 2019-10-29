_Después de generar el Back End se deben copiar los archivos en este orden_

## Front End 🛠️

1.	Abrir la solución **Zeus.Inventario.Front**.

2.	Ir a la carpeta generada por Smart Code **Models** y copiar en la siguiente ruta: **Zeus.Inventario.UI.Data\Models**, cualquier cambio en este archivo debe hacerse en la carpeta **Custom**.

3.	Ir a la carpeta generada por Smart Code **Proxies** y copiar el archivo en la siguiente ruta **Zeus.Inventario.UI.Data\Proxies** , cualquier cambio en este archivo debe hacerse en la carpeta **Custom**.

4.	Editar el archivo **Zeus.Inventario.UI.Data\Factories\ZeusProxyBusinessLogicExtentions.cs**, ir al final del archivo y asegurarse de crear las líneas de código correspondientes a la tabla en cuestión.
```
        public static ColoresBusinessLogic ColoresBusinessLogic(this ProxyLogicaNegocio proxy)
        {
                return new ColoresBusinessLogic(proxy.ServiceAddresUnkown, proxy.Token);
        }
```        
5.	Ir a la carpeta generada por Smart Code **UIControllers** y copiar el archivo en la siguiente ruta **Zeus.Inventario.UI.WebApp\ Controllers** , cualquier cambio en este archivo debe hacerse en la carpeta **Custom**.

6.	Ir a la carpeta generada por Smart Code **UIView** y copiar la carpeta en la siguiente ruta **Zeus.Inventario.UI.WebApp\Views**, se crearán 4 archivos.

7.	Editar el archivo **../wwwroot/data/AppMenu.json** Agregar la opción de menú.
Importante es que estas opciones de menú se les configura el controlador y la acción, para los maestros y los documentos la acción será **List**, que corresponden a la ventana principal que muestra una cuadricula.
```  
{
                                "Name": "Configuración de datos específicos",
                                "NameEn": "Specific data settings",
                                "Description": "Configuración de datos específicos",
                                "DescriptionEn": "Specific data settings",
                                "Privileges": [],
                                "Items": [
                                        {
                                                "Name": "Tipos de Máquinas",
                                                "NameEn": "Types of Machines",
                                                "Description": "Tipos de Máquinas",
                                                "DescriptionEn": "Types of Machines",
                                                "Controller": "ProduccionTipoMaquina",
                                                "Action": "List",
                                                "Privileges": []
                                        }
}                                        
```  
8.	Ir al proyecto **Zeus.Inventario.UI.WebApp.csproj** y editar la vista **XXXXXXXXList.cshtml** con el objetivo de solo mostrar las columnas relevantes, por ejemplo, eliminar las columna **iden**.
<img src="https://github.com/EdilbertoMG/Hefesto/blob/master/Imagenes/EliminarIden.png" alt="Eliminar Iden">

Algo muy importante es que si se piensa mostrar el nombre de un foráneo en particular hay que hacer lo siguiente: 
•	El control **Html.Zeus().DataGridBasi** apunta al controlador GET del objeto.

<img src="https://github.com/EdilbertoMG/Hefesto/blob/master/Imagenes/Controlador%20Get%20del%20Objeto.jpg" alt="Controlador Get del Objeto">

•	Este a su vez llama al proxi del objeto en el método **Find** el cual se comunica con la API en la ruta **GetByOptions**.
<img src="https://github.com/EdilbertoMG/Hefesto/blob/master/Imagenes/M%C3%A9todo%20Find.jpg" alt="Metodo Find">

•	Para lograr obtener la información de los foráneos se debe agregar los **Include** correspondientes.

<img src="https://github.com/EdilbertoMG/Hefesto/blob/master/Imagenes/Include.jpg" alt="Includes">

•	Por último, configurar en la grilla el nombre del **Foraneo**.

<img src="https://github.com/EdilbertoMG/Hefesto/blob/master/Imagenes/Foraneo.jpg" alt="Foraneo">

9.	Editar la vista **XXXXXXEdit.cshtml**, hay que tener en cuenta varias cosas:

•	Agregar la siguiente instrucción al principio de la página.
``` 
@inject ILanguageResource languageResource
``` 
•	Todos los campos del objeto deben tener su correspondiente control en la forma, los que no se mostraran visualmente deben estar representados con el control **@Html.HiddenFor**.
``` 
@Html.HiddenFor(m => m.NOMBREDELCAMPO, new { id = ViewBag.PrefixNOMBREOBJETO + "_NOMBREDELCAMPO" }).
``` 
•	Para aquellas tablas donde la llave primaria es un **Iden**, se debe eliminar la condición 
**@if (!Model.EsNuevo){}** pues de todas formas este control no se va a visualizar y de inmediato se debe agregar su correspondiente **@Html.HiddenFor**.

10.	Crear buscador en la base de datos: se debe editar el archivo **z2999999 DatosPorDefault_Hefesto_Buscadores.sql** y agregar el nuevo buscador del objeto creado.
``` 
-- ProduccionTipoMaquina
Execute spSistema_Hefesto @Op = 'ActualizaBuscador',
@xml = 
'
<Buscador>
	<Codigo>ProduccionTipoMaquina</Codigo>
	<Nombre>Tipos de máquinas en producción</Nombre>
	<CampoDevolver>Codigo</CampoDevolver>
	<SQL>
		Select  Codigo, 
			Nombre 
		From	ProduccionTipoMaquina
	</SQL>
	<SqlChoice>
		Select	ProduccionTipoMaquina.* 
		From	ProduccionTipoMaquina 
		Where	Codigo = @CODE
	</SqlChoice>
	<Anchos>20,70</Anchos>
	<Avanzada>
		<Campo>
			<Nombre>Codigo</Nombre>
			<Titulo>Código</Titulo>
			<Tipo>varchar</Tipo>
		</Campo>
		<Campo>
			<Nombre>Nombre</Nombre>
			<Titulo>Nombre</Titulo>
			<Tipo>varchar</Tipo>
		</Campo>
	</Avanzada>
</Buscador>
'
GO
``` 
11.	Si la llave primaria es un **Iden**, para que las búsquedas por código se logren hacer hay agregar al proyecto **Zeus.Inventario.UI.Data\Proxies\Custom** una copia del archivo **NOMBREOBJETOBusinessLogic.cs** y agregar un nuevo método de consulta a la API, este debe coincidir con la ruta que se tuvo que agregar en el Back End, que normalmente se llama **GetByCode**.
Lo mejor es crear una sobrecarga del **FindById** que existe.
