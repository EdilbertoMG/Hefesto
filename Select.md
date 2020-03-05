Pasos para crear **Select** dentro de los Maestros

## Paso 1
La parte de la creacion en los SpCombos es igual que en los Reporte e igual en el Front pero con las siguientes pequeñas Diferencias

Creamos una Vairable en el modelo igual que en los Reportes
```c#
public List<CombosModel> ListTipoConcepto { get; set; }
```
En nuestro **Controlador** como se trata de un maestro ascociamos igualmente nuestro combos en los Reportes pero con la diferencia que solo sera en algunos metodos
```c#
private KitModel EditModel(KitModel model)
		{
			ViewBag.PrefixKit = Prefix;
			string iduser = User.Claims.FirstOrDefault(x => x.Type.EndsWith("/nameidentifier")).Value;

			if (ModelState.IsValid)
			{
				try
				{
					if (model.EsNuevo)
					{
						model = Manager().KitBusinessLogic().Add(model, iduser).ToModel();
					}
					else
					{
						model = Manager().KitBusinessLogic().Edit(model, iduser).ToModel();
					}

					if (model.Errores != null && model.Errores.Any())
					{
						AddErrorToModel(model);
					}
					else if (model.EsNuevo)
					{
						model.EsNuevo = false;
					}
					model.ListTipoConcepto = (List<CombosModel>)Manager().CombosBusinessLogic().FindByCode("TipoConceptoKit").ToModels();
				}
				
				catch (Exception e)
				{
					while (e != null)
					{
						ModelState.AddModelError("Error: ", e.Message);
						e = e.InnerException;
					}
				}
			}

			return model;
		}
```
```c#
private KitModel EditModel(Decimal Iden)
		{
			ViewBag.PrefixKit = Prefix;
			KitModel model = Manager().KitBusinessLogic().FindById(Iden).ToModel();

			if (model == null)
			{
				model = GetNewModel();
				model.EsNuevo = true;
				model.Iden = Iden;
				return model;
			}


			model.ListTipoConcepto = (List<CombosModel>)Manager().CombosBusinessLogic().FindByCode("TipoConceptoKit").ToModels();

			model.EsNuevo = false;
			return model;
		}
```

```c#
private KitModel GetNewModel()
		{
			ViewBag.PrefixKit = Prefix;
			KitModel model = new KitModel();
			model.EsNuevo = true;
			model.ListTipoConcepto = (List<CombosModel>)Manager().CombosBusinessLogic().FindByCode("TipoConceptoKit").ToModels();
			return model;
		}
```

Cabe destacar que la parte que solo se agrega es la siguiente 
```c#
model.ListTipoConcepto = (List<CombosModel>)Manager().CombosBusinessLogic().FindByCode("TipoConceptoKit").ToModels()
```
