# LINQ 
## ¿ Qué es LINQ ?
<p align="justify">
LINQ (Language Integrated Query) es una tecnología desarrollada por Microsoft que forma parte del ecosistema .NET. Se trata de un conjunto de extensiones al lenguaje C#  que permite escribir consultas de manera directa dentro del código, utilizando una sintaxis similar a la de SQL, pero trabajando con datos de diferentes orígenes.
</p>
<p align="justify">
La idea principal de LINQ es facilitar la consulta y manipulación de datos utilizando una sola forma de escribir las consultas. Gracias a LINQ, se puede usar la misma sintaxis para trabajar con datos que provienen de listas, bases de datos, archivos XML, servicios web u otras fuentes, sin necesidad de aprender métodos diferentes para cada una.
</p>
<p align="justify">
Gracias a LINQ, el código se vuelve más legible, seguro  y productivo. Además, cuando se usa con bases de datos (especialmente Entity Framework), LINQ traduce automáticamente las consultas al lenguaje SQL correspondiente, optimizando el acceso a la información.
</p>

***Beneficios:***

* El código es más fácil de leer y escribir
* Con bases de datos (Entity Framework), LINQ se convierte automáticamente en SQL
* El manejo de datos es más natural, consistente y eficiente para los desarrolladores.

## ¿Por qué se utiliza LINQ en una Arquitectura por Capas?
<p align="justify">
En una arquitectura por capas, la regla principal es que cada parte del programa tenga una sola tarea. La capa de datos se encarga de conectarse a la base de datos y traer la información, pero es en la capa de negocio donde usamos LINQ. Se hace así porque LINQ nos permite filtrar, ordenar o agrupar esa información según lo que necesite la aplicación, manteniendo el código ordenado y separado de la base de datos.
</p>

## Diagrama del Modelo de 4 capas y LINQ
Cada capa cumple con una funcion en especifico:
* ***Capa de Presentación*** (La interfaz de usuario: la pantalla).
* ***Capa de Lógica de Negocio*** (Donde se procesan los datos).
* ***Capa de Datos*** (El puente con la base de datos).
* ***Capa de Entidad*** (Los moldes de los objetos y el idioma común que usan todas las capas para pasarse datos).
* ***Base de Datos*** (La base de datos real).

![Concepto de LINQ](./assets/imagenes/diagrama-4-capas.png)
