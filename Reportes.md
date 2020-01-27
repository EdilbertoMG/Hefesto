# Reportes

_Alguno de los Controles mas usados en los reportes_

## Controles 🚀

# 1.	Select

Si tu **Select** es **estatico** abre el SP: **z2999999 DatosPorDefault_Hefesto_Combos** y crear uno nuevo, donde Codigo representa el codigo unico de de Select, Texto lo visual que se vera y valor sera lo que se envie cuando se seleccione un campo del select
```sql
-- Acumulado de Ventas por Vendedor - odenamiento.
Execute spSistema_Hefesto @Op = 'Combo',
@xml = 
'
<Combo>
	<Codigo>AcumuladoDeVentasPorVendedor</Codigo>
	<Valores>
		<Texto>Codigo Vendedor</Texto>
		<Valor>Codigo Vendedor</Valor>
	</Valores>
	<Valores>
		<Texto>Nombre Vendedor</Texto>
		<Valor>Nombre Vendedor</Valor>
	</Valores>
	<Valores>
		<Texto>Razon Social del Cliente</Texto>
		<Valor>Razon Social del Cliente</Valor>
	</Valores>
	<Valores>
		<Texto>Fecha de Facturacion</Texto>
		<Valor>Fecha de Facturacion</Valor>
	</Valores>
	<Valores>
		<Texto>Consecutivo</Texto>
		<Valor>Consecutivo</Valor>
	</Valores>
	<Valores>
		<Texto>Fuente</Texto>
		<Valor>Fuente</Valor>
	</Valores>
	<Valores>
		<Texto>Documento</Texto>
		<Valor>Documento</Valor>
	</Valores>
	<Valores>
		<Texto>Fuente - Documento</Texto>
		<Valor>Fuente - Documento</Valor>
	</Valores>
	<Valores>
		<Texto>Total Facturado</Texto>
		<Valor>Total Facturado</Valor>
	</Valores>
</Combo>
'
GO
```
Si tu **Select** es **dinamico** abre el SP: **spAPI_Combos** y crea el Select que consulte en la tabla los valores que se deben mostrar
```sql
If @Op = 'SpRptImpuestosAsumidos_Movimientos'
	Begin
		Set @Sw_EntroOp = 1

		SELECT	Codigo = Cast('SpRptImpuestosAsumidos_Movimientos' As varchar(50)),
			Texto = Cast(TipoDocumento.Nombre As Varchar(200)),
			Valor = Cast(TipoDocumento.Id As Varchar(200))
		From	TipoDocumento
		where ciclo = 1 and factor <> 0
	End
	-------------------------------------------------------------------------------------
```
En tu **Modelo** crea un dato de tipo List con el nombre que recibira tu FRX
```c#
public List<CombosModel> Ordenamiento { get; set; }
```
En tu **Controlador** incia el dato, pudes usar **FindByCode** ó **FindBySp** dependiendo si tu Select es estatico usa FindByCode y si es dinamico usa FindBySp
```c#
Ordenamiento = (List<CombosModel>)Manager().CombosBusinessLogic().FindByCode("AcumuladoDeVentasPorVendedor").ToModels(),
```
En tu **vista** crear el Control Select
```cshtml
@(Html.Inventario().SelectBox()
										.DataSource(Model.Ordenamiento)
										.DisplayExpr("Texto")
										.ValueExpr("Valor")
										.ElementAttr(new { paramReport = "Ordenamiento", dxCtrReport = "dxselect" })
										.Value("Codigo Vendedor")
										)
```
# 2.	RadioGroup

```cshtml
@(Html.Inventario().RadioGroup()
										       .ElementAttr(new
										       {
											   paramReport = "TipoOrdenamiento",
											   dxCtrReport = "dxradio"
										       })
										       .DataSource(new List<DisplayText>
												   {
												       new DisplayText{Text = languageResource.GetRecurso("Ordenar Ascendentemente"), Value = "ASC"},
												       new DisplayText{Text = languageResource.GetRecurso("Ordenar Descendentemente"), Value = "DESC" }
												   })
										       .ValueExpr("Value")
										       .DisplayExpr("Text")
										       .Value("ASC")
										       .Layout(Orientation.Horizontal)
								)
```
# 3.	CheckBox

Se puede usar de esta manera cuando el dato que recibe el FRX es distinto de BIT, si tu campo es **BIT** debes usar true y false y el FRX lo convertira en 0 ó 1
```cshtml
@(Html.DevExtreme().CheckBox()
									  .Value(false)
									  .Text("Mostrar Agrupamiento por Bodegas")
									  .ElementAttr(new
									  {
									   paramReport = "MostrarPorBodega",
									   dxCtrReport = "dxcheckbox",
									   valTrue = "SI",
									   valFalse = "NO"
									  }
									  )
								)
```
# 4.	Bloquear un un buscador mediante un CheckBox
```javascript
function IncluirConceptosJS(data) {
		var prefixed = '@ViewBag.PrefixReporteAcumuladoDeVentasVendedor';
		if (data.value) {
			$("#" + prefixed + "_ConceptoI").val("").prop("disabled", false).removeClass("readonly");
			$("#" + prefixed + "_ConceptoF").val("").prop("disabled", false).removeClass("readonly");
			$("#btnOpenSearcherConceptoI").removeClass("disabled");
			$("#btnOpenSearcherConceptoF").removeClass("disabled");
		} else {
			$("#" + prefixed + "_ConceptoI").val("").prop("disabled", true).addClass("readonly");
			$("#" + prefixed + "_ConceptoF").val("").prop("disabled", true).addClass("readonly");
			$("#btnOpenSearcherConceptoI").addClass("disabled");
			$("#btnOpenSearcherConceptoF").addClass("disabled");
		}
	}
```
# 5.	Crear un Buscador con multiples condicionales o filtros o variables

**Paso 1:** Crear un buscador normal en el SP **z2999999 DatosPorDefault_Hefesto_Buscadores**, en nuestro **SQL** creamos el condicional y cada variable a usar se debe colocar como si de una variable de SqlServer se tratara Ej: **@param1**
```sql
-- Documentos
Execute spSistema_Hefesto @Op = 'ActualizaBuscador',
@xml = 
'
<Buscador>
	<Codigo>Documentos</Codigo>
	<Nombre>Documentos</Nombre>
	<CampoDevolver>Documento</CampoDevolver>
	<SQL>
		SELECT Fecha = FECHDCTO, Documento = NUMEDCTO, Descripción = DESCDCTO
		FROM DOCUMENT 
		where FNTEDCTO BETWEEN @param1 AND @param2
	</SQL>
	<SqlChoice>
		SELECT	NUMEDCTO
		FROM	DOCUMENT
		Where	NUMEDCTO = @CODE
	</SqlChoice>
	<Anchos>20,30,50</Anchos>
	<Avanzada>
		<Campo>
			<Nombre>FECHDCTO</Nombre>
			<Titulo>Fecha</Titulo>
			<Tipo>varchar</Tipo>
		</Campo>
		<Campo>
			<Nombre>NUMEDCTO</Nombre>
			<Titulo>Documento</Titulo>
			<Tipo>varchar</Tipo>
		</Campo>
		<Campo>
			<Nombre>DESCDCTO</Nombre>
			<Titulo>Descripción</Titulo>
			<Tipo>varchar</Tipo>
		</Campo>
	</Avanzada>
</Buscador>
'
GO
```
En nuestro **HTML** Creamos una Lista de tipo AppSearcherAddConditionParams y dentro creamos un Json, donde Name sera el nombre que le pusimos a nuestras variables en el buscador, IdTargetControl puede ir el valor estatico que se usara o se le da el ID del control donde buscara estos datos, TypeField aqui le daremos el tipo de Input que se va a usar para mandar los datos, IsRequired si el condicional es requerido se usa true si es opcional se usa false, UsePrefixed si usamos prefix se le da true en caso contrario false.
```cshtml
@{ 
	List<AppSearcherAddConditionParams> searcherAddParams = new List<AppSearcherAddConditionParams>()
	{
			new AppSearcherAddConditionParams
			{
				Name = "@param1",
				IdTargetControl = "#FuenteIShowParam",
				TypeField = AppSearcherTypeField.DevExpressInput,
				IsRequired = false,
				UsePrefixed = false
			},
			new AppSearcherAddConditionParams
			{
				Name = "@param2",
				IdTargetControl = "#FuenteFShowParam",
				TypeField = AppSearcherTypeField.DevExpressInput,
				IsRequired = false,
				UsePrefixed = false
			}
	};
}
```
En nuestro **HTML** dentro del buscador debajo del **funcCallBack** pegamos lo siguiente
```cshtml
addConditionParams=@searcherAddParams.GetStringAddCondition()
```
**Opcional** Por lo general estos buscadores en primera intancia cuando es dinamico dependiendo de otro buscador al no recibir datos siempre tienden a mostrar todos los valores para que esto funcione se debe crear unos valores por defecto en nuestro **HTML**, que por lo general el inicial es vacio y el final **zzzz**
```html
<input type="hidden" id="FuenteIShowParam" value="" />
<input type="hidden" id="FuenteFShowParam" value="zzzz" />
```
Luego solo tocaria en la funcion javascript que llena los datos que se encuentran en los buscadores darle valores cada vez que se seleccioné un registro.
```javascript
// Foraneo FuenteI
	function getDatosFuenteI(data) {
		var registro = data.data[0];

		if (data.data[0]) {
		        $("#FuenteIShow").val(registro.Codigo);
			$("#FuenteFShow").val(registro.Codigo);
			$("#FuenteIShowParam").val(registro.Codigo);
			$("#FuenteFShowParam").val(registro.Codigo);
		} else {
			$("#FuenteIShow").val("");
			$("#FuenteIShowParam").val("");
		}
	}
```
# 6.	Listas (Para datos de tipo cadenas separados por ,)

Hacemos todo igual que un Select, hasta que lleguemos a la parte del control **HTML** usamos este:
```cshtml
<fieldset class="scheduler-border">
					<legend class="scheduler-border">
						@(Html.DevExtreme().CheckBox()
						      .Value(false)
						      .Text("Seleccionar Todos")
						      .ID("listCheck")
						      .OnValueChanged("SeleccionaAll")
						)
					</legend>
					<input type="hidden" id="TipoDeDocumento" paramReport="TipoDeDocumento" />
					@(Html.DevExtreme().List()
							    .Height(300)
							    .ShowSelectionControls(true)
							    .SelectionMode(ListSelectionMode.Multiple)
							    .SelectAllMode(SelectAllMode.AllPages)
							    .SearchEnabled(true)
							    .SearchExpr(new[] { "Texto" })
							    .PageLoadMode(ListPageLoadMode.ScrollBottom)
							    .DataSource(Model.TipoDeDocumento, "Valor")
							    .ItemTemplate(@<text><%- Texto %></text>)
							    .OnSelectionChanged("TiposDeDocumentosJS")
							    .NextButtonText("Cargar Mas")
							    .ID("TipoDeDocumentoID"))
				</fieldset>
```
Luego Creamos la siguiente funcion JavaScript para recibir los datos y armar la cadena que vamos a enviar 
```javascript
function TiposDeDocumentosJS(data) {
		$("#TipoDeDocumento").val(data.component.option("selectedItemKeys").join(","));
	}

	function SeleccionaAll(data) {
         var listWidget1 = $("#TipoDeDocumentoID").dxList('instance');
            if (data.value == true) {
                listWidget1.selectAll();
		    listWidget1.option("disabled", true);
		    $("#TipoDeDocumento").val("");
            } else {
                listWidget1.unselectAll();
                listWidget1.option("disabled", false);
            }
	}
```
**Opcional** En la funcion **validateReport()** valida que la cadena no se vaya vacia 
```javascript
let = TiposDocumentos = $("#TiposDocumentos").val();

if (TiposDocumentos == "") {
			DevExpress.ui.notify("Seleccione al menos un Documento", "error", 2000);
			return false
		}
```
# 7.	Select que se filtra apartir de un buscador 

Dentro de nuesto **HTML** creamos un un **div** y le damos un **id**, en este div es donde se creara el select.
```cshtml
<div class="form-group">
								<div class="md-form">
									<input type="hidden" id="Oculto" value="0" />
									<div id="Serie"></div>
								</div>
							</div>
```
1. En la funcion que llama nuestro buscador al momento de obteber un dato, creamos nuestro **Select** con **Jquery** y **devexpress**, donde el **if** cuando existan datos validos se creara un **Json** que llamaremos **parametros** en el crearemos todos los parametros necesarios y le damos el valor que necesite en este caso el valor que traiga el buscador.

2. Usamos Jquery para seleccionar el **div** que creamos con el **id** que se le puso en este caso **Serie** creamos un control **dxSelectBox** donde **operation** sera el nombre que le daremos a nuestro **OP** en el SP: **spAPI_Combos**.
En **parametros** haremos que nuestro Json se convierta en un Json String ya que asi lo recibe la **Api** usamos el **JSON.stringify**

En caso de que el buscador no tenga valor o el valor no este exista en la tabla que el apunta, se creara un buscador sin datos normal por cuestiones esteticas.
```javascript
// Foraneo Fuente
	function getDatosFuente(data) {
		var registro = data.data[0];

		if (data.data[0]) {
			$("#FuenteShow").val(registro.Codigo);
			$("#Oculto").val("1");

			var parametros = {
				"fuente": registro.Codigo
			};
			
			$("#Serie").dxSelectBox({
				"name": "ReporteListadoDeAuditoriaDeConsecutivoContables.Serie",
				"value": "",
				"dataSource": {
					"store": DevExpress.data.AspNet.createStore({
						"loadParams": {
							"operation": "FUENTE_SERIE", "parametros": JSON.stringify(parametros)
						},
						"loadUrl": "/Combos/GetBySp"
					})
				},
				"showClearButton": false,
				"displayExpr": "Texto",
				"valueExpr": "Valor",
				"inputAttr": { "id": "ReporteListadoDeAuditoriaDeConsecutivoContables_Serie", "paramReport": "Serie" },
				"placeholder": "Elegir Serie",
				"disabled": false
			});
		} else {
			$("#FuenteShow").val("");
			$("#Oculto").val("0");

			$("#Serie").dxSelectBox({
				"name": "ReporteListadoDeAuditoriaDeConsecutivoContables.Serie",
				"value": "",
				"showClearButton": false,
				"placeholder": "Elegir Serie",
				"inputAttr": { "id": "ReporteListadoDeAuditoriaDeConsecutivoContables_Serie", "paramReport": "Serie" },
				"disabled": true
			});
		}
	}
```
3. En nuestra funcion **$(document).ready(function ()** iniciamos la funcion que llama nuestro buscador al momento de obteber un dato y le mandamos un datos vacio para que asi entre en el else y cree el select vacio por cuestiones esteticas
```javascript
getDatosFuente($(""));
```
4. En el SP: **spAPI_Combos**. creamos nuestro sentencia normal de un Select pero para que use parametros **adiccionales** debemos recibir un String y conventirlo en XML y sacamos nuestra variable para poder filtrar en este caso **@Fuente**
```sp
If @Op = 'FUENTE_SERIE'
	Begin
		Set @Sw_EntroOp = 1
		 
		Declare @vXML   XML
		Declare @Fuente Char(2)
		Declare @IdDoc  Int

		Set @vXML = @parametros

		Execute sp_xml_preparedocument @IdDoc OUTPUT, @vXML

		select @Fuente = fuente
		From OpenXml (@IdDoc, '/parametros')
		With (
				fuente varchar (50) 'fuente '
		)

		SELECT	Codigo = Cast('FUENTE_SERIE' As varchar(50)),
				Texto = Cast(NUMCONS.INICIO As Varchar(200)),
				Valor = Cast(NUMCONS.INICIO As Varchar(200))
		From	NUMCONS 
		WHERE	IDFUENTE = @Fuente
		Order By INICIO

	End
```
# 8.	Lista que crea un XML 

Hacemos todos los pasos igual que un **Select** hasta llegar al controlador html y hacemos lo siguiente.

Creamos **2 input ocultos**, uno para llenar la cadena y otro para enviar el **XML**, asiganamos una funcion **OnSelectionChanged**
```cshtml
<input type="hidden" id="Meses" paramReport="Meses" value="" />
<input type="hidden" id="Cadena" />
								@(Html.DevExtreme().List()
											    .Height(480)
											    .ShowSelectionControls(true)
											    .SelectionMode(ListSelectionMode.Multiple)
											    .DataSource(Model.Meses, "Valor")
											    .ItemTemplate(@<text><%- Texto %></text>)
											    .OnSelectionChanged("MesesJS")
								)
```
Iniciamos la funcion **MesesJS** para que guarde todos los campos que se seleccionen separador por,
```
function MesesJS(data) {
		$("#Cadena").val(data.component.option("selectedItemKeys").join(","));
	}
```
En la funcion **validateReport()** hacemos lo siguiente, traemos los valores que tenga la cadena en ese momento, si esta vacio envia nuestro valos Meses vacia en mi caso, si este no es tu caso haz la validacion correspondiente, si hay valor entonces se hace una funcion 
que recibe 2 parametros **cadenaADividir** y **separador**,
en la variable **arrayDeCadenas** unamos la funcion **split** que nos permite dividir una cadena cada vez que encuentre el valor que asisgnemos en este caso , creamos una variable , con la parte inicial de nuestro **XML** y luego en un **for** vamos agregando la demas ectrucutra, por ultimo asignamos esta **XML** en formato cadena a nuestro campo oculto para que asi se envie.
```
var cadenaMeses = $("#Cadena").val();

		if (cadenaMeses == "") {

			$("#Meses").val("");

		} else {

			function dividirCadena(cadenaADividir, separador) {

				var arrayDeCadenas = cadenaADividir.split(separador);

				var cadena;
				var cadenaFinal = "<tabla><Mes Numeric>";

				for (var i = 0; i < arrayDeCadenas.length; i++) {

					cadena = "<fila><&" + arrayDeCadenas[i] + "&>";
					//alert(cadena);
					cadenaFinal = cadenaFinal + cadena;

				}
				//alert(cadenaFinal);
				$("#Meses").val(cadenaFinal);
			}

			var coma = ",";

			dividirCadena(cadenaMeses, coma);
		}
```
# 9.	Obtener Valor de un Checkbox si esta chequeado o no, Obtener Valor de un RadioGroup
```js
let ArtCheck = $("#Articulo").dxCheckBox("instance").option("value");
let CampoAMostrardxRadio = $("#CampoAMostrardxRadio").dxRadioGroup("instance").option("value");
```
# 10.	Javascript Buscadores para manejar multiples buscadores desde una sola función
```js
// Foraneos
	function getDatosBuscador(data, codevalue, textboxF4) {

		var registro = data.data[0];
		var IdDelTextAsociado = textboxF4;

		if (data.data[0]) {
			$("#" + IdDelTextAsociado).val(registro.Codigo);
		} else {
			$("#" + IdDelTextAsociado).val("");
		}
	}
```
# 11.	Select que Bloquea se bloquea cuando se usa y activa un buscador 
```js
function Grupos(data) {

		let ID = data.element[0].id;
		let IDH = ID.replace("Select", "");
		let valorSelec = data.value;

		if (valorSelec == "ZONA") {

			if (!$("#ZonaIShow").prop("disabled")) {
				DevExpress.ui.notify("Zona ya fue seleccionada utilice otro", "warning", 2000);
			} else {
				$("#ZonaIShow").val("").prop("disabled", false).removeClass("readonly");
				$("#btnOpenSearcherZonaI").removeClass("disabled");
				$("#ZonaFShow").val("").prop("disabled", false).removeClass("readonly");
				$("#btnOpenSearcherZonaF").removeClass("disabled");

				$("#" + ID).dxSelectBox({ "disabled": true })
				$("#" + IDH).val(valorSelec)
			}

		} else if (valorSelec == "TERCERO") {

			if (!$("#TerceroIShow").prop("disabled")) {
				DevExpress.ui.notify("Tercero ya fue seleccionada utilice otro", "warning", 2000);
			} else {
				$("#TerceroIShow").val("").prop("disabled", false).removeClass("readonly");
				$("#btnOpenSearcherTerceroI").removeClass("disabled");
				$("#TerceroFShow").val("").prop("disabled", false).removeClass("readonly");
				$("#btnOpenSearcherTerceroF").removeClass("disabled");

				$("#" + ID).dxSelectBox({ "disabled": true })
				$("#" + IDH).val(valorSelec)
			}

		} else if (valorSelec == "CLIENTE") {

			if (!$("#ClienteIShow").prop("disabled")) {
				DevExpress.ui.notify("Cliente ya fue seleccionada utilice otro", "warning", 2000);
			} else {
				$("#ClienteIShow").val("").prop("disabled", false).removeClass("readonly");
				$("#btnOpenSearcherClienteI").removeClass("disabled");
				$("#ClienteFShow").val("").prop("disabled", false).removeClass("readonly");
				$("#btnOpenSearcherClienteF").removeClass("disabled");

				$("#" + ID).dxSelectBox({ "disabled": true })
				$("#" + IDH).val(valorSelec)
			}

		} else if (valorSelec == "VENDEDOR") {

			if (!$("#VendedorIShow").prop("disabled")) {
				DevExpress.ui.notify("Vendedor ya fue seleccionada utilice otro", "warning", 2000);
			} else {
				$("#VendedorIShow").val("").prop("disabled", false).removeClass("readonly");
				$("#btnOpenSearcherVendedorI").removeClass("disabled");
				$("#VendedorFShow").val("").prop("disabled", false).removeClass("readonly");
				$("#btnOpenSearcherVendedorF").removeClass("disabled");

				$("#" + ID).dxSelectBox({ "disabled": true })
				$("#" + IDH).val(valorSelec)
			}

		} else if (valorSelec == "TIPO CLIENTE") {

			if (!$("#TipoClienteIShow").prop("disabled")) {
				DevExpress.ui.notify("Tipo Cliente ya fue seleccionada utilice otro", "warning", 2000);
			} else {
				$("#TipoClienteIShow").val("").prop("disabled", false).removeClass("readonly");
				$("#btnOpenSearcherTipoClienteI").removeClass("disabled");
				$("#TipoClienteFShow").val("").prop("disabled", false).removeClass("readonly");
				$("#btnOpenSearcherTipoClienteF").removeClass("disabled");

				$("#" + ID).dxSelectBox({ "disabled": true })
				$("#" + IDH).val(valorSelec)
			}

		} else if (valorSelec == "SEGMENTO") {

			if (!$("#SegmentoIShow").prop("disabled")) {
				DevExpress.ui.notify("Segmento ya fue seleccionada utilice otro", "warning", 2000);
			} else {
				$("#SegmentoIShow").val("").prop("disabled", false).removeClass("readonly");
				$("#btnOpenSearcherSegmentoI").removeClass("disabled");
				$("#SegmentoFShow").val("").prop("disabled", false).removeClass("readonly");
				$("#btnOpenSearcherSegmentoF").removeClass("disabled");

				$("#" + ID).dxSelectBox({ "disabled": true })
				$("#" + IDH).val(valorSelec)
			}

		}
	}
```
# 12.	Validar que las fechas sean diferentes
```js
function FechaFChanged() {
		let inicial = $("#FechaI").dxDateBox("instance").option("text");
		let Final = $("#FechaF").dxDateBox("instance").option("text");

		if (Final < inicial) {
			DevExpress.ui.notify("La fecha final no puede ser menor que la inicial", "warning", 2000);
			$("#FechaF").dxDateBox("instance").option("value", inicial);
		}
	}
```
# 13.	Bloquear Fechas
```js
function VigenciaJS(data) {
		if (data.value == true) {
			$("#FechaI").dxDateBox({"disabled": false});
			$("#FechaF").dxDateBox({"disabled": false});
		} else {
			$("#FechaI").dxDateBox({"disabled": true});
			$("#FechaF").dxDateBox({"disabled": true});
		}
	}
```
# 14.	Parametros Globales
Si se necesita que los parametros globales sean precargados en nuestra vista, primero iniciamos nuestras variables en el controlador
```c#
DateTime FechaCorte;
DateTime FechaCorteInicial;
```

```c#
//le hacemos un get a ParametrosBusinessLogic
List<ParametroSimple> parametros = ParametrosBusinessLogic.GetParameters(httpContextAccessor, httpContextAccessor.GetToken(), config);

// variable que recibe el parametro
string sFechaCorte;

//le hacemos un get a parametros en la fecha de corte
sFechaCorte = parametros.Get("Fechacorte");

//lo parseamos a tipo fecha ya que se necesita en los controles devextreme
FechaCorte = DateTime.Parse(sFechaCorte);

//por funcionalidad en otra variable lo parseamos a el dia uno del mismo año y mes
FechaCorteInicial = new DateTime(FechaCorte.Year, FechaCorte.Month, 1);
```
# 15.	Buscadores Foraneos
```Js
// Foraneos Iniciales
	function getDatosBuscadorI(data, codevalue, textboxF4) {

		var registro = data.data[0];
		var IdDelTextAsociado = textboxF4;
		var IdDelTextAsociadoF = IdDelTextAsociado.replace("IShow", "FShow");
		if (data.data[0]) {
			if (registro.Codigo == undefined) {
				$("#" + IdDelTextAsociado).val(registro.codigo);
				$("#" + IdDelTextAsociadoF).val(registro.codigo);
			} else {
				$("#" + IdDelTextAsociado).val(registro.Codigo);
				$("#" + IdDelTextAsociadoF).val(registro.Codigo);
			}
		} else {
			$("#" + IdDelTextAsociado).val("");
		}
	}

	// Foraneos Finales
	function getDatosBuscadorF(data, codevalue, textboxF4) {

		var registro = data.data[0];
		var IdDelTextAsociado = textboxF4;

		if (data.data[0]) {
			if (registro.Codigo == undefined) {
				$("#" + IdDelTextAsociado).val(registro.codigo);
			} else {
				$("#" + IdDelTextAsociado).val(registro.Codigo);
			}
		} else {
			$("#" + IdDelTextAsociado).val("");
		}
	}
```
# 16.	Cargando que Bloquea la Pantalla
```js
$("#hefesto-block-loading").addClass("blockScreenGifLoading");
$("#container-zeus").addClass("blockScreenUnder");
```
