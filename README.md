# Pasos para Utilizar SmartCode

_Después de generar los archivos en el Smart code se deben copiar los archivos en este orden_

## Back End 🚀

1.	Abrir la solución **Zeus.Inventario.Back**

2.	Ir a la carpeta generada por Smart Code **Entities** y copiar el archivo en la raíz del proyecto **Zeus.Inventario.Infrastructure.Entities**

3.	Ir a la carpeta generada por Smart Code **Mappings** y copiar en el proyecto **Zeus.Inventario.Infrastructure** dentro de la carpeta **Mappings**

4.	Editar el archivo **Zeus.Inventario.Infrastructure\Factories\ZeusContextoDB.cs**, ir al final del archivo y asegurarse de crear las líneas de código correspondientes a la tabla en cuestión.
Declarar:
```
public DbSet<Colores> Colores { get; set; }
```
Y en el método **OnModelCreating** y agregar una nueva línea correspondiente a la tabla en cuestión:
```
modelBuilder.ApplyConfiguration(new ColoresMap());
```
5.	Ir a la carpeta generada por Smart Code **BussinessLogic** y copiar el archivo en la raíz del proyecto **Zeus.Inventario.BusinessLogic**

6.	Editar el archivo **Zeus.Inventario.BusinessLogic\Factories\BussinesLogicExtensions.cs**, ir al final del archivo y asegurarse de crear las líneas de código correspondientes a la tabla en cuestión.
```
public static ColoresBusinessLogic ColoresBusinessLogic(this LogicaNegocio logic)
        {
                return new ColoresBusinessLogic(logic.settings);
        }
```
7.	Ir a la carpeta generada por Smart Code **Controllers** y copiar en la siguiente ruta: **Zeus.Inventario.Api\ Controllers**, cabe aclarar que las modificaciones personalizadas de estos objetos se realizarán en la carpeta **Custom**.

Con lo anterior ya se podrían hacer pruebas de la Api, no sin antes se tenga actualizadas las estructuras de la seguridad de .Net, como también los datos de conexión del archivo: **Zeus.Inventario.Api\wwwroot\data\AppConfig.json**
```
[
        {
                "ActivarMultiplsResultSets": true,
                "CatalogoInicial": "InventarioVsDesarrollo",
                "Contrasena": "zeustecinv",
                "FuenteDatos": "localhost\\SQL2016",
                "IdUsuario": "sainventario",
                "Idioma": "es-CO",
                "Nombre": "Desarrollo Inventario",
                "NroConexion": 1,
                "PorDefecto": true,
                "PorInstanciaUsuario": false,
                "SeguridadIntegrada": false,
                "TiempoEsperaConexion": 0,
                "TipoBD": 0
        }
]
```
8.	Crear una clase parcial dentro de la ruta **Zeus.Inventario.Infrastructure.Entities\Custom** de la entidad que se acaba de generar y agregar el método **ToXML()**
