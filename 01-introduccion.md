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

* El código es más fácil de leer y escribir.
* Con bases de datos (Entity Framework), LINQ se convierte automáticamente en SQL.
* El manejo de datos es más natural, consistente y eficiente para los desarrolladores.

## ¿Por qué se utiliza LINQ en una Arquitectura por Capas?
<p align="justify">
LINQ se utiliza en una arquitectura por capas porque facilita la consulta y manipulación de datos de una manera clara y ordenada, respetando la responsabilidad de cada capa. Permite filtrar, ordenar, agrupar y transformar información con menos código, lo que mejora el mantenimiento y la reutilización del sistema, evitando mezclar la lógica del negocio con el acceso a los datos.

## Diagrama del Modelo de 4 capas y LINQ
Cada capa cumple con una funcion en especifico:
* ***Capa de Presentación*** (La interfaz de usuario: la pantalla).
* ***Capa de Lógica de Negocio*** (Donde se procesan los datos).
* ***Capa de Datos*** (Se usa para realizar consultas a la base de datos).
* ***Capa de Entidad*** (Los moldes de los objetos y el idioma común que usan todas las capas para pasarse datos).
* ***Base de Datos*** (La base de datos real).

<p align="center">
  <img src="./assets/imagenes/Base%20de%20datos.png" alt="Concepto de LINQ y Capas" width="300">
</p>

## Ventajas de usar LINQ en capas
* Hace el código más fácil de leer y mantener.
* Reduce la cantidad de código necesario para consultar datos.
* Utiliza la misma sintaxis para diferentes fuentes de datos.
* Disminuye la probabilidad de errores al manipular colecciones.
* Facilita la separación de responsabilidades entre las capas.

# Sintaxis de una consulta LINQ

La sintaxis de una consulta LINQ puede escribirse de dos formas principales: **sintaxis de consulta (similar a SQL)** y **sintaxis de métodos (lambda)**.

##  1. Sintaxis de consulta (Query Syntax)

Es la forma más parecida a SQL.

```csharp
var resultado =
    from d in listaDados
    where d.puntaje > 0
    select d;
```

 **Estructura básica:**

```csharp
from variable in origen
where condición
select resultado
```

---

## 2. Sintaxis de métodos (Method Syntax)

Es la más usada en proyectos reales.

```csharp
var resultado = listaDados
                .Where(d => d.puntaje > 0)
                .ToList();
```

---

## Operadores más usados

| Operador | Propósito | Ejemplo |
|----------|-----------|---------|
| `Where` | Filtrar | `.Where(x => x.Edad > 18)` |
| `Select` | Proyectar/transformar | `.Select(x => x.Nombre)` |
| `OrderBy` / `OrderByDescending` | Ordenar | `.OrderBy(x => x.Fecha)` |
| `FirstOrDefault` | Primer elemento o null | `.FirstOrDefault(x => x.Id == id)` |
| `ToList` | Ejecutar y materializar | `.ToList()` |
| `Count` | Contar elementos | `.Count(x => x.Activo)` |
| `Any` | Verificar existencia | `.Any(x => x.Nombre == "Ana")` |

---
