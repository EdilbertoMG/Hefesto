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
<img src="https://raw.githubusercontent.com/EdilbertoMG/Hefesto/master/Imagenes/EliminarIden.png?token=AHN7IGFKI64P2FHU3QASQN25XA236" alt="Eliminar Iden">
