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
