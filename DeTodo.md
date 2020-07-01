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
					<div class="col-xl-3 col-lg-3 col-md-3" style="display: none;">
						<div class="form-group">
							@(Html.Inventario().NumberBoxFor(m => m.IdArticulo).InputAttr("maxlength", 2147483647).ID("IdArticulo"))
						</div>
					</div>
					<div class="col-xl-3 col-lg-3 col-md-3">
						<div class="form-group">
							<label for=@(ViewBag.PrefixConfigControlRentabilidadPorClienteProducto + "_CodigoArticulo")>
								<span asp-validation-for="CodigoArticulo" class="lbl-msg-required">@languageResource.GetRecurso("CodigoArticulo")</span>
							</label>
							<div class="input-group mb-3 searcher-group">
								@(Html.Zeus().TextBoxFor(m => m.CodigoArticulo)
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
							<label for=@(ViewBag.PrefixConfigControlRentabilidadPorClienteProducto + "_NombreArticulo")>
								<span asp-validation-for="NombreArticulo" class="lbl-msg-required">@languageResource.GetRecurso("Nombre")</span>
							</label>
							@(Html.Inventario().TextBoxFor(m => m.NombreArticulo).InputAttr(new { @class = "form-control", maxlength = 100 }).ReadOnly(true).ID("NombreArticulo"))
						</div>
					</div>
					<div class="col-xl-3 col-lg-3 col-md-3">
						<div class="form-group">
							<label for=@(ViewBag.PrefixConfigControlRentabilidadPorClienteProducto + "_Presentacion")>
								<span class="lbl-msg-required">@languageResource.GetRecurso("Presentación")</span>
							</label>
							<div class="md-form">
							@(Html.Inventario().SelectBoxFor(m => m.Presentacion)
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
<!-- Exportacion de JS con funciones generales de Inventario -->
function getDatosArticulo(data) {
		var registro = data.data[0],
			codigo = $("#CodigoArticulo").dxTextBox("instance"),
			nombre = $("#NombreArticulo").dxTextBox("instance"),
			id = $("#IdArticulo").dxNumberBox("instance");

		if (data.data[0]) {
			codigo.option({ "value": registro.Codigo});
			nombre.option({ "value": registro.Nombre });
			id.option({ "value": registro.IdArticulo });
			CargarComboPresentacion(registro.Codigo, registro.IdArticulo.toString());
		} else {
			codigo.option({ "value": "" });
			nombre.option({ "value": "" });
			id.option({ "value": "" });

			$("#PresentacionArticulo").dxSelectBox({
				"value": "",
				"placeholder": "Seleccione Presentación"
			});
		}
	}
	function OnChangePresentacion(data) {
		$("#IdArticulo").dxNumberBox("instance").option({ "value": data.value});
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
