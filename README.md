# Pasos para Utilizar SmartCode

_Después de generar los archivos en el Smart Code se deben copiar los archivos en este orden_

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
```
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
```
using Hefesto.Backend.Core.Data;
using System;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;
using System.IO;
using System.Xml;
using System.Xml.Serialization;

namespace Zeus.Inventario.BusinessLogic
{
    public partial class ProduccionTipoMaquinaBusinessLogic 
    {
        #region Reglas de negocio

        public override ProduccionTipoMaquina Adicionar(ProduccionTipoMaquina data)
        {
                ValidateRules(ReglasAdicionar, data);
                return ExecuteOperation("I", data);
        }

        public override ProduccionTipoMaquina Modificar(ProduccionTipoMaquina data)
        {
                ValidateRules(ReglasAdicionar, data);
                return ExecuteOperation("U", data);
        }

        public override ProduccionTipoMaquina Eliminar(ProduccionTipoMaquina data)
        {
                ValidateRules(ReglasAdicionar, data);
                return ExecuteOperation("D", data);
        }

        protected override ProduccionTipoMaquina ExecuteProcedure(string operation, ProduccionTipoMaquina data)
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
10.	Crear una clase parcial dentro de la ruta **Zeus.Inventario.Api\Controllers\Custom** de la entidad que se acaba de generar y agregar un controlador de búsqueda por código.
Esta búsqueda siempre hará referencia a la llave visual de la entidad en los casos donde la llave primaria es un **Iden**, para aquellos casos donde la llave primaria es la misma llave visual no es necesario crear este código.
```
using DevExtreme.AspNet.Data;
using DevExtreme.AspNet.Data.ResponseModel;
using DevExtreme.AspNet.Mvc;
using Hefesto.Api.Core.Controllers;
using Hefesto.Backend.Core.Utilities;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.Linq;
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
11.	Para los foráneos.

•	Primero se debe colocar en comentarios la propiedad foránea en la entidad **Zeus.Inventario.Infrastructure.Entities** generada por el Smart Code, las imágenes siguientes corresponden al ejemplo tipo de PracticaHijo.
<img src="https://i.ibb.co/9bFXbPR/Screenshot-1.png" alt="Logotipo de HTML5">

•	En la entidad Custom se declara una propiedad del tipo del objeto foráneo e inicializar este objeto en el constructor de la entidad, por ejemplo:
```
[XmlIgnore]
        [ForeignKey("IdenPapa")]
        public virtual PracticaPadre Papa { get; set; }

public PracticaHijo() {
            this.Papa = new PracticaPadre();
        }
```
•	Luego se debe declarar la propiedad de la siguiente forma.
```
[RecursoDisplayName("PracticaHijo.IdenPapa")]
        [Column("IdenPapa")]
        [Required(AllowEmptyStrings = false, ErrorMessage = "El campo IdenPapa es requerido")]
        public virtual Decimal IdenPapa { get; set; }
```
•	Para el caso de Foraneos opcionales y campos numéricos, debemos crear una propiedad Boleana con el prefijo **ShouldSerialize** para que al convertir el objeto a XML el nodo del Foraneo no viaje.  
Esto lo puede generar automáticamente:
```
public bool ShouldSerializeCantidadJuguetes()
        {
            return CantidadJuguetes.HasValue;
        }
```        
```
Execute spSistema_Hefesto 'ShouldSerialize', 'TABLA'
```
•	Luego en la carpeta **Zeus.Inventario.BusinessLogic** el archivo **XXXXXBusinessLogic** del **Custom** se debe sobrescribir el método **BuscarPorId()**, esto para personalizar los datos que serán consultados juntos con sus foraneos.
```
public override PracticaHijo BuscarPorId(Expression<Func<PracticaHijo, bool>> predicate)
        {
            PracticaHijo PracticaHijo = (UnidadTrabajo.Session as ZeusContextoDB)
                    .PracticaHijo
                    .Include(t => t.Papa)
                    .SingleOrDefault(predicate);

            return PracticaHijo;
        }
```   
•	Y Tambien se crear el método **BuscarTodos()**
```
public override List<PracticaHijo> BuscarTodos()
        {
            return Tabla()
                    .Include(t => t.Papa)
                    .ToList();
        }
```
Con todos estos pasaso ya nuestro BackEnd esta listo para hacer las pruebas necesarias.
