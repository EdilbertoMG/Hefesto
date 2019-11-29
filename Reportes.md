# Reportes

_Alguno de los Controles mas usados en los reportes_

## Controles 🚀

1.	**Select**

En tu **Modelo** crea un dato de tipo List con el nombre que recibira tu FRX
```
public List<CombosModel> Ordenamiento { get; set; }
```
En tu **Controlador** incia el dato, pudes usar **FindByCode** ó **FindBySp** dependiendo si tu Select es estatico o dinamico
```
Ordenamiento = (List<CombosModel>)Manager().CombosBusinessLogic().FindByCode("SpRptImpuestosAsumidos_Movimientos").ToModels(),
```
1.	**RadioGroup**

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
3.	**CheckBox**

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
