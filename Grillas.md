# Grillas

_Forma de crear Grillas en Hefesto_

Antes de hacer los pasos para consultar los datos y guardar verifica bien si las tablas a las cuales vas a crear como Grilla ya tienen todos los datos necesarios que vas a mostrar si no es asi crea una vista en Sql y a esa vista le creas el Back y el Front y luego sigue con los pasos.
## Back
## Paso 1

Creamos nuestro Back Y Front con el mismo flujo de Siempre: las gruilla por lo general son tablas las cuales debebemos crearle nuestro Back y Fron normalmente (No debes crear Sp ya que estas grillas solo serviran para consultar los datos).
## Paso 2

Dentro de Nuestro Back en la solucion **Zeus.Inventario.Infrastructure.Entities** en el **Custom** creamos unas variables con la etiqueta [NotMapped] de las entidades ya creadas "Grillas", para este ejemplo son 2.
```c#
[NotMapped]
public virtual List<View_Kit_Articulos> View_Kit_Articulos { get; set; }
[NotMapped]
public virtual List<View_Kit_Conceptos> View_Kit_Conceptos { get; set; }
```
## Paso 3
Creamos dentro de **Zeus.Inventario.BusinessLogic** en el **Custom** creamos nuestros metodos los cuales se usaran en la entidad principal para buscar los datos en la entidad de las Grillas, este paso se debe hacer dentro del custon de las entidades a consultar "tablas de las grillas", no de nuestra entidad principal, para este ejemplo son 2 grillas es decir creamos 1 metodo por cada entidad.
```c#
public List<View_Kit_Articulos> BuscarTodosKit(Decimal? Iden)
		{
			return this.BuscarTodos(t => t.Kit_Iden == Iden);
		}
```
```c#
public List<View_Kit_Conceptos> BuscarTodosKit(Decimal? Iden)
		{
			return this.BuscarTodos(t => t.Kit_Iden == Iden);
		}
```
Esto nos permitira buscar por Iden o Codigo dependiendo del valor asociado entre la entidad principal y tus otras entidades o tablas.
## Paso 4

Dentro de **Zeus.Inventario.BusinessLogic** en el **Custom** sobreescribimos el metodo **BuscarPorId** y dentro asociamos las Variables no mapeadas y mandamos a consultar los datos filtrando por nuestra variable a utilizar en este caso las tablas compartian el Iden, esta busqueda se hace en nuestro metodos creados en el paso anterior en cada entidad (Todo estos pasos son solamente para consultar), ya con esto podemos realizar la busqueda ya sea por Codigo o Id como lo determinamos en nuestra entidad principal con los pasos normales de creacion de maestros.
```c#
 public override Kit BuscarPorId(Expression<Func<Kit, bool>> predicate)
                {
                        Kit Kit = (UnidadTrabajo.Session as ZeusContextoDB)
                                .Kit
                                .SingleOrDefault(predicate);

                        if (Kit != default(Kit))
                        {
                                Kit.View_Kit_Articulos = new View_Kit_ArticulosBusinessLogic(UnidadTrabajo).BuscarTodosKit(Kit.Iden);
                                Kit.View_Kit_Conceptos = new View_Kit_ConceptosBusinessLogic(UnidadTrabajo).BuscarTodosKit(Kit.Iden);
                        }

                        return Kit;
                }
```
## Paso 5
Dentro de **Zeus.Inventario.Api** en la Carpeta **Controllers** de nuestra entidad, en la clase Put **Put**, agregamos.
```C#
objetoActual.View_Kit_Articulos = model.View_Kit_Articulos;
objetoActual.View_Kit_Conceptos = model.View_Kit_Conceptos;
```
Agregamos las varibales creadas en el primer paso y la igualamos con los valores que traera el modelo en el Put para que asi nos reciba los datos que enviaremos desde el Front en los pasos que vienen a continuacion.

## Paso 6
Dentro del Front en la solucion **Zeus.Inventario.UI.Data** dentro del modelo creamos lo siguiente 
```C#
public string Kit_ArticulosGridSerialized { get; set; }
public string Kit_ConceptosSerialized { get; set; }
```
En este ejemplo se crea una variable por cada **Grilla** estas varibales serviran como variables ocultas en nuestro Front para poder enviar los datos que tendran nuestra Grilla.
Dentro del modelo tambien pegamos lo siguiente dentro de la clase **ToModel**
```C#
View_Kit_Articulos = entity.View_Kit_Articulos,
View_Kit_Conceptos = entity.View_Kit_Conceptos,
```
Este paso sirve para que nos retorne los valores que estamos consultando desde el Back y podelos usar en el Front

## Paso 7
En nuestra de **Zeus.Inventario.UI.WebApp** en la carpeta **Views** dentro de la Vista **KitEdit.cshtml** pegamos las Grillas Generadas por el Smart code Junto a sus botones si se necesitan, y en ella podemos hacer la configruacion que necesitemos, como segunda forma puedes llamar esa Grilla como vista parcial. en este ejemplo se decide crear las Grillas manualmente para evitar pegar codigo de mas que no se usara en como en la vista parcial.

Botones
```cshtml
Dictionary<string, string> actionsList = new Dictionary<string, string>
	{
	};
	List<object> buttonsList = new List<object>
{
		new {
			htmlContent = "<i class=\"fas fa-plus\"></i>",
			action = "javascript:AgregarEnLaGrillaKit_Articulos()",
			tooltip = "Agregar"
	},
		new {
			htmlContent = "<i class=\"fas fa-file-excel\"></i>",
			action = "export",
			tooltip = "Exportar a Excel"
		}
	};

	List<object> buttonsListDos = new List<object>
{
		new {
			htmlContent = "<i class=\"fas fa-plus\"></i>",
			action = "javascript:AgregarEnLaGrillaKit_Conceptos()",
			tooltip = "Agregar"
	},
		new {
			htmlContent = "<i class=\"fas fa-file-excel\"></i>",
			action = "export",
			tooltip = "Exportar a Excel"
		}
	};
```
Si los datos dentro de la **Grilla** los quieres guardar con un boton que aparezca arriba de la Grilla debes crearlo como se muestra en este Ejemplo y en el **action** poner que sera de tipo javascript **action = "javascript:AgregarEnLaGrillaKit_Conceptos()",**

```cshtml
<div class="box-body table-responsive no-padding mt-2">
							@(Html.Zeus().DataGridBasic<View_Kit_ArticulosModel>(actionsList, buttonsList, false, false)
											.ID("View_Kit_ArticulosModel_grid")
											.DataSource(Model.View_Kit_Articulos, new string[] { "Kit_Iden", "Iden" })
											.ColumnAutoWidth(true)
											.Editing(editing =>
											{
												editing.AllowDeleting(true);
												editing.UseIcons(true);
											})
											.Columns(columns =>
											{
												columns.AddFor(m => m.Codigo).Caption("Codigo");
												columns.AddFor(m => m.Nombre).Caption("Nombre");
												columns.AddFor(m => m.Presentacion).Caption("Presentacion");
												columns.AddFor(m => m.Cantidad).Caption("Cantidad");
												columns.AddFor(m => m.Precio).Caption("Precio");
											})
							)
						</div>
```
Es de suma importancia que el DataSource apunte a las variable que pasamos del Back al Front con los datos consultados de la entidad de la Grilla **.DataSource(Model.View_Kit_Conceptos, new string[] { "Kit_Iden", "Iden" })**
```cshtml
<div class="box-body table-responsive no-padding mt-2">
							@(Html.Zeus().DataGridBasic<View_Kit_ConceptosModel>(actionsList, buttonsListDos, false, false)
							.ID("View_Kit_ConceptosModel_grid")
							.DataSource(Model.View_Kit_Conceptos, new string[] { "Kit_Iden", "Iden" })
							.Editing(editing =>
							{
							editing.AllowDeleting(true);
							editing.UseIcons(true);

							})
							.SearchPanel(sp => { sp.Visible(true); sp.Placeholder("Buscar"); sp.Width(240); }).Hint("Buscar")
							.Columns(columns =>
							{
							columns.AddFor(m => m.Codigo).Caption("Codigo");
							columns.AddFor(m => m.Nombre).Caption("Nombre");
							columns.AddFor(m => m.Cantidad).Caption("Cantidad");
							columns.AddFor(m => m.Precio).Caption("Precio");
							})
							.ColumnHidingEnabled(false)
							.RemoteOperations(true)
							.ShowBorders(true)
							.ShowColumnLines(false)
							.ShowRowLines(true)
							.RowAlternationEnabled(true)
							.Paging(p => p.PageSize(10))
							.Pager(p => p
								.ShowInfo(true)
								.InfoText("Página {0} de {1} ({2} registros)")
								.ShowNavigationButtons(true)
								.ShowPageSizeSelector(false)
								.AllowedPageSizes(new int[] { 10, 50, 100 })
								.Visible(true))
							.NoDataText("No hay datos")
							.Export(x => x
								.Enabled(false)
								.AllowExportSelectedData(false)
								.ExcelFilterEnabled(true))
							.HeaderFilter(h => h.Visible(false))
							.FilterRow(p => p.Visible(false))
							.SearchPanel(s => s
							.Visible(true)
							.Width(240)
							.Placeholder("Buscar"))
							)
						</div>
```
Es de suma importancia que el DataSource apunte a las variable que pasamos del Back al Front con los datos consultados de la entidad de la Grilla **.DataSource(Model.View_Kit_Conceptos, new string[] { "Kit_Iden", "Iden" })**

## Paso 8
Creamos unas con devextreme un **HiddenFor** por cada grilla que apuenten a nuestra variables creadad en el modelo del Front para poder enviar los datos de la Grilla al Back, le asiganamos tambien un Id unico.
```cshtml
<!-- Datos de las Grillas Ocultos -->
		@Html.HiddenFor(m => m.Kit_ArticulosGridSerialized, new { id = "Kit_ArticulosGridSerialized" })
		@Html.HiddenFor(m => m.Kit_ConceptosSerialized, new { id = "Kit_ConceptosSerialized" })
```
## Paso 9
En nuestro **JavaScript** hacemos las validaciones correspondientes de los datos que se van a insertar en la Grilla al precionarl el boton que hemos configurado anteriormente, luego de estas validaciones procedemos a agregar los datos a la Grilla de la siguiente manera, en este ejemplo por ser 2 Grillas se hacen 2 veces.
```js
function AgregarEnLaGrillaKit_Articulos() {

	let Iden = $("#Iden").val(),
		KitCodigo = $("#KitCodigo"),
		ArticuloShow = $("#ArticuloShow"),
		ArticulosNombre = $("#ArticulosNombre").dxTextBox("instance"),
		Presentacion = $("#ArticulosPresentacion").dxSelectBox("instance"),
		ArticulosCantidad = $("#ArticulosCantidad").dxNumberBox("instance"),
		ArticulosPrecio = $("#ArticulosPrecio").dxNumberBox("instance"),
		Kit_ArticulosGrid = $("#View_Kit_ArticulosModel_grid");

		if (KitCodigo.val() == "") {
			DevExpress.ui.notify("Agregue un codigo de kit", "warning", 2000);
		}else if (ArticuloShow.val() == "") {
			DevExpress.ui.notify("Agregue un Artículo", "warning", 2000);
		}else if (Presentacion.option("value") == "") {
			DevExpress.ui.notify("Seleccione una Presentacion", "warning", 2000);
		}else if ((ArticulosCantidad.option("value") == "") || (ArticulosCantidad == 0)) {
			DevExpress.ui.notify("Agregue una Cantidad", "warning", 2000);
		} else {
			var objetoNuevo = {
				"Iden": Math.floor(Math.random()*101),
				"Codigo": ArticuloShow.val(),
				"Nombre": ArticulosNombre.option("value"),
				"Presentacion": Presentacion.option("text"),
				"Cantidad": ArticulosCantidad.option("value"),
				"Precio": ArticulosPrecio.option("value"),
				"Kit_Iden": Iden,
				"Articulo": Presentacion.option("value")
			}

			Kit_ArticulosGrid.dxDataGrid("getDataSource")["_store"]["_array"].push(objetoNuevo);
			Kit_ArticulosGrid.dxDataGrid("instance").refresh();
		}
	}
```
```js
function AgregarEnLaGrillaKit_Conceptos() {
		let Iden = $("#Iden").val(),
		KitCodigo = $("#KitCodigo"),
		ConceptoShow = $("#ConceptoShow"),
		ConceptoNombre = $("#ConceptoNombre").dxTextBox("instance"),
		ConceptoCantidad = $("#ConceptoCantidad").dxNumberBox("instance"),
		ConceptoPrecio = $("#ConceptoPrecio").dxNumberBox("instance"),
		Kit_ConceptosGrid = $("#View_Kit_ConceptosModel_grid");

		if (KitCodigo.val() == "") {
			DevExpress.ui.notify("Agregue un codigo de kit", "warning", 2000);
		}else if (ConceptoShow.val() == "") {
			DevExpress.ui.notify("Agregue un Concepto", "warning", 2000);
		}else if ((ConceptoCantidad.option("value") == "") || (ArticulosCantidad == 0)) {
			DevExpress.ui.notify("Agregue una Cantidad", "warning", 2000);
		} else {
			var objetoNuevo = {
				"Iden": Math.floor(Math.random()*101),
				"Codigo": ConceptoShow.val(),
				"Nombre": ConceptoNombre.option("value"),
				"Cantidad": ConceptoCantidad.option("value"),
				"Precio": ConceptoPrecio.option("value"),
				"Kit_Iden": Iden,
				"Concepto": idConcepto
			}

			Kit_ConceptosGrid.dxDataGrid("getDataSource")["_store"]["_array"].push(objetoNuevo);
			Kit_ConceptosGrid.dxDataGrid("instance").refresh();
		}
	}
```
## Paso 10
Dentro de **JavaScript** en la primera funcion en la funcionalidad de **window.kitEdit** agregamos **OnBeforeSubmit**, lo cual nos servira para traer todos los datos que esten en nuestra Grilla y luego Serializarlos en un JSON.stringify, meterlos en los **HiddenFor** que creamos en nuestra vista para asi luego enviarlo por las varibales que le asociamos y creamos con anterioridad dentro de nuestro modelo.
```js
window.kitEdit = {
			onFormSuccess: onFormSuccess,
			onFormFailure: onFormFailure,
			OnBeforeSubmit: function () {
				var Kit_ArticulosGrid = $("#View_Kit_ArticulosModel_grid").dxDataGrid("getDataSource"),
					Kit_ConceptosGrid = $("#View_Kit_ConceptosModel_grid").dxDataGrid("getDataSource");

			$("#Kit_ArticulosGridSerialized").val(JSON.stringify(Kit_ArticulosGrid["_store"]["_array"]));
			$("#Kit_ConceptosSerialized").val(JSON.stringify(Kit_ConceptosGrid["_store"]["_array"]));
			return true;
			}
		};
```
## Paso 11
Dentro de nuestra vista **__KitEdit.cshtml** asociamos la funcion OnBeforeSubmit al boton guardado que esta en nuestro **ToolbarZeus**
```js
@(Html.Zeus().ToolbarZeus("KitToolbar",
									"#mainPanel",
									"Kit",
									"KitForm",
									"kitEdit.OnBeforeSubmit",
									Model.EsNuevo,
									"/Kit/ListPartial",
									"/Kit/New",
									string.Format("/Kit/Delete/[['Iden', '{0}']]", Model.Iden),
									string.Format("/Kit/Duplicate/[['Iden', '{0}']]", Model.Iden),
									"../theme/images/edit_master.png",
									false,
									"Kit_",
									false,
									buttonsList,
									languageResource.Language,
									"Kit"
									))
```
## Paso 12 
Dentro de **Zeus.Inventario.UI.WebApp** en la carpeta **Controllers** de nuestra entidad en el **[HttpPost]** en la clase **tionResult Edit** hacemos un condicional if donde los datos que enviamos desde nuestra vista no sean nulll, Deserializamos nuestro String, los asociamos como una lista de nuestra variables creadas en nuestro modelo para asi puedan llegar de la forma correcta al Back.
```c#
[HttpPost]
		public ActionResult Edit([Bind(Prefix = Prefix)] KitModel model)
		{
			if (model.Kit_ArticulosGridSerialized != null){
				model.View_Kit_Articulos = JsonConvert.DeserializeObject<List<View_Kit_Articulos>>(model.Kit_ArticulosGridSerialized);
			}
			if (model.Kit_ConceptosSerialized != null){
				model.View_Kit_Conceptos = JsonConvert.DeserializeObject<List<View_Kit_Conceptos>>(model.Kit_ConceptosSerialized);
			}
			return PartialView("KitEdit", EditModel(model));
		}
```
