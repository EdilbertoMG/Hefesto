# Grillas

_Forma de crear Grillas en Hefesto_

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


## Paso 4

Dentro de **Zeus.Inventario.BusinessLogic** en el **Custom** sobreescribimos el metodo **BuscarPorId** y dentro asociamos las Variables no mapeadas y mandamos a consultar los datos filtrando por nuestra variable a utilizar en este caso las tablas compartian el Iden, esta busqueda se hace en nuestro metodos creados en el paso anterior en cada entidad (Todo estos pasos son solamente para buscar).
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
