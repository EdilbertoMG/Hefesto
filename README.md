# Pasos para Utilizar SmartCode

_Después de generar los archivos en el Smart Code se deben copiar los archivos en este orden_

## Back End 🚀

1.	Abrir la solución **Zeus.Inventario.Back**

2.	Ir a la carpeta generada por Smart Code **Entities** y copiar el archivo en la raíz del proyecto **Zeus.Inventario.Infrastructure.Entities**

3.	Ir a la carpeta generada por Smart Code **Mappings** y copiar en el proyecto **Zeus.Inventario.Infrastructure** dentro de la carpeta **Mappings**

4.	Editar el archivo **Zeus.Inventario.Infrastructure\Factories\ZeusContextoDB.cs**, ir al final del archivo y asegurarse de crear las líneas de código correspondientes a la tabla en cuestión.
Declarar:
```c#
public DbSet<Colores> Colores { get; set; }
```
Y en el método **OnModelCreating** y agregar una nueva línea correspondiente a la tabla en cuestión:
```c#
modelBuilder.ApplyConfiguration(new ColoresMap());
```
5.	Ir a la carpeta generada por Smart Code **BussinessLogic** y copiar el archivo en la raíz del proyecto **Zeus.Inventario.BusinessLogic**

6.	Editar el archivo **Zeus.Inventario.BusinessLogic\Factories\BussinesLogicExtensions.cs**, ir al final del archivo y asegurarse de crear las líneas de código correspondientes a la tabla en cuestión.
```c#
public static ColoresBusinessLogic ColoresBusinessLogic(this LogicaNegocio logic)
        {
                return new ColoresBusinessLogic(logic.settings);
        }
```
7.	Ir a la carpeta generada por Smart Code **Controllers** y copiar en la siguiente ruta: **Zeus.Inventario.Api\ Controllers**, cabe aclarar que las modificaciones personalizadas de estos objetos se realizarán en la carpeta **Custom**.

Con lo anterior ya se podrían hacer pruebas de la Api, no sin antes se tenga actualizadas las estructuras de la seguridad de .Net, como también los datos de conexión del archivo: **Zeus.Inventario.Api\wwwroot\data\AppConfig.json**
```c#
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
```c#
using System;
using System.IO;
using System.Xml;
using System.Xml.Serialization;

namespace Zeus.Inventario.Infrastructure.Entities
{
    public partial class ProduccionTipoMaquina 
    {
        public string ToXML()
        {
                if (this == null)
                {
                        return string.Empty;
                }
                try
                {
                        var xmlserializer = new XmlSerializer(typeof(ProduccionTipoMaquina));
                        var stringWriter = new StringWriter();
                        var settings = new XmlWriterSettings();
                        settings.Indent = true;
                        settings.OmitXmlDeclaration = true;
                        using (var writer = XmlWriter.Create(stringWriter, settings))
                        {
                                xmlserializer.Serialize(writer, this);
                                return stringWriter.ToString();
                        }
                }
                catch (Exception ex)
                {
                        throw new Exception("An error occurred", ex);
                }
        }

    }
}
```
9.	Crear una clase parcial dentro de la ruta **Zeus.Inventario.BusinessLogic\Custom** de la entidad que se acaba de generar y sobrescribir los métodos: **Adicionar(), Modificar(), Eliminar(), ExecuteProcedure()**
Importante aclarar que ya se debe tener claro cuál es el **Iden** del spwsg asignado que en el ejemplo siguiente es el **153**
```c#
using System.Linq;
using Hefesto.Backend.Core.Persistencia;
using Zeus.Inventario.Infrastructure.Entities;
using System.Collections.Generic;

namespace Zeus.Inventario.BusinessLogic
{
        public partial class Concepto_INCsBusinessLogic
        {
                #region Reglas de negocio

                public override Concepto_INCs Adicionar(Concepto_INCs data)
                {
                        ValidateRules(ReglasAdicionar, data);
                        return ExecuteOperation("I", data);
                }

                public override Concepto_INCs Modificar(Concepto_INCs data)
                {
                        ValidateRules(ReglasAdicionar, data);
                        return ExecuteOperation("U", data);
                }

                public override Concepto_INCs Eliminar(Concepto_INCs data)
                {
                        ValidateRules(ReglasAdicionar, data);
                        return ExecuteOperation("D", data);
                }

                protected override Concepto_INCs ExecuteProcedure(string operation, Concepto_INCs data)
                {
                        if (MensajesExcepcion.Any())
                        {
                                data.Errores.AddRange(MensajesExcepcion);
                        }
                        else
                        {
                                EjecutarProcedimientoAlmacenado("spAPI_Inventario", new List<ParametroBd>{
                                new ParametroBd("@OP", operation),
                                new ParametroBd("@Iden", "153"),
                                new ParametroBd("@XML", data.ToXML())
                        });
                        }

                        return data;
                }

                #endregion

        }
}
```
## SI LA LLAVE PRIMARIA ES UN IDEN
10.	Crear una clase parcial dentro de la ruta **Zeus.Inventario.Api\Controllers\Custom** de la entidad que se acaba de generar y agregar un controlador de búsqueda por código.
Esta búsqueda siempre hará referencia a la llave visual de la entidad en los casos donde la llave primaria es un **Iden**, para aquellos casos donde la llave primaria es la misma llave visual no es necesario crear este código.
```c#
using Microsoft.AspNetCore.Mvc;
using Zeus.Inventario.BusinessLogic.Factories;
using Zeus.Inventario.Infrastructure.Entities;

namespace Zeus.Inventario.Api.Controllers
{
    public partial class ProduccionTipoMaquinaController 
    {
        /// <summary>
        /// Consulta un registro por su llave visual
        /// </summary>
        /// <param name="Codigo"></param>
        /// <returns>El ProduccionTipoMaquina</returns>
        [HttpGet]
        [Route("GetByCode")]
        public ProduccionTipoMaquina GetByCode(string Codigo)
        {
            return Manager().ProduccionTipoMaquinaBusinessLogic().BuscarPorId(t => t.Codigo == Codigo);
        }
    }
}
```
## SI TIENE FORÁNEOS
11.	Para los foráneos.

•	Primero se debe colocar en comentarios o borrar la propiedad foránea en la entidad **Zeus.Inventario.Infrastructure.Entities** generada por el Smart Code, las imágenes siguientes corresponden al ejemplo tipo de ProduccionMaquinas.
```c#
       //[RecursoDisplayName("ProduccionMaquinas.IdenProducciontipomaquina")]
       //[Column("IDEN_PRODUCCIONTIPOMAQUINA")]
       //[Required(AllowEmptyStrings = false, ErrorMessage = "El campo Tipo de Máquina es requerido")]
       //public virtual Decimal IdenProducciontipomaquina { get; set; }
```
•	En la entidad **Custom** se declara una propiedad del tipo del objeto foráneo.
```c#
[XmlIgnore]
                [ForeignKey("IdenProduccionTipoMaquina")]
                public virtual ProduccionTipoMaquina ProduccionTipoMaquina { get; set; }
```
•	Luego se debe declarar la propiedad de la siguiente forma.
```c#
[RecursoDisplayName("ProduccionMaquinas.IdenProduccionTipoMaquina")]
                [Column("IDEN_PRODUCCIONTIPOMAQUINA")]
                [Required(AllowEmptyStrings = false, ErrorMessage = "El campo Tipo de Máquina es requerido")]
                public virtual decimal IdenProduccionTipoMaquina { get; set; }
```
•	Inicializar este objeto en el constructor de la entidad:
```c#                
                public ProduccionMaquinas()
                {
                        this.ProduccionTipoMaquina = new ProduccionTipoMaquina();
                }
```
•	Para el caso de Foraneos opcionales y campos numéricos, debemos crear una propiedad Boleana con el prefijo **ShouldSerialize** para que al convertir el objeto a XML el nodo del Foraneo no viaje.  
Esto lo puede generar automáticamente:
```c#
public bool ShouldSerializeCantidadJuguetes()
        {
            return CantidadJuguetes.HasValue;
        }
```        
```c#
Execute spSistema_Hefesto 'ShouldSerialize', 'TABLA'
```
•	Luego en la carpeta **Zeus.Inventario.BusinessLogic** el archivo **XXXXXBusinessLogic** del **Custom** se debe sobrescribir el método **BuscarPorId()**, esto para personalizar los datos que serán consultados juntos con sus foraneos.
```c#
public override ProduccionMaquinas BuscarPorId(Expression<Func<ProduccionMaquinas, bool>> predicate)
        {
                ProduccionMaquinas ProduccionMaquinas = (UnidadTrabajo.Session as ZeusContextoDB)
                        .ProduccionMaquinas
                        .Include(t => t.ProduccionTipoMaquina)
                        .SingleOrDefault(predicate);

                return ProduccionMaquinas;
        }
```   
•	Y Tambien se crear el método **BuscarTodos()**, esto es solo si tienes uno o mas foraneo.
```c#
public override List<ProduccionMaquinas> BuscarTodos()
        {
                return Tabla()
                        .Include(t => t.ProduccionTipoMaquina)
                        .ToList();
        }
```
12.	Por último, crear los sp_API y sp_WSG correspondiente para mantener la compatibilidad.

Con todos estos pasaso ya nuestro BackEnd esta listo para hacer las pruebas necesarias.

Puede continuar con el <a href="https://github.com/EdilbertoMG/Hefesto/blob/master/FrontEnd.md">FrontEnd</a>, si no genera documentos, pero si genera documentos de soporte continue con el siguiente <a href="https://github.com/EdilbertoMG/Hefesto/blob/master/BackEndParaDocumentosDeSoporte.md">BackEnd documetos de soporte</a>, pero si su Documentos es de los que generan contabilización continue con el siguiente <a href="#">BackEnd documetos que generan contabilización</a>
