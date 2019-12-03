# Reportes

_Alguno de los Controles mas usados en los reportes_

## Controles 🚀

# 1.	Select

Si tu **Select** es **estatico** abre el SP: **z2999999 DatosPorDefault_Hefesto_Combos** y crear uno nuevo, donde Codigo representa el codigo unico de de Select, Texto lo visual que se vera y valor sera lo que se envie cuando se seleccione un campo del select
```
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
```
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
```
public List<CombosModel> Ordenamiento { get; set; }
```
En tu **Controlador** incia el dato, pudes usar **FindByCode** ó **FindBySp** dependiendo si tu Select es estatico usa FindByCode y si es dinamico usa FindBySp
```
Ordenamiento = (List<CombosModel>)Manager().CombosBusinessLogic().FindByCode("AcumuladoDeVentasPorVendedor").ToModels(),
```
En tu **vista** crear el Control Select
```
@(Html.Inventario().SelectBox()
										.DataSource(Model.Ordenamiento)
										.DisplayExpr("Texto")
										.ValueExpr("Valor")
										.ElementAttr(new { paramReport = "Ordenamiento", dxCtrReport = "dxselect" })
										.Value("Codigo Vendedor")
										)
```
# 2.	RadioGroup

```
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
```
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
```
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
```
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
```c#
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
```
<input type="hidden" id="TiposDocumentos" paramReport="TiposDocumentos" />
						@(Html.DevExtreme().List()
									    .Height(120)
									    .ShowSelectionControls(true)
									    .SelectionMode(ListSelectionMode.All)
									    .SelectAllMode(SelectAllMode.AllPages)
									    .DataSource(Model.TiposDocumentos, "Valor")
									    .ItemTemplate(@<text><%- Texto %></text>)
									    .OnSelectionChanged("TiposDeDocumentosJS")
						)
```
Luego Creamos la siguiente funcion JavaScript para recibir los datos y armar la cadena que vamos a enviar 
```javascript
function TiposDeDocumentosJS(data) {
		$("#TiposDocumentos").val(data.component.option("selectedItemKeys").join(","));
	}
```
