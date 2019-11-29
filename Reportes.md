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
