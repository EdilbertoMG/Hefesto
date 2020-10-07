1. Funcion JavaScript que convierte cualquier fecha en el formato año mes dia que use el comnpitador donde se ejecuta
```js
FechaEfectiva.option("value").toLocaleDateString()
```
2. Mestros que Tienen Cosas Interesantes **EscenariosImpuestos**

3. Maestro donde se reemplaza un Zml (el Zml proviene de un grilla) por una tabla Ver **SpAPI_ClasificacionTipoDocumento** , **SpClasificacionTipoDocumento** , Tabla creada **TableCiclos_SpId** y Funcion creada **GridTipoDocumentoClasificacion**

4. Grilla que Guarda uno a uno **ViewDctoClienteVsArticulo**

5. Grilla que muestra el boton borrado dependiendo de un condicional al crear las celdas **ConfiFormatosDeImpresion**

6. Funcion JavaScript para saber en donde esta el cursos de ecritura o lo que esta selecionado
```js
let indiceI = textarea["0"].selectionStart;
let indiceF = textarea["0"].selectionEnd;
```
7. Grilla que llena datos desde un SP **GridEvaluacionProveedores**.

8. Grilla creada con Jquery, Consulta dinamica que devulve n numero de campos para ser pintados en el front **ConfigComisionesDescuentoRecaudoPorVendedor**

9. Cargar archivo plano **GridPuntoDeReordenBodega**, funcionalidad que permite mandar cualquier informacion, pero devolver un modelo o una lista con su manejo de errores.

10. Traere una fecha de SQL y Modelarla a una JavaScript 
```js
// traere una fecha de sql y mostrarla en el front
		function sqlToJsDate(sqlDate) {
			var sqlDateArr1 = sqlDate.split("/");
			var sMonth = (Number(sqlDateArr1[1]) - 1).toString();
			var sDay = sqlDateArr1[0];
			var sqlDateArr2 = sqlDateArr1[2].split(" ");
			var sYear = sqlDateArr2[0];
			var sqlDateArr3 = sqlDateArr2[1].split(":");
			var sHour = sqlDateArr3[0];
			var sMinute = sqlDateArr3[1];
			var sqlDateArr4 = sqlDateArr3[2].split(".");
			var sSecond = sqlDateArr4[0];

			return new Date(sYear, sMonth, sDay, sHour, sMinute, sSecond);
		}
```
11. Consultar en el front el usuario logueado actualmente
```c#
//string idUser = User.Claims.FirstOrDefault(x => x.Type.EndsWith("/nameidentifier"))?.Value;
	string nombreDeUsuario = User.Identity.Name;
 ```
12. Cargar Articulo y Presentacion
```cshtml
<div class="row">
				@(Html.HiddenFor(m => m.IdArticulo, new { id = "IdArticulo" }))
				<div class="col-xl-3 col-lg-3 col-md-3">
					<div class="form-group">
						<label for=@(ViewBag.PrefixConfigPrepararSeries + "_CodigoArticulo")>
							<span class="lbl-msg-required">@languageResource.GetRecurso("Artículo")</span>
						</label>
						<div class="input-group mb-3 searcher-group">
							@(Html.Zeus().TextBox()
                                                            .InputAttr(new
                                                            {
                                                                @class = "form-control searcher-field searcher-event",
                                                                idBtnSearcher = "btnOpenSearcherArticulo",
                                                                panelHeader = panelHeader,
                                                                panelGeneral = panelGeneral,
                                                                maxlength = "25"
                                                            })
							    .ID("CodigoArticulo")
                                                            )
							<a id="btnOpenSearcherArticulo" title="Buscar"
							   class="input-group-append searcher-btn searcher-btn-event"
							   searcherCode="ARTICULO"
							   typeSelect="CHOICE"
							   funcCallBack="getDatosArticulo"
							   prefix=""
							   textboxF4=""
							   nextFieldFocus="">
								<i class="fas fa-search searcher-icon"></i>
							</a>
						</div>
					</div>
				</div>
				<div class="col-xl-3 col-lg-3 col-md-3">
					<div class="form-group">
						<label for=@(ViewBag.PrefixConfigPrepararSeries + "_NombreArticulo")>
							<span class="lbl-msg-required">@languageResource.GetRecurso("Nombre")</span>
						</label>
						@(Html.Inventario().TextBox().MaxLength(100).ReadOnly(true).ID("NombreArticulo"))
					</div>
				</div>
				<div class="col-xl-3 col-lg-3 col-md-3">
					<div class="form-group">
						<label for=@(ViewBag.PrefixConfigPrepararSeries + "_Presentacion")>
							<span class="lbl-msg-required">@languageResource.GetRecurso("Presentación")</span>
						</label>
						<div class="md-form">
							@(Html.Inventario().SelectBox()
							.ID("PresentacionArticulo")
							.Disabled(false)
							.Placeholder("Seleccione Presentación")
							.OnValueChanged("OnChangePresentacion")
							)
						</div>
					</div>
				</div>
			</div>
```
```js
<!-- Exportacion de JS con funciones generales de Inventario -->
<script src="~/js/custom_Inventario.js"></script>
<!-- Exportacion de JS con funciones generales de Inventario -->
```
```js
function getDatosArticulo(data) {

		let registro = null,
			codigo = $("#CodigoArticulo").dxTextBox("instance"),
			nombre = $("#NombreArticulo").dxTextBox("instance");

		if (data != null) {
			registro = data.data[0];
		}
		
		if (registro) {
			codigo.option({ "value": registro.Codigo });
			nombre.option({ "value": registro.Nombre });
			$("#IdArticulo").val(registro.IdArticulo);
			CargarComboPresentacion(registro.Codigo, registro.IdArticulo.toString());
		} else {
			codigo.option({ "value": "" });
			nombre.option({ "value": "" });
			$("#IdArticulo").val("");

			$("#PresentacionArticulo").dxSelectBox({
				"value": "",
				"placeholder": "Seleccione Presentación",
				"dataSource": []
			});
		}
	}
	function OnChangePresentacion(data) {
		$("#IdArticulo").val(data.value);
	}
	function CargarComboPresentacion(Codigo, Presentacion) {

		var parametros = {
			"articulo": Codigo
		};

		CargarPresentacionesArticulos(parametros, Presentacion, "PresentacionArticulo")
	}
```
13. Funcionalidad en forma de Alert que pregunta si deseas continuar o no y funciona dependiendo la respuesta
```js
// pregunta si desea contiunuar con la operacion
			var result = DevExpress.ui.dialog.confirm("<p>El vendedor(" + CodigoVendedor + ") ya esta asignado a un equipo ¿desea cambiarlo?</p>", "¿Esta seguro?");
			result.done(function (dialogResult) {
				if (dialogResult) {
					//guardar
					guardadoGrilla(iden, CodigoVendedor, sel);
				} else {
					//actualiza grilla
					prepararDatos();
				}
			});
```
14 . Grilla que se Consulta atravez de un Sp, pero dentro de su propio Sp_Api **ConfigControlRentabilidadPorClienteProducto**

15 . **SpAPI_ControlRentabilidadPorClienteProducto**

16. Ejemplo de Get
```js
function refreshFuentes() {
		var grid = $('#GridRollFuentesModel_grid').dxDataGrid('instance'),
			idRoll = $('#idRoll').dxTextBox('instance').option("value");

		if ((idRoll == "") || (idRoll == null) || (idRoll == undefined)) {
			DevExpress.ui.notify("Seleccione un Roll", "warning", 2500);
		} else {
			$.ajax({
				type: "GET",
				url: `/GridRollFuentes/GetDataGrid?Id=${idRoll}`,
				contentType: false,
				processData: false,
				async: true,
				cache: false,
				hideLoading: false,
				success: function (data) {
					if (data[0].TieneErrores) {
						DevExpress.ui.notify(data[0].Errores[0], "warning", 2000);
					} else {
						grid.option("dataSource", data);
						grid.refresh();

						refreshFuentesSeries(data[0].Fuente);
					}
				},
				error: function () {
					DevExpress.ui.notify("Error al actualizar", "error", 2000);
				}
			});
		}
	}
```

17. Ejemplo de Post 
```js
function opcionesEnGrillaFuentes(e) {
		var idRoll = $('#idRoll').dxTextBox('instance').option("value");

		if ((idRoll == "") || (idRoll == null) || (idRoll == undefined)) {
			DevExpress.ui.notify("Seleccione un Roll", "warning", 2500);
		} else {
			var parameters =
			{
				"Check": e.data.Check,
				"Fuente": e.data.Fuente,
				"DESFUENTE": e.data.DESFUENTE,
				"IdRoll": parseInt(idRoll)
			};

			var formdata = new FormData();
			formdata.append("data", JSON.stringify(parameters));
			$.ajax({
				type: "POST",
				url: "/GridRollFuentes/IANDD",
				data: formdata,
				contentType: false,
				processData: false,
				async: true,
				cache: false,
				hideLoading: false,
				success: function () {
					DevExpress.ui.notify("Guardado satisfactoriamente", "success", 2500);
				},
				error: function () {
					DevExpress.ui.notify("Error al Guardadar", "error", 2500);
				}
			});
		}
	}
```

18. Grilla ediyable, Focus en Celdes y llave primaria 
```c#
@(Html.DevExtreme().DataGrid()
					    .ID("GridRollFuentesModel_grid")
					    .Editing(editing =>
					    {
						    editing.Mode(GridEditMode.Cell);
						    editing.AllowUpdating(true);
						    editing.AllowAdding(false);
						    editing.AllowDeleting(false);
						    editing.UseIcons(true);
					    })
					    .KeyExpr("Fuente")
					    .OnRowUpdated("opcionesEnGrillaFuentes")
					    .FocusedRowEnabled(true)
					    .FocusedRowKey(-1)
					    .OnFocusedRowChanged("OnCellClickFuentes")
					    //.OnCellClick("OnCellClickFuentes")
					    .Columns(col =>
					    {
						    col.Add()
						    .Alignment(HorizontalAlignment.Center)
						    .Caption(languageResource.GetRecurso("Fuentes utilizadas por este roll"))
						    .Columns(columns =>
						    {
							    columns.Add().DataField("Check").Visible(true).AllowEditing(true).Caption(languageResource.GetRecurso("Chk")).Width(50).Alignment(HorizontalAlignment.Center);
							    columns.Add().DataField("Fuente").Visible(true).AllowEditing(false).Caption(languageResource.GetRecurso("ID")).Width(250).Alignment(HorizontalAlignment.Center);
							    columns.Add().DataField("DESFUENTE").Visible(true).AllowEditing(false).Caption(languageResource.GetRecurso("Nombre")).Width(250).Alignment(HorizontalAlignment.Center);

						    });
					    })
					    .NoDataText("No hay datos")
					    .SearchPanel(searchPanel => searchPanel
					    .Visible(true)
					    .Width(240)
					    .Placeholder("Buscar..."))
					    .Paging(p => p.PageSize(10))
					    .Pager(p => p
						 .ShowInfo(true)
						 .InfoText("Página {0} de {1} ({2} registros)")
						 .ShowNavigationButtons(true)
						 .ShowPageSizeSelector(false)
						 .AllowedPageSizes(new int[] { 10, 50, 100 })
						 .Visible(true))
					)
```
19. Extraer los nombre de los campos de una Tabla
```c#
public string BuscarDatosSelect(string SpRpt)
		{
			List<string> result = (ZeusContextDb as ZeusContextoDB).GetColumnsFromProcedure("SpRptVariables_Funciones",
				new SqlParameter("@Op", "ColumnasSpRpt"),
				new SqlParameter("@SpRpt", SpRpt));

			return JsonConvert.SerializeObject(result);
		}
```
20. Guardar Con Modelo Directamente
```js
let model =
		{
			"IdiomaDiccionarioIden": e.data.IdiomaDiccionarioIden,
			"IdiomaTiposIden": e.data.IdiomaTiposIden,
			"frace": e.data.frace,
			"Traduccion": e.data.Traduccion,
			"Proteger": e.data.Proteger
		};

		$.ajax({
			type: "POST",
			url: "/ConfigIdiomaControles/Edit",
			contentType: 'application/json',
			dataType: 'json',
			data: JSON.stringify(model),
			processData: false,
			async: true,
			cache: false,
			hideLoading: false,
			success: function () {
				refreshGrid(codigo);
				DevExpress.ui.notify("Guardado satisfactoriamente", "success", 2500);
			},
			error: function () {
				DevExpress.ui.notify("Error al Eliminar", "error", 2500);
			}
		});
```
```c#
[HttpPost]
		public ConfigIdiomaControlesModel Edit([FromBody]ConfigIdiomaControlesModel model)
		{
			return EditModel(model);
		}
```
21. Subir Archivo Plano y Convetirlo en un Arreglo
```c#
@{
	List<object> buttonsList = new List<object>
	{
	new {
	htmlContent = "<i class=\"fas fa-file-upload\"></i>",
	action = "javascript:CargarArchivoPlano()",
	tooltip = "Cargar Archivo Plano"
	}
}
```
```c#
<div class="col-xl-3 col-lg-3 col-md-3">
						<div class="center-block">
							@(Html.DevExtreme().FileUploader()
							    .Name("archivoPlano")
							    .SelectButtonText("Cargar Archivo Plano")
							    .LabelText("")
							    .Accept("text/plain")
							    .ID("archivoPlano")
							    .OnUploaded("archivoPlano")
							    .Visible(false)
							)
						</div>
					</div>
```
```js
function archivoPlano(e) {
		let lector = new FileReader(),
			text = e.file;

		lector.addEventListener("load",
			function (evento) {
				let cadena = evento.target.result;

				if (cadena === '') {
					DevExpress.ui.notify('Este archivo no contiene datos', "warning", 2000);
				} else {
					archivoPlanoAjax(cadena);
				}

			}, false);

		lector.readAsText(text);
	}

	function archivoPlanoAjax(data) {
		let Array = data.split("\n");
	}
```
22. Simular Click en un dxFileUploader desde un boton
```js
function CargarArchivoPlano() {
        let fileUploader = $('#archivoPlano').dxFileUploader('instance');
	fileUploader.reset();
	fileUploader._isCustomClickEvent = true;
	fileUploader._$fileInput.click();
    }
```

23. SpApi donde se maneja xml de diferentes entidades **SpAPI_ConfigDescuentosVentasPeriodosGrid**

24. Buscador que se tiene un parametro adicional
```cshtml
<div class="col-xl-3 col-lg-3 col-md-3">
					<div class="form-group">
						<div class="md-form">
							@(Html.Inventario().SelectBox()
							.DataSource(Model.ListTipoDocumento)
							.DisplayExpr("Texto")
							.ValueExpr("Valor")
							.Value(Model.ListTipoDocumento[0].Codigo)
							.ID("tipoDocumento")
							.Placeholder("Seleccione Tipo de Documento")
						)
						</div>
					</div>
				</div>
```
```cshtml
<div class="col-xl-3 col-lg-3 col-md-3">
					<div class="form-group">
						<div class="input-group mb-3 searcher-group">
							@(Html.Zeus().TextBoxFor(m => m.ConsecutivoInicial)
							.InputAttr(new
							{
								maxlength = "form-control searcher-field searcher-event",
								@class = "form-control searcher-field searcher-event",
								idBtnSearcher = "consecutivoInicial_btnOpenSearcher",
								panelHeader = panelHeader,
								panelGeneral = panelGeneral
							}).MaxLength(25)
							.ID("consecutivoInicial")
							)
							<a id="consecutivoInicial_btnOpenSearcher" title="Buscar"
							   class="input-group-append searcher-btn searcher-btn-event"
							   searcherCode="CabeceraDeDocumentos"
							   typeSelect="CHOICE"
							   addConditionParams=@(@"{'@param1' : {
	    							'valTarget' : '#tipoDocumento',
	    							'typeField' : 'dxselect',
	    							'usePrefixed' : false,
	    							'isReq' : false
	    							}}"
	    						.Replace(" ", ""))
							   funcCallBack="getConsecutivoInicial"
							   prefix=""
							   textboxF4=""
							   nextFieldFocus="">
								<i class="fas fa-search searcher-icon"></i>
							</a>
						</div>
					</div>
				</div>
```
25. Modal como Ventana **ConfigDiasSinIva**

26. Verfificar si los Datos no son Null cuando vienen de la Base
```c#
if (!(resultado.IDEN is DBNull))
					{
						newModel.IDEN = resultado.IDEN;
					}
```
27. SpApi donde se usa un While para recorrer una tabla temporal y se manda a ejecutar una accion **SpAPI_ConfigConsumoDirectoVariables**

28. Texbox con solo Mayusculas
```css
<style type="text/css">  
    .uppercase .dx-texteditor-input {  
        text-transform: uppercase;  
    }  
</style>
```
```cshtml
@(Html.DevExtreme().TextBox()  
    .ElementAttr("class", "uppercase")  
    .Value("Test")  
) 
```

29. Ventana Modal que se abre desde el menu **CambiarFirmaDigital**
