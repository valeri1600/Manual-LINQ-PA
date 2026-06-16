# Consultas con LINQ
Todos los ejemplos de esta sección se aplican al proyecto de simulación de dados utilizando **LINQ to SQL** en la **Capa de Datos**.

---

## Tipos de sintaxis en LINQ

En las consultas de LINQ existen dos tipos principales: la **sintaxis de consulta** (similar a SQL) y la **sintaxis de métodos**. La primera es similar a SQL y se utiliza con palabras clave como `from`, `where` y `select`.

### Sintaxis de consulta 
Esta sintaxis es muy parecida a SQL y utiliza palabras clave como `from`, `where` y `select`, lo que facilita su comprensión inicial.

```csharp
var resultado =
    from d in listaDados
    where d.puntaje > 0
    select d;
```
### Sintaxis de métodos (Lambda)
La sintaxis que utilizaremos en este proyecto es la sintaxis de métodos (lambda), ya que es más práctica, flexible y se usa con mayor frecuencia en aplicaciones reales.

```csharp
var resultado = listaDados
                .Where(d => d.puntaje > 0)
                .ToList();
```            

## Estructura base de una consulta LINQ

Antes de aplicar los operadores, es importante comprender la estructura general de una consulta LINQ en la capa de datos.

La estructura general es la siguiente:

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var resultado = contexto.Dados
                            .OperadorLINQ(...)
                            .ToList();
}
```

* **`DadosDataContext`**: representa la conexión con la base de datos y permite acceder a las tablas generadas por LINQ to SQL.
* **`contexto.Dados`**: corresponde a la tabla `Dados` sobre la cual se realizarán las consultas.
* **`OperadorLINQ`**: representa el operador que se desea utilizar, como `Where`, `Select`, `OrderBy`, entre otros.
* **`var resultado`**: la palabra clave `var` permite que C# determine automáticamente el tipo de dato de la variable según el resultado de la consulta.
* **`ToList()`**: ejecuta la consulta y convierte el resultado obtenido en una lista que puede ser utilizada posteriormente en la aplicación.

Esta estructura sirve como base para la mayoría de las consultas realizadas con LINQ.

## ¿Qué significa `=>` en las consultas LINQ?

Al comenzar a trabajar con LINQ, uno de los símbolos mas importante es `=>`. Este operador se conoce como **operador lambda** y se utiliza para indicar la condición o la acción que se aplicará sobre cada elemento de una colección.

Su estructura general es la siguiente:

```csharp
elemento => condición
```

Se puede leer como: **"Para cada elemento, realiza esta acción o verifica esta condición".**

Por ejemplo, en la siguiente consulta:

```csharp
contexto.Dados.Where(d => d.puntaje > 0)
```

la expresión:

```csharp
d => d.puntaje > 0
```

significa: **"Para cada dado (`d`), selecciona aquellos cuyo puntaje sea mayor que cero".**

Donde:

* **`d`** representa cada registro de la tabla `Dados`. Puedes pensar en él como una variable temporal.
* **`=>`** se lee como "tal que", "va hacia" o "para cada".
* **`d.puntaje > 0`** es la condición que debe cumplirse.

Otros ejemplos son:

**Obtener los lanzamientos con valores iguales:**

```csharp
.Where(d => d.valoresIguales)
```

Se interpreta como: "Para cada lanzamiento `d`, obtener aquellos donde `valoresIguales` sea verdadero."

**Obtener únicamente los puntajes:**

```csharp
.Select(d => d.puntaje)
```

Se interpreta como: "Para cada lanzamiento `d`, seleccionar su puntaje."

**Ordenar por suma:**

```csharp
.OrderByDescending(d => d.suma)
```

Se interpreta como:  "Para cada lanzamiento `d`, utilizar el campo `suma` para ordenar de mayor a menor."

El operador `=>` sirve para indicar qué se hará con cada elemento de la colección.

## Operaciones Basicas de Consulta

## ToList() — Convertir resultados en lista

### ¿Qué es?

Permite ejecutar una consulta LINQ y convertir el resultado en una lista (List), lo que facilita trabajar con los datos dentro de la aplicación.

### Ejemplo:

```csharp
var lista = contexto.Dados.ToList();
```
`ToList()` → ejecuta la consulta y convierte los registros en una lista de objetos (List "<Dados>").

## Where — Filtrar registros

**¿Qué es?**

Permite obtener únicamente los elementos que cumplen una condición determinada.

**Ejemplo:**

Obtener los lanzamientos donde ambos dados tuvieron el mismo valor.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var iguales = contexto.Dados
                           .Where(d => d.valoresIguales)
                           .ToList();
}
```

---

## Select — Proyectar (seleccionar campos específicos)

**¿Qué es?**

Permite seleccionar únicamente la información que se necesita de cada registro.

**Ejemplo:**

Obtener solamente los puntajes registrados.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var puntajes = contexto.Dados
                           .Select(d => d.puntaje)
                           .ToList();
}
```

---

## OrderBy y OrderByDescending — Ordenar

**¿Qué es?**

Permiten ordenar la información de forma ascendente o descendente.

**Ejemplo:**

Obtener los lanzamientos ordenados de mayor a menor puntaje.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var ordenados = contexto.Dados
                            .OrderByDescending(d => d.puntaje)
                            .ToList();
}
```

---

## FirstOrDefault — Obtener un solo registro

**¿Qué es?**

Devuelve el primer elemento que cumple una condición. Si no existe, retorna `null`.

**Ejemplo:**

Obtener el primer lanzamiento con puntaje de 5.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var lanzamiento = contexto.Dados
                              .FirstOrDefault(d => d.puntaje == 5);
}
```

> **Importante:** Antes de utilizar el resultado, se debe verificar que no sea `null`.

```csharp
if (lanzamiento == null)
{
    MessageBox.Show("No existen lanzamientos con puntaje 5.");
}
```

---

## Any — Verificar si existe algún registro

**¿Qué es?**

Permite comprobar si existe al menos un elemento que cumpla una condición.

**Ejemplo:**

Verificar si existe algún lanzamiento con dados iguales.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    bool existe = contexto.Dados
                           .Any(d => d.valoresIguales);
}
```

---

## Take — Obtener una cantidad específica de registros

**¿Qué es?**

Permite seleccionar un número determinado de elementos.

**Ejemplo:**

Obtener los cinco primeros lanzamientos registrados.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var primerosCinco = contexto.Dados
                                .Take(5)
                                .ToList();
}
```

---

## Skip — Omitir registros
¿Qué es?  
Permite saltar una cantidad de registros antes de devolver resultados.

**Ejemplo:**  
Omitir los primeros 5 lanzamientos y mostrar el resto.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var lanzamientos = contexto.Dados
                               .OrderBy(d => d.id)
                               .Skip(5)
                               .ToList();
}
```

---

## Distinct — Eliminar elementos repetidos

**¿Qué es?**

Permite obtener únicamente valores únicos, eliminando duplicados.

**Ejemplo:**

Obtener los puntajes diferentes obtenidos durante los lanzamientos.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var puntajesUnicos = contexto.Dados
                                 .Select(d => d.puntaje)
                                 .Distinct()
                                 .ToList();
}
```

---

## Combinando operadores

**¿Qué es?**

LINQ permite combinar varios operadores para obtener resultados más específicos.

**Ejemplo:**

Obtener los cinco lanzamientos con puntaje mayor a cero, ordenados de mayor a menor.

```csharp
using (DadosDataContext contexto = new DadosDataContext())
{
    var mejores = contexto.Dados
                           .Where(d => d.puntaje > 0)
                           .OrderByDescending(d => d.puntaje)
                           .Take(5)
                           .ToList();
}
```

---

