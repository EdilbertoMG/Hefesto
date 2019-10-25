# Pasos para Utilizar SmartCode

_Después de generar los archivos en el Smart code se deben copiar los archivos en este orden_

## Back End 🚀

1.	Abrir la solución **Zeus.Inventario.Back**

2.	Ir a la carpeta generada por Smart Code **Entities** y copiar el archivo en la raíz del proyecto **Zeus.Inventario.Infrastructure.Entities**

3.	Ir a la carpeta generada por Smart Code **Mappings** y copiar en el proyecto **Zeus.Inventario.Infrastructure** dentro de la carpeta **Mappings**

4.	Editar el archivo **Zeus.Inventario.Infrastructure\Factories\ZeusContextoDB.cs**, ir al final del archivo y asegurarse de crear las líneas de código correspondientes a la tabla en cuestión.
Declarar:
```
public DbSet<Colores> **Colores** { get; set; }
```
Y en el método **OnModelCreating** y agregar una nueva línea correspondiente a la tabla en cuestión:
```
modelBuilder.ApplyConfiguration(new ColoresMap());
```
