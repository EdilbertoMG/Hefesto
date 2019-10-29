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

Algo muy importante es que si se piensa mostrar el nombre de un foráneo en particular hay que hacer lo siguiente: 
•	El control **Html.Zeus().DataGridBasi** apunta al controlador GET del objeto.

<img src="https://raw.githubusercontent.com/EdilbertoMG/Hefesto/master/Imagenes/Controlador%20Get%20del%20Objeto.jpg?token=AHN7IGCCNYPA5GHI5XBPSZS5XA3XI" alt="Controlador Get del Objeto">

•	Este a su vez llama al proxi del objeto en el método **Find** el cual se comunica con la API en la ruta **GetByOptions**.
<img src="https://raw.githubusercontent.com/EdilbertoMG/Hefesto/master/Imagenes/M%C3%A9todo%20Find.jpg?token=AHN7IGGCJOH3DIMDPREM2NS5XA37C" alt="Metodo Find">

•	Para lograr obtener la información de los foráneos se debe agregar los **Include** correspondientes.

<img src="https://raw.githubusercontent.com/EdilbertoMG/Hefesto/master/Imagenes/Include.jpg?token=AHN7IGE7TF42N6UHI5KAOC25XA4KQ" alt="Includes">

•	Por último, configurar en la grilla el nombre del **Foraneo**.

<img src="https://raw.githubusercontent.com/EdilbertoMG/Hefesto/master/Imagenes/Foraneo.jpg?token=AHN7IGCEVEVU7R4KD7XOWYK5XA45U" alt="Foraneo">
