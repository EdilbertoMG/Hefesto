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
## Front
## Paso 1
Dentro de **Zeus.Inventario.Api** en la Carpeta **Controllers** de nuestra entidad, en la clase Put **Put**, agregamos.
```C#
objetoActual.View_Kit_Articulos = model.View_Kit_Articulos;
objetoActual.View_Kit_Conceptos = model.View_Kit_Conceptos;
```
Agregamos las varibales creadas en el primer paso y la igualamos con los valores que traera el modelo en el Put para que asi nos reciba los datos que enviaremos desde el Front en los pasos que vienen a continuacion.

## Paso 2
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
