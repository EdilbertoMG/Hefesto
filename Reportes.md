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
