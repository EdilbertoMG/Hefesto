_Después de generar el Back End se deben copiar los archivos en este orden_

## Front End 🛠️

1.	Abrir la solución **Zeus.Inventario.Front**.

2.	Ir a la carpeta generada por Smart Code **Models** y copiar en la siguiente ruta: **Zeus.Inventario.UI.Data\Models**, cualquier cambio en este archivo debe hacerse en la carpeta **Custom**.

3.	Ir a la carpeta generada por Smart Code **Proxies** y copiar el archivo en la siguiente ruta **Zeus.Inventario.UI.Data\Proxies** , cualquier cambio en este archivo debe hacerse en la carpeta **Custom**.

4.	Editar el archivo **Zeus.Inventario.UI.Data\Factories\ZeusProxyBusinessLogicExtentions.cs**, ir al final del archivo y asegurarse de crear las líneas de código correspondientes a la tabla en cuestión.
```c#
        public static ColoresBusinessLogic ColoresBusinessLogic(this ProxyLogicaNegocio proxy)
        {
                return new ColoresBusinessLogic(proxy.ServiceAddresUnkown, proxy.Token);
        }
```        
5.	Ir a la carpeta generada por Smart Code **UIControllers** y copiar el archivo en la siguiente ruta **Zeus.Inventario.UI.WebApp\ Controllers** , cualquier cambio en este archivo debe hacerse en la carpeta **Custom**.

6.	Ir a la carpeta generada por Smart Code **UIView** y copiar la carpeta en la siguiente ruta **Zeus.Inventario.UI.WebApp\Views**, se crearán 4 archivos.

7.	Editar el archivo **../wwwroot/data/AppMenu.json** Agregar la opción de menú.
Importante es que estas opciones de menú se les configura el controlador y la acción, para los maestros y los documentos la acción será **List**, que corresponden a la ventana principal que muestra una cuadricula.
```js
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
•	El control **Html.Zeus().DataGridBasi** apunta al controlador GET del objeto de **Inventario.UI.Modules.Controllers**.

```c#
 [HttpGet]
                public LoadResult Get(DataSourceLoadOptions loadOptions)
                {
                        return Manager().ProduccionMaquinasBusinessLogic().Find(loadOptions).ToModels().ToLoadResult();
                }
``` 

•	Este a su vez llama al proxi del objeto en el método **Find** el cual se comunica con la API en la ruta **GetByOptions** dentro de **Inventario.UI.Data.Proxies**.
```c#
public ListaResultado<ProduccionMaquinas> Find(DataSourceLoadOptions loadOptions)
        {
            return Task.Run(async () => await FindAsync(loadOptions)).GetAwaiter().GetResult();
        }

        public async Task<ListaResultado<ProduccionMaquinas>> FindAsync(DataSourceLoadOptions loadOptions)
        {
            var options = JsonConvert.SerializeObject(loadOptions);
            var requestUri = string.Concat(serviceAddres, "/api/ProduccionMaquinas/GetByOptions?options=" + options);
            var response = await HttpRequestFactory.Get(requestUri, token);
            return response.ContentAsType<ListaResultado<ProduccionMaquinas>>();
        }
``` 
•	Para lograr obtener la información de los foráneos se debe agregar los **Include** correspondientes en el Back End **Zeus.Inventario.Api/Controllers**.

```c#
[HttpGet]
        [Route("GetByOptions")]
        public ListaResultado<ProduccionMaquinas> GetByOptions(string options)
        {
            DataSourceLoadOptions loadOptions = string.IsNullOrWhiteSpace(options) ? new DataSourceLoadOptions() : JsonConvert.DeserializeObject<DataSourceLoadOptions>(options);
            return DataSourceLoader.Load(Manager().ProduccionMaquinasBusinessLogic().Tabla()
                                                                                    .Include(t => t.ProduccionTipoMaquina)
                    , loadOptions).ToListaResultado<ProduccionMaquinas>();
        }
```
•	Por último, configurar en la grilla el nombre del **Foraneo** en la vista **XXXXXXList.cshtml**.

```cshtml
@(Html.Zeus().DataGridBasic<ProduccionMaquinasModel>(actionsList, buttonsList, false, false, @VIEWDET_PANELDETAIL)
												.ID("ProduccionMaquinasModel_grid")
												.DataSource(d => d.Mvc().Area("").Controller("ProduccionMaquinas").LoadAction("Get").Key("Iden")
															.LoadParams(new { codeDetail = VIEWDET_HEADERMASTER_CODE }))
												.Columns(columns =>
													{
												columns.AddFor(m => m.Codigo)
																	.Caption("Código");
												columns.AddFor(m => m.Nombre)
																	.Caption("Nombre");
												columns.AddFor(m => m.ProduccionTipoMaquina.Nombre)
																	.Caption("Tipo de Máquina");
												columns.AddFor(m => m.Descripcion)
																	.Caption("Descripción");
												columns.AddFor(m => m.NoOperadores)
																	.Caption("No Operadores");
												columns.AddFor(m => m.Costominuto)
																	.Caption("Costo por Minuto");
												columns.AddFor(m => m.Costominuto2)
																	.Caption("Costo Mínuto Niif");
													})
												)

```

9.	Editar la vista **XXXXXXEdit.cshtml**, hay que tener en cuenta varias cosas:

•	Agregar la siguiente instrucción al principio de la página.
``` cshtml
@inject ILanguageResource languageResource
``` 
•	Todos los campos del objeto deben tener su correspondiente control en la forma, los que no se mostraran visualmente deben estar representados con el control **@Html.HiddenFor**.
```cshtml
@Html.HiddenFor(m => m.NOMBREDELCAMPO, new { id = ViewBag.PrefixNOMBREOBJETO + "_NOMBREDELCAMPO" }).
``` 
•	Para aquellas tablas donde la llave primaria es un **Iden**, se debe eliminar la condición 
**@if (!Model.EsNuevo){}** pues de todas formas este control no se va a visualizar y de inmediato se debe agregar su correspondiente **@Html.HiddenFor**.

10.	Crear buscador en la base de datos: se debe editar el archivo **z2999999 DatosPorDefault_Hefesto_Buscadores.sql** y agregar el nuevo buscador del objeto creado.
```sql
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

```c#
using Hefesto.Frontend.Core.HttpClient;
using System.Threading.Tasks;
using Zeus.Inventario.Infrastructure.Entities;

namespace Zeus.Inventario.UI.Data.Proxies
{
        public partial class ComisionesClasificacionDeVendedoresBusinessLogic
        {
                public ComisionesClasificacionDeVendedores FindById(string codigo)
                {
                        return Task.Run(async () => await FindByIdAsync(codigo)).GetAwaiter().GetResult();
                }

                public async Task<ComisionesClasificacionDeVendedores> FindByIdAsync(string codigo)
                {
                        var requestUri = string.Concat(serviceAddres, "/api/ComisionesClasificacionDeVendedores/GetByCode?Codigo=" + codigo);
                        var response = await HttpRequestFactory.Get(requestUri, token);
                        return response.ContentAsType<ComisionesClasificacionDeVendedores>();
                }
        }
}
```
Esta es la ruta que debe existir en el Back End en la carpeta **Zeus.Inventario.UI.Data/Controllers/Custom**
```c#
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Zeus.Inventario.UI.Data.Factories;
using Zeus.Inventario.UI.Models;

namespace Zeus.Inventario.UI.Modules.Controllers
{
        [Authorize]
        public partial class ComisionesClasificacionDeVendedoresController
        {
                [HttpGet]
                public IActionResult GetByCode(string codigo, string nextFieldFocus)
                {
                        ViewBag.NextFocus = nextFieldFocus;
                        ViewBag.PrefixKit = Prefix;
                        if (string.IsNullOrEmpty(codigo))
                        {
                                ModelState.AddModelError($"{Prefix}_Codigo", "Código esta vacio");
                                return PartialView("ComisionesClasificacionDeVendedoresEdit", GetNewModel());
                        }

                        ComisionesClasificacionDeVendedoresModel model = Manager().ComisionesClasificacionDeVendedoresBusinessLogic().FindById(codigo).ToModel();

                        if (model == null)
                        {
                                model = GetNewModel();
                                model.EsNuevo = true;
                                model.Iden = 0;
                                model.Codigo = codigo;

                                return PartialView("ComisionesClasificacionDeVendedoresEdit", model);
                        }

                        return PartialView("ComisionesClasificacionDeVendedoresEdit", model);
                }
        }
}
```
12.	Si la llave primaria es un **Iden**, también hay agregar al proyecto **Zeus.Inventario.UI.WebApp\Controllers\Custom** una copia del archivo **NOMBREDELOBJETOController.cs** y agregar un nuevo controlador el cual utilizará el nuevo método creado en el proxi **FindById**.

```c#
using DevExtreme.AspNet.Data;
using DevExtreme.AspNet.Data.ResponseModel;
using DevExtreme.AspNet.Mvc;
using Hefesto.Backend.Core.Utilities;
using Hefesto.Frontend.Core.Controllers;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using System;
using System.Collections.Generic;
using System.Linq;
using Zeus.Inventario.Infrastructure.Entities;
using Zeus.Inventario.UI.Data.Factories;
using Zeus.Inventario.UI.Models;

namespace Zeus.Inventario.UI.Modules.Controllers
{
    [Authorize] 
    public partial class ProduccionMaquinasController 
    {
        [HttpGet]
        public IActionResult GetByCode(string codigo, string nextFieldFocus)
        {
                ViewBag.NextFocus = nextFieldFocus;
                ViewBag.PrefixProduccionMaquinas = Prefix;
                if (string.IsNullOrEmpty(codigo))
                {
                        ModelState.AddModelError($"{Prefix}_Codigo", "Código esta vacio");
                        return PartialView("ProduccionMaquinasEdit", GetNewModel());
                }

                ProduccionMaquinasModel model = Manager().ProduccionMaquinasBusinessLogic().FindById(codigo).ToModel();

                if (model == null)
                {
                        model = GetNewModel();
                        model.EsNuevo = true;
                        model.Iden = 0;
                        model.Codigo = codigo;

                        return PartialView("ProduccionMaquinasEdit", model);
                }

                return PartialView("ProduccionMaquinasEdit", model);
        }
    }
}
```
## Hacer Solo si la llave primaria es un Iden de lo contrario deje el buscador que genera el Smart Code

13.	Agregar control del buscador principal en la vista **NOMBREOBJETOEdit.cshtml**, Se puede hacer manualmente esta operación, pero se recomienda utilizar la siguiente instrucción SQL que ayuda a generarlo de forma automática.
```sql
Execute spSistema_Hefesto @Op= 'BuscadorPrincipal', @Tabla = 'TipoAsociado', @LLaveLogica = 'Codigo', @SiguienteCampo = 'Nombre'
```
Dando como resultado algo como esto:
```cshtml
<div class="col-xl-3 col-lg-3 col-md-3 col-sm-3 ">
                                <div class="form-group">
                                        <label for=@(ViewBag.PrefixTipoAsociado + "_Codigo")>
                                                @(Html.ZeusCore().ValidationMessageFor(t => t.Codigo, languageResource.GetRecurso("Codigo "), new { @class = "lbl-msg-required" }))
                                        </label>
                                        <div class="input-group mb-3 searcher-group">
                                                @(Html.Zeus().TextBoxFor(m => m.Codigo)
                                                .InputAttr(new
                                                {
                                                        @class = "form-control searcher-field searcher-event",
                                                        idBtnSearcher = "TipoAsociado_btnOpenSearcher",
                                                        panelHeader = panelHeader,
                                                        panelGeneral = panelGeneral
                                                })
                                                )
                                                <a id="TipoAsociado_btnOpenSearcher" title="Buscar"
                                                   class="input-group-append searcher-btn searcher-btn-event"
                                                   searcherCode="TIPOASOCIADO"
                                                   typeSelect="SELECT"
                                                   pathController="/TipoAsociado/GetByCode/$Codigo"
                                                   prefix="@ViewBag.PrefixTipoAsociado"
                                                   onPrepareData=""
                                                   textboxF4=@(ViewBag.PrefixTipoAsociado + "_Codigo")
                                                   nextFieldFocus=@(ViewBag.PrefixTipoAsociado + "_Nombre")>
                                                        <i class="fas fa-search searcher-icon"></i>
                                                </a>
                                        </div>
                                </div>
                        </div>
```
Hasta este punto ya debe mostrar en el menú y el buscador principal debe funcionar sin problema.

14.	Agregar funcionalidad de variables adicionales y datos adjuntos.
•	Editar el archivo **z2999999 DatosPorDefault_Hefesto_Adjuntos.sql**.
Se debe primero identificar si existe un código en la tabla **VariableDefinicionMaestro** que corresponda con el objeto que se está creando, esto con el objetivo que tanto para las variables adicionales como para los adjuntos se maneje el mismo código, posteriormente agregar la información correspondiente al objeto en cuestión.  **OJO** si existe ya el registro en el estándar, hay que respetar el existente y hacer que coincidan el campo **Tabla** de la tabla **VariableDefinicionMaestro** y el campo **ds_nombre** de la tabla **SYS_ENTIDADES**.
Tener en cuenta que el campo **id** de la tabla **SYS_ENTIDADES** se debe incrementar cada vez que se agregue uno nuevo.

```sql
---------------------------------------------------------------------------------------------------
----------------------------------- ProduccionTipoMaquina
---------------------------------------------------------------------------------------------------
-- Adjuntos
If Not Exists(Select * from SYS_ENTIDADES Where id = 2)
Begin
	insert Into SYS_ENTIDADES (id, ds_nombre, ds_descripcion)
	values (2 ,'ProTipoMaquina', 'Tipos de Máquinas de Producción')
End

-- Variables
If Not Exists(Select * from VariableDefinicionMaestro Where Codigo = 'ProTipoMaquina' And IDEN_TipoVariable = 1 And IDEN_TipoTransaccion Is Null)
Begin
	Insert Into VariableDefinicionMaestro (IDEN_TipoVariable, IDEN_TipoTransaccion, Codigo, Nombre, Descripcion, Ventana, Tabla, CampoBusqueda, GrupoFormulacion) 
	Values (1, Null, 'ProTipoMaquina', 'Producción - Tipos de Máquina de producción', '', '', 'ProTipoMaquina', 'Codigo', Null)
End
Else
Begin
	Update	VariableDefinicionMaestro
	Set	Tabla = 'ProTipoMaquina'
	Where		Codigo = 'ProTipoMaquina'
		And	IDEN_TipoVariable = 1
		And	IDEN_TipoTransaccion Is Null
End

GO
```

•	Editar la vista **_NOMBREOBJETOEdit.cshtml**.
•	Agregar la siguiente instrucción al principio de la página.
```cshtml
@inject ILanguageResource languageResource
```
•	Agregar una lista de botones con el nombre **buttonsList** la cual será adicionada a los botones de la barra de la vista.

```cshtmal
@{
        List<object> buttonsList = new List<object>
{
                new {
                        htmlContent = "<img src=\"../images/icon-variable2.png\"></img>",
                        action = "javascript:showModalVariables()",
                        tooltip = "Variables Adicionales",
                        showInNew = false
                },
                new {
                        htmlContent = "<i class=\"fas fa-file-contract\"></i>",
                        action = "javascript:showModalMasterDocuments()",
                        tooltip = "Documentos",
                        showInNew = false
                }
        };
}
```

•	Agregue a la barra la lista de botones **buttonsList**, **languageResource.Language** y un título en ingles de la vista.

```cshtmal	
buttonsList,
languageResource.Language,
"Production machines"
```
<img src="https://github.com/EdilbertoMG/Hefesto/blob/master/Imagenes/ButtonList.png" alt="Agregar Button List">

•	Agregar una etiqueta **<script type="text/javascript">** el cual se encargará de la apertura de la ventana modal de los datos adjuntos y las variables adicionales.

```js
<script type="text/javascript">
        function showModalVariables() {
                loadHefestoAddVariables(
                        "TipoAsociado",
                        "@Model.Codigo",
                        "@ViewBag.EsNuevo",
                        "",
                        true,
                        "40%",
                        "60%",
                        "Variables Adicionales"
                );
        }
        function showModalMasterDocuments() {
                loadHefestoDocumentsMaster(
                        "TipoAsociado",
                        "@Model.Codigo",
                        "",
                        "@ViewBag.EsNuevo",
                        "MASTER",
                        "60%",
                        "60%",
                        ""
                );
        }
</script>
```
<img src="https://github.com/EdilbertoMG/Hefesto/blob/master/Imagenes/JavaScript.png" alt="JavaScript">

15.	Agregar foráneos: 
•	Primero hay que identificar cual es el campo foráneo **Iden** si lo hay, la llave lógica del foráneo y el campo nombre del foráneo que se mostrarán en la vista. 

•	Abrir el Back End y buscar en la ruta **PUT** (actualizar) y garantizar que se asigne el objeto de navegación esta en **Zeus.Inventario.Api/Controllers**.

```c#
/// <summary>
        /// Modifica un registro
        /// </summary>
        /// <param name="model"></param>
        [HttpPut]
        public ProduccionMaquinas Put([FromBody] ProduccionMaquinas model, string iduser)
        {
            try
            {
                var objetoActual = Manager().ProduccionMaquinasBusinessLogic().BuscarPorId( t => t.Iden == model.Iden );

                objetoActual.Codigo = model.Codigo;
                objetoActual.Nombre = model.Nombre;
                objetoActual.IdenProduccionTipoMaquina = model.IdenProduccionTipoMaquina;
                objetoActual.Descripcion = model.Descripcion;
                objetoActual.NoOperadores = model.NoOperadores;
                objetoActual.Costominuto = model.Costominuto;
                objetoActual.Costominuto2 = model.Costominuto2;

                // Todos los foraneos
                objetoActual.ProduccionTipoMaquina = model.ProduccionTipoMaquina;

                List<ProduccionMaquinas> modelLevelAccess = Manager().ProduccionMaquinasBusinessLogic().GetModelsByLevelAccess("EDIT");

                if (modelLevelAccess != null && !modelLevelAccess.Any(t => t.Iden == model.Iden))
                {
					throw new UnauthorizedAccessException("¡No tiene permisos para realizar esa operación!");
                }

                return Manager().ProduccionMaquinasBusinessLogic().Modificar(objetoActual);

            }
```

<img src="https://github.com/EdilbertoMG/Hefesto/blob/master/Imagenes/AgregarForaneo%20en%20el%20Put.png" alt="Agregar Foraneo en el Put">

•	Editar El archivo del Front End **XXXXXXModel.cs** y asignar los objetos de navegación dentro de la carpeta **Zeus.Inventario.UI/Models**.

```c#
public static ProduccionMaquinasModel ToModel(this ProduccionMaquinas entity)
		{
			if (entity == null)
			{
				return null;
			}

			return new ProduccionMaquinasModel
			{
                                Iden = entity.Iden,
                                Codigo = entity.Codigo,
                                Nombre = entity.Nombre,
                                IdenProduccionTipoMaquina = entity.IdenProduccionTipoMaquina,
                                Descripcion = entity.Descripcion,
                                NoOperadores = entity.NoOperadores,
                                Costominuto = entity.Costominuto,
                                Costominuto2 = entity.Costominuto2,
                                TieneErrores = entity.TieneErrores,
                                ProduccionTipoMaquina = entity.ProduccionTipoMaquina,

                                Errores = entity.Errores,
                                EsNuevo = entity.EsNuevo
			};
		}
```
•	Editar El archivo del Front End **XXXXXXController.cs** y crea la validacion de los forneos que no son requeridos, dentro de **Zeus.Inventario.UI.WebApp**.

```c#
//Para que no valide campos del foraneo que no son requeridos
	private void IgnoreTimeStampValidation()
	{
		ModelState.Keys.Where(t => t.StartsWith("Bodega.Propiedad1") || t.StartsWith("Bodega.Propiedad2")
		|| t.StartsWith("Bodega.Propiedad3") || t.StartsWith("Bodega.Propiedad4") || t.StartsWith("Bodega.Propiedad5")
		|| t.StartsWith("Bodega.Personal") || t.StartsWith("Bodega.TipoFactura") || t.StartsWith("Bodega.Bu"))
		.ToList().ForEach(t => ModelState.Remove(t));
	}
```
Inicialo en **EditModel**
```c#
 private (######Model EditModel(######Model model) 
        {
            ViewBag.Prefix(###### = Prefix;
            string iduser = User.Claims.FirstOrDefault(x => x.Type.EndsWith("/nameidentifier")).Value;

		IgnoreTimeStampValidation();
```
