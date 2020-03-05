# Grillas

_Forma de crear Grillas en Hefesto_

#1.	
Creamos nuestro Back Y Front con el mismo flujo de Siempre: las gruilla por lo general son tablas las cuales debebemos crearle nuestro Back y Fron normalmente.
#2. 
Dentro de Nuestro Back en la solucion **Zeus.Inventario.Infrastructure.Entities** creamos unas variables con la etiqueta [NotMapped] de las entidades ya creadas "Grillas", para este ejemplo son 2.
```c#
[NotMapped]
		public virtual List<View_Kit_Articulos> View_Kit_Articulos { get; set; }
		[NotMapped]
		public virtual List<View_Kit_Conceptos> View_Kit_Conceptos { get; set; }
```
